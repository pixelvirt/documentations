# PixelView High Availability (HA) Installation on Docker

This document describes a **production-grade High Availability (HA) deployment** of PixelView using Docker, where:

- MongoDB runs as a **3-node replica set on three separate machines**
- PixelView services run with **multiple replicas**
- MongoDB provides durable state
- PixelView services remain stateless and horizontally scalable

---

## HA Architecture Overview

### Node Layout

| Node | Role |
| ----- | ------ |
| Node-1 | MongoDB (Primary / Secondary) |
| Node-2 | MongoDB (Primary / Secondary) |
| Node-3 | MongoDB (Primary / Secondary) |
| Node-1 | PixelView services (API, UI, workers) |

> **Important:** MongoDB nodes **must run on separate hosts** (VMs or physical machines). Running multiple MongoDB containers on the same host does **not** provide real HA.

### Host Requirements

Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

!!! note "System Recommendation"

    8 vCPU, 16GB RAM, 1GB network
    : 3 x nodes

To achieve HA we need to have

1. 3 node replica mongo db cluster
2. Each service in the cluster running on all the nodes
3. DNS (round robin) to the frontend on all 3 nodes to achieve DNS loadbalancer

First, on all 3 nodes edit your servers hosts file and put entries for nodes:

``` sh
$ cat /etc/hosts

127.0.0.1 localhost

192.168.5.1 mongo-node-1
192.168.5.2 mongo-node-2
192.168.5.3 mongo-node-3

```

NOTE: Make sure to match the name and IP of the server to your actual server.

Next, on all 3 nodes pull all the relevant docker images

``` sh
docker pull ghcr.io/pixelvirt/inithive-rabbitmq:latest
docker pull ghcr.io/pixelvirt/findescalation:latest
docker pull ghcr.io/pixelvirt/escalation:latest
docker pull mongo:5
docker pull rabbitmq:4.2.2-management
docker pull ghcr.io/pixelvirt/ansible-server:latest
docker pull ghcr.io/pixelvirt/ospc-openstack-go:latest
docker pull ghcr.io/pixelvirt/openstack-go:latest
docker pull ghcr.io/pixelvirt/alertagility:v2.0
docker pull ghcr.io/pixelvirt/alertagility-frontend:latest
```

---

## MongoDB Replica Set (3 Separate Nodes)

Each MongoDB node runs **one MongoDB container**, all configured with the same replica set name.

### Assumptions

- All MongoDB nodes can reach each other over the network
- Hostnames or IPs:
  - `mongo-node-1`
  - `mongo-node-2`
  - `mongo-node-3`
- Port `27017` is open between MongoDB nodes

---

### MongoDB Docker Compose (Run on *each* MongoDB node)

Create the following `docker-compose.yml` on **each node**.

> The file is identical on all three nodes.

```yaml
services:
  escalation:
    image: ghcr.io/pixelvirt/escalation:latest
    restart: always
    depends_on:
      - rabbitmqservice
      - mongoservice
    environment:
      MONGOURL: "mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/alertagility?replicaSet=rs0"
      RABBITURL: "amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility"
    network_mode: host

  alertagility:
    image: ghcr.io/pixelvirt/alertagility:v2.0
    restart: always
    ports:
      - "9090:9090"
    depends_on:
      - mongoservice
      - rabbitmqservice
    environment:
      DEX_ISSUER_URL: "https://dex.pixelvirt.com/dex"
      DEX_REDIRECT_URI: "https://cloud.pixelvirt.com/api/auth/callback"
      OPENSTACK_MIDDLEWARE_URL: "http://localhost:8005"
      MONGOURL: "mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/alertagility?replicaSet=rs0"
      DOMAIN: "pixelvirt.com"
      SUBDOMAIN: "alertagility"
      RABBITURL: "amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility"
      ENVIRONMENT: "dev"
      KAMAJI_KUBECONFIG: "/root/kubeconfig.yaml"
    network_mode: host
    volumes:
      - ./kubeconfig.yaml:/root/kubeconfig.yaml


  rabbitmqservice:
    image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
    restart: always
    ports:
      - "5673:5672"
    environment:
      - RABBITMQ_PASSWORD=dcW41MPUlM54uw2
      - RABBITMQ_USER=alertagility
    network_mode: host

  mongoservice:
    image: mongo:5
    command: ["--replSet", "rs0", "--bind_ip_all"]
    restart: always
    container_name: ${HOSTNAME}
    hostname: ${HOSTNAME}
    volumes:
      - ./pixelview-data:/data/db
    network_mode: host

  alert-frontend:
    image: ghcr.io/pixelvirt/alertagility-frontend:latest
    restart: always
    network_mode: host
    depends_on:
      - alertagility
      - ansible-server
      - openstack
    ports:
      - "80:80"
    environment:
      - BACKEND_URL=localhost:9090
      - ANSIBLE_BACKEND_URL=localhost:8000
      - OPENSTACK_BACKEND_URL=localhost:8005
      - KUBERNETES_BACKEND_URL=localhost:4000
      - CHAT_BACKEND_URL=localhost:8765
      - PIXELVIEW_URL=localhost:9090
      - BACKUP_BACKEND_URL=localhost:9191

  backup:
    image: ghcr.io/pixelvirt/backup:latest
    environment:
      PORT: "9191"
      DB_TYPE: mongodb
      MONGO_URI: mongodb://localhost:27017
      PIXELVIEW_URL: http://localhost:9090
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      ALLOWED_ORIGINS: "*"
    depends_on:
      - mongoservice
    restart: unless-stopped
    container_name: pixelvirt_backup
    network_mode: host

  openstack:
    image: ghcr.io/pixelvirt/openstack-go:latest
    restart: always
    ports:
      - "8005:8005"
    environment:
      - MONGO_URI="mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/alertagility?replicaSet=rs0"
      - MONGO_DBNAME=dashboard_db
      - GOPHER_CLOUD_DEBUG=true
      - PIXELVIEW_URL=http://localhost:9090
    container_name: openstack-go
    network_mode: host
    extra_hosts:
      - "newcloud.pixelvirt.com:138.199.223.176"
#    Include common cloud config. This will be visible to all users.
#    volumes:
#      - ./clouds.yaml:/etc/openstack/clouds.yaml

  kubernetes:
    restart: always
    image: ghcr.io/pixelvirt/kubernetes-go:latest
    ports:
      - "4000:4000"
    container_name: k8s-dashboard
    network_mode: host
    environment:
      - KUBECONFIG_FILE=/usr/src/app/kubeconfig
      - GET_CONFIG_FROM=bla
      - PIXELVIEW_URL=http://localhost:9090
#    Include common kube config. This will be visible to all users.
#    volumes:
#      - ./k8s-config/:/usr/src/app/

  ansible-server:
      image: ghcr.io/pixelvirt/ansible-server:latest
      build: .
      container_name: ansible-server
      ports:
          - "8000:8000"
      depends_on:
          - rabbitmqservice
      restart: unless-stopped
      network_mode: host
      environment:
      - SERVER_PORT=8000
      - AUTH_KEY=6c673f51-6045-47b0-8745-eef9d165a310
      - RABBITMQ_HOST=localhost
      - RABBITMQ_PORT=5672
      - RABBITMQ_USERNAME=alertagility
      - RABBITMQ_PASSWORD=dcW41MPUlM54uw2
      - RABBITMQ_VHOST=/
      - JOB_INPUT_QUEUE=admin-admin
      - JOB_OUTPUT_QUEUE=ansible_output
      - MONGODB_URI=mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/alertagility?replicaSet=rs0
      - MONGODB_DBNAME=ansible_server_db
      - PIXELVIEW_URL=http://localhost:9090
volumes:
  data:
  config:
  ansible-rabbitmq-data:
```

Start MongoDB:

``` sh
$ export HOSTNAME=$(hostname)
$docker compose up -d
```

### Initialize the MongoDB Replica Set (One-Time)

This step initializes the MongoDB replica set and must be executed **only once** after all three MongoDB nodes are running.

> Run this from **mongo-node-1** (or whichever node you want to become the initial primary).

---

#### Connect to MongoDB

```bash
docker exec -it mongo mongosh

rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo-node-1:27017" },
    { _id: 1, host: "mongo-node-2:27017" },
    { _id: 2, host: "mongo-node-3:27017" }
  ]
})
```

#### Verify Replica Set Status

```
rs.status()
```

---
!!! Note
    mongo-node-1, mongo-node-2 and mongo-node-3 node hostname and adjust according to the node hostname for your setup.

!!! Info
    At this point for a clean install restart the services by running:

    ``` sh
        $ docker compose restart
    ```

If everything has gone correctly, you have 3 nodes with PixelView services running on each node.


### DNS / LB
At this point you will need to setup a round robin DNS entry or external load balancer that points to your nodes.

Assuming DNS: testing.pixelvirt.com when you dig on the fqdn you see:

``` sh
dig testing.pixelvirt.com                                                                                                                        git:master*
testing.pixelvirt.com.	300	IN	A	50.56.158.227
testing.pixelvirt.com.	300	IN	A	50.56.157.101
testing.pixelvirt.com.	300	IN	A	174.143.59.131
```

Now, on your browser open https://your-domain-name.com and login with the default login:
```
admin@localhost

password
```
