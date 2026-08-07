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
|-----|------|
| Node-1 | MongoDB (Primary / Secondary) |
| Node-2 | MongoDB (Primary / Secondary) |
| Node-3 | MongoDB (Primary / Secondary) |
| Node-1 | PixelView services (API, UI, workers) |

> **Important:** MongoDB nodes **must run on separate hosts** (VMs or physical machines). Running multiple MongoDB containers on the same host does **not** provide real HA.

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

Create the following `docker-compose.yml` on **each MongoDB node**.

> The file is identical on all three nodes.

```yaml
version: "3.9"

services:
  mongo:
    image: mongo:5
    container_name: mongo
    restart: always
    command: ["--replSet", "rs0", "--bind_ip_all"]
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

Start MongoDB:
```
docker compose up -d

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

Once the mongo replica set is set, on node1 (or any other node) use the following docker compose
```yaml
services:
  alertagility:
    image: ghcr.io/pixelvirt/alertagility:latest
    restart: always
    ports:
      - "9090:9090"
    depends_on:
      - mongoservice
      - rabbitmqservice
    environment:
      DEX_ISSUER_URL: "https://dex.pixelvirt.com/dex"
      DEX_REDIRECT_URI: "https://pixelvirt.com/api/auth/callback"
      OPENSTACK_MIDDLEWARE_URL: "http://openstack:8005"
      MONGOURL: "mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/?replicaSet=rs0"
      DOMAIN: "flex.ohthree.com"
      SUBDOMAIN: "alertagility"
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

  rabbitmqservice:
    image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
    restart: always
    ports:
      - "5673:5672"
    environment:
      - RABBITMQ_PASSWORD=vcW41MPUlM54uw
      - RABBITMQ_USER=alertagility

  alert-frontend:
    image: ghcr.io/pixelvirt/alertagility-frontend:latest
    restart: always
    depends_on:
      - alertagility
    ports:
      - "80:80"
    environment:
      - BACKEND_URL=alertagility:9090
      - OPENSTACK_BACKEND_URL=openstack:8005
      - KUBERNETES_BACKEND_URL=kubernetes:4000
      - CHAT_BACKEND_URL=chatbot:8765

  openstack:
    image: ghcr.io/pixelvirt/openstack-go:latest
    restart: always
    ports:
      - "8005:8005"
    environment:
      - MONGO_URI="mongodb://mongo-node-1:27017,mongo-node-2:27017,mongo-node-3:27017/?replicaSet=rs0"
      - MONGO_DBNAME=dashboard_db
      - GOPHER_CLOUD_DEBUG=true
      - PIXELVIEW_URL=http://alertagility:9090
#    Include common cloud config. This will be visible to all users.
#    volumes:
#      - ./clouds.yaml:/etc/openstack/clouds.yaml

  kubernetes:
    restart: always
    image: ghcr.io/pixelvirt/kubernetes-go:latest
    ports:
      - "4000:4000"
    container_name: k8s-dashboard
    environment:
      - KUBECONFIG_FILE=/usr/src/app/kubeconfig
      - GET_CONFIG_FROM=bla
      - PIXELVIEW_URL=http://alertagility:9090
#    Include common kube config. This will be visible to all users.
#    volumes:
#      - ./k8s-config/:/usr/src/app/

  chatbot:
    restart: always
    image: ghcr.io/pixelvirt/chatbot-dummy:latest
    ports:
      - "8765:8765"
    command: python3 app/main.py
```

### Multiple App Nodes
Run the same compose file on two or more application nodes, and place a load balancer (NGINX / HAProxy / cloud LB) in front of them.

