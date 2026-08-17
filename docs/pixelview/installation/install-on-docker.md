# Install Pixelview (AIO Setup)

In non HA setup, we setup all components of the PixelView app and automation services on one sigal server as All In One (AIO) setup. We use docker compose to setup the components and run all components in docker containers.

!!! note "Host Requirements"
    Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so, you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

!!! note "System Recommendation"

    8 vCPU, 16GB RAM, 1GB network

## Steps to Install

### Copy Docker Compose File
Create a directory for your project and create a file named `docker-compose.yml`. Copy the provided service configurations into this file.
  
```yaml title="docker-compose.yaml" linenums="1"
services:
  escalation:
    image: ghcr.io/pixelvirt/escalation:latest
    restart: always
    depends_on:
      - rabbitmqservice
      - mongoservice
    environment:
      MONGOURL: "mongodb://localhost:27017"
      RABBITURL: "amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility"
    network_mode: host

  pixelview:
    image: ghcr.io/pixelvirt/alertagility:test
    restart: always
    ports:
      - "9090:9090"
    depends_on:
      - mongoservice
      - rabbitmqservice
    environment:
      DEX_ISSUER_URL: "https://dex.cloud.pixelvirt.com/dex"
      DEX_REDIRECT_URI: "https://cloud.pixelvirt.com/api/auth/callback"
      OPENSTACK_MIDDLEWARE_URL: "http://openstack:8005"
      MONGOURL: "mongodb://localhost:27017/alertagility"
      DOMAIN: "cloud.pixelvirt.com"
      SUBDOMAIN: "alertagility"
      RABBITURL: "amqp://alertagility:dcW41MPUlM54uw2@localhost:5672/alertagility"
      ENVIRONMENT: "dev"
      KAMAJI_KUBECONFIG: "/root/kubeconfig.yaml"
    network_mode: host
    extra_hosts:
      - "test.pixelvirt.com:49.12.40.20"
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
    volumes:
      - ./pixelview-data:/data/db
    network_mode: host

  pixelview-frontend:
    #image: ghcr.io/pixelvirt/alertagility-frontend:capi
    image: ghcr.io/pixelvirt/alertagility-frontend:latest
    restart: always
    depends_on:
      - pixelview
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
      - HOST_MANAGEMENT_BACKEND_URL=localhost:8080
    network_mode: host

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
      - MONGO_URI=mongodb://localhost:27017
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
      - MONGO_URI=mongodb://localhost:27017
      - AUTH_KEY=6c673f51-6045-47b0-8745-eef9d165a310
    extra_hosts:
      - "kubernetes.default.svc.cluster.local:119.9.94.8"
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
      - MONGODB_URI=mongodb://localhost:27017/ansible_server_db
      - MONGODB_DBNAME=ansible_server_db
      - PIXELVIEW_URL=http://localhost:9090
      network_mode: host

volumes:
  data:
  config:
  ansible-mongo-data:
  ansible-rabbitmq-data:
```


### Start the containers
Open a terminal window, navigate to the directory where the `docker-compose.yml` file is located, and run the following command:
```
docker compose up -d
```
This command will start all the services in detached mode, meaning they will run in the background.

### Verify Installation
After running the `docker-compose up -d` command, you can verify that the services are running by executing:

```
docker compose ps
```
This command will display the status of all the services defined in the `docker-compose.yml` file.

### Accessing the Application
Once the services are up and running, you can access the frontend application by opening a web browser and navigating to [`http://localhost`](http://localhost).

!!! note "Credentials"

    - The default username to access the frontend is `admin@localhost`
    - The default password is `password`

### Stopping Services
To stop the running services, execute the following command:
```
docker compose down
```
This will stop and remove all containers defined in the `docker-compose.yml` file.

## Add licence key
Apart from username and password, you will also need to add a license key to be able to access the PixelView interface. Once you receive license, pelase follow these instructions.

  - Goto `Settings` -> `License key`

    <a href="../images/license1.jpg" class="glightbox">
      <img src="../images/license1.jpg">
    </a>
    
  - Paste your license key

    <a href="../images/license2.jpg" class="glightbox">
    <img src="../images/license2.jpg">
  </a>

  - Hit `Apply License`

## Notes
- The services are configured to automatically restart (`restart: always`) in case of failures or system reboots.
- The `depends_on` directive is used to define service dependencies, ensuring that dependent services are started before the dependent ones.
- Ensure that your system meets the resource requirements for running the services, especially for memory and CPU.
- You can customize environment variables and port mappings in the `docker-compose.yml` file as needed.

