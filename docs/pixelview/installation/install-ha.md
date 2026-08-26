# PixelView High Availability (HA) Installation on Docker

This document describes a **production-grade High Availability (HA) deployment** of PixelView using Docker, where:

- MongoDB runs as a **3-node replica set on three separate machines**
- PixelView services run with **multiple replicas**
- MongoDB provides durable state
- PixelView services remain stateless and horizontally scalable

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
Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so, you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

!!! note "System Recommendation"

    8 vCPU, 16GB RAM, 1GB network
    : 3 x nodes

To achieve HA we need

- 3 node replica mongo db cluster
- Each service in the cluster running on all the nodes
- DNS (round robin) to the frontend on all 3 nodes to achieve DNS loadbalancer

First, on all 3 nodes edit your servers hosts file and put entries for nodes:

``` sh
$ cat /etc/hosts

127.0.0.1 localhost

192.168.5.1 mongo-node-1
192.168.5.2 mongo-node-2
192.168.5.3 mongo-node-3

```

NOTE: Make sure to match the name and IP of the server to your actual server.

## MongoDB Replica Set
Each MongoDB node runs **one MongoDB container**, all configured with the same replica set name.

### Assumptions
- All MongoDB nodes can reach each other over the network
- Hostnames or IPs:
    - `mongo-node-1`
    - `mongo-node-2`
    - `mongo-node-3`
- Port `27017` is open between MongoDB nodes

The MongoDB container takes the name from you `HOSTNAME` environment variable.
Set up the `HOSTNAME` variable on all your nodes before you start the containers.
``` sh
$ export HOSTNAME=$(hostname)
```

After setting up the hostname, verify the hostname with
```sh
$ echo $HOSTNAME
```

Then make sure to replace node names on `MONGODB_URI` environment variables on your docker-compose.yml file

## MongoDB Docker Compose
Create the following `docker-compose.yml` on **each node**.

```yaml
services:
  mongodb:
    image: mongo:8.3.8
    command: ["--replSet", "rs0", "--bind_ip_all"]
    container_name: ${HOSTNAME}
    hostname: ${HOSTNAME}
    restart: always
    # this version of mongo db has issue with linux kernel 6.19 and above
    # if it doesn't start or keeps restarting, uncomment this.
    # environment:
    #   GLIBC_TUNABLES: glibc.pthread.rseq=1
    volumes:
      - ./pixelview-data:/data/db
    network_mode: host

  rabbitmq:
    image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
    container_name: rabbitmq
    restart: always
    environment:
      RABBITMQ_PASSWORD: dcW41MPUlM54uw2
      RABBITMQ_USER: alertagility
    network_mode: host
  
  pixelview-frontend:
    image: ghcr.io/pixelvirt/pixelview-frontend:v0.0.1
    container_name: pixelview-frontend
    restart: always
    environment:
      ANSIBLE_BACKEND_URL: localhost:8000
      BACKEND_URL: localhost:9090
      BACKUP_BACKEND_URL: localhost:9191
      CHAT_BACKEND_URL: localhost:8765
      HOST_MANAGEMENT_BACKEND_URL: localhost:8080
      KUBERNETES_BACKEND_URL: localhost:4000
      OPENSTACK_BACKEND_URL: localhost:8005
      PIXELVIEW_URL: localhost:9090
    network_mode: host

  pixelview-backend:
    image: ghcr.io/pixelvirt/pixelview-backend:v0.0.1
    container_name: pixelview-backend
    restart: always
    environment:
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      DEX_ISSUER_URL: https://dex.pixelvirt.com/dex
      DEX_REDIRECT_URI: https://cloud.pixelvirt.com/api/auth/callback
      OPENSTACK_MIDDLEWARE_URL: http://localhost:8005
      MONGO_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
      DOMAIN: pixelvirt.com
      SUBDOMAIN: alertagility
      RABBITURL: amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility
      ENVIRONMENT: dev
      KAMAJI_KUBECONFIG: /root/kubeconfig.yaml
      SERVICE_IP:
    network_mode: host
    volumes:
      - ./kubeconfig.yaml:/root/kubeconfig.yaml

  pixelview-escalation:
    image: ghcr.io/pixelvirt/pixelview-escalation:v0.0.1
    container_name: pixelview-escalation
    restart: always
    environment:
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      MONGO_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
      RABBITURL: amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility
    network_mode: host

  pixelview-openstack:
    image: ghcr.io/pixelvirt/pixelview-openstack:v0.0.1
    container_name: pixelview-openstack
    restart: always
    environment:
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      GOPHER_CLOUD_DEBUG: true
      MONGO_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
      MONGO_DBNAME: dashboard_db
      PIXELVIEW_URL: http://localhost:9090
    extra_hosts:
      - "newcloud.pixelvirt.com:138.199.223.176"
    network_mode: host

  pixelview-kubernetes:
    image: ghcr.io/pixelvirt/pixelview-kubernetes:v0.0.1
    container_name: pixelview-kubernetes
    restart: always
    environment:
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      GET_CONFIG_FROM: bla
      KUBECONFIG_FILE: /usr/src/app/kubeconfig
      MONGO_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
      PIXELVIEW_URL: http://localhost:9090
    network_mode: host

  pixelview-ansible-server:
      image: ghcr.io/pixelvirt/pixelview-ansible-server:v0.0.1
      container_name: pixelview-ansible-server
      restart: always
      environment:
        AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
        JOB_INPUT_QUEUE: admin-admin
        JOB_OUTPUT_QUEUE: ansible_output
        MONGODB_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
        MONGODB_DBNAME: ansible_server_db
        PIXELVIEW_URL: http://localhost:9090
        RABBITMQ_HOST: localhost
        RABBITMQ_PORT: 5672
        RABBITMQ_USERNAME: alertagility
        RABBITMQ_PASSWORD: dcW41MPUlM54uw2
        RABBITMQ_VHOST: /
        SERVER_PORT: 8000
      network_mode: host
  
  pixelview-patch-management:
    image: ghcr.io/pixelvirt/pixelview-patch-management:v0.0.1
    container_name: pixelview-patch-management
    restart: always
    environment:
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      SERVER_PORT: 8080
      LOG_LEVEL: info
      PIXELVIEW_URL: http://localhost:9090
      STATIC_API_KEY: dev-key
      MONGODB_URI: mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/pixelview?replicaSet=rs0
      MONGODB_DBNAME: pixelview
      ANSIBLE_SERVER_DBNAME: ansible_server_db
      ANSIBLE_SERVER_URL: localhost:8000
      LEASE_TTL_SECONDS: 30
      HEARTBEAT_INTERVAL_SECONDS: 10
      CLAIM_POLL_INTERVAL_SECONDS: 5
      REAPER_INTERVAL_SECONDS: 60
      ANSIBLE_WATCH_INTERVAL_SECONDS: 5
      BLOCKED_REQUEUE_SECONDS: 15
      WORKER_COUNT: 4
      RECONCILE_INTERVAL_SECONDS: 30
      RECONCILE_STALE_AFTER_SECONDS: 120
    network_mode: host

  # pixelview-backup:
  #   image: ghcr.io/pixelvirt/backup:latest
  #   container_name: pixelview-backup
  #   restart: always
  #   environment:
  #     ALLOWED_ORIGINS: "*"
  #     AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
  #     DB_TYPE: mongodb
  #     MONGO_URI: mongodb://localhost:27017
  #     PIXELVIEW_URL: http://localhost:9090
  #     PORT: 9191
  #   network_mode: host

volumes:
  data:
```

### Add `SERVICE_IP`
Add the `SERVICE_IP` to the environment variable of `pixelview-backend` service. This is the ip of the host that your `pixelview-backend` container is running on.

### Start containers
Once you set up the hostname, you can start the conatainers
``` sh
$ docker compose up -d
```

## Initialize the MongoDB Replica Set
This step initializes the MongoDB replica set and must be executed **only once** after all MongoDB nodes are up and running.

> Run this from only one of the mongodb containers


### Connect to MongoDB
```bash
docker exec -it mongo-node-1 mongosh
```

### Initialize Replica Set
```mongodb
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo-node-1:27017" },
    { _id: 1, host: "mongo-node-2:27017" },
    { _id: 2, host: "mongo-node-3:27017" }
  ]
})
```

### Verify Replica Set Status
```mongodb
rs.status().members.map(m => ({host:m.name,state:m.stateStr,health:m.health}))
```

!!! Note
    mongo-node-1, mongo-node-2 and mongo-node-3 node hostname and adjust according to the node hostname for your setup.

!!! Info
    At this point for a clean install restart the services by running:
    ``` sh
        $ docker compose restart
    ```
If everything has gone correctly, you have 3 nodes with PixelView services running on each node.

## DNS / LB
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
username: admin@localhost
password: password
```
