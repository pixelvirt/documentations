# Installation
Like Docker? So do we!

One of the quickest ways to get pixelview running is using Docker. This page will show you the basics of how to use pixelview  with Docker.

## Host Requirements


Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

## Steps to Install


### 1. Copy Docker Compose File

Create a directory for your project and create a file named `docker-compose.yml`. Copy the provided service configurations into this file.

  
```yaml title="docker-compose.yaml" linenums="1"

version: "3.9"

services:
  escalation:
    image: ghcr.io/pixelvirt/escalation:latest
    restart: always
    depends_on:
      - rabbitmq
    extra_hosts:
      - "mongoservice:172.17.0.1"
      - "rabbitmqservice:172.17.0.1"
    environment:
      MONGOURL: "mongoservice:27018"
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

  finEscalation:
    image: ghcr.io/pixelvirt/findescalation:latest
    restart: always
    depends_on:
      - rabbitmq
    extra_hosts:
      - "mongoservice:172.17.0.1"
      - "rabbitmqservice:172.17.0.1"
    environment:
      MONGOURL: "mongoservice:27018"
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

  alertagility:
    image: ghcr.io/pixelvirt/alertagility:latest
    restart: always
    ports:
      - "9090:9090"
    depends_on:
      - rabbitmq
    extra_hosts:
      - "mongoservice:172.17.0.1"
      - "rabbitmqservice:172.17.0.1"
    environment:
      MONGOURL: "mongoservice:27018"
      DOMAIN: "demo.pixelvirt.com"
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

  rabbitmq:
    image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
    restart: always
    ports:
      - "5673:5672"
    environment:
      - RABBITMQ_PASSWORD=vcW41MPUlM54uw
      - RABBITMQ_USER=alertagility

  mongodb:
    image: mongo:5
    ports:
      - "27018:27017"
    volumes:
      - ./data:/data/db

  mongodb-utils:
    image: mongo:7.0
    container_name: mongodb-utils
    restart: always
    ports:
      - "27019:27017"
    volumes:
      - ./data-utils:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: Password123

  alert-frontend:
    image: ghcr.io/pixelvirt/alertagility-frontend:latest
    restart: always
    depends_on:
      - alertagility
    ports:
      - "80:80"
    environment:
      - BACKEND_URL=alertagility:9090

  openstack-go:
    image: ghcr.io/pixelvirt/openstack-go:latest
    restart: always
    ports:
      - "8005:8005"
    environment:
      - DATA_URL=https://example.com # MAKE SURE THIS IS THE URL YOUR INSTALLATION IS AVAILABLE ON
      - MONGO_URI=mongodb://dashboard:password123@mongodb-utils:27017/dashboard_db?authSource=dashboard_db
      - MONGO_DBNAME=dashboard_db
      - GOPHER_CLOUD_DEBUG=true
      - ALERTAGILITY_URL="http://alertagility:9090"
    volumes:
      - ./clouds.yaml:/etc/openstack/clouds.yaml

  k8s-dashboard:
    restart: always
    image: ghcr.io/pixelvirt/kubernetes-go:latest
    env_file:
      - .env
    ports:
      - "9091:9091"
    container_name: k8s-dashboard
    environment:
      - KUBECONFIG_FILE=/usr/src/app/kubeconfig
      - GET_CONFIG_FROM=out-of-cluster
    volumes:
      - ./k8s-config/:/usr/src/app/

```

#### 1.1 **Clone Git Repository (Alternative to Manually Creating manifests Files)::**

Clone the repository containing the Docker manifests:

```
git clone  https://github.com/pixelvirt/pixelview-docker.git
cd pixelview-docker/installation
```

<div style="border-left: 5px solid #0c2d7a; padding: 10px; border-radius: 5px;">
  <span style="font-size: 1.2em;">&#128161;</span> <strong>Note:</strong> Find DOMAIN and replace it with your actual domain name and adjust environments according to your system.
</div>

<div style="border-left: 5px solid #0c2d7a; padding: 10px; border-radius: 5px;">
  <span style="font-size: 1.2em;">&#128161;</span> <strong>Note:</strong> KUBECONFIG_FILE is picked up from ./k8s-config directory, make sure your kubeconfig is available there (this will be deprecated next release to use config fomr UI). Also, ensure that, DATA_URL, in openstack-go is the url you just installed pixelview on.
</div>

### 2. start the containers

Open a terminal window, navigate to the directory where the `docker-compose.yml` file is located, and run the following command:

```

docker compose up -d

```

This command will start all the services in detached mode, meaning they will run in the background.

### 3. Verify Installation

After running the `docker-compose up -d` command, you can verify that the services are running by executing:

  

```

docker compose ps

```

  

This command will display the status of all the services defined in the `docker-compose.yml` file.

### 4. Accessing the Application

Once the services are up and running, you can access the frontend application by opening a web browser and navigating to [`http://localhost`](http://localhost).

### 5. Stopping Services

To stop the running services, execute the following command:

  

```

docker compose down

```

  

This will stop and remove all containers defined in the `docker-compose.yml` file.

  

## Notes

  

- The default username to access the frontend is `admin@localhost`

- The default password is `password`

- The services are configured to automatically restart (`restart: always`) in case of failures or system reboots.

- The `depends_on` directive is used to define service dependencies, ensuring that dependent services are started before the dependent ones.

- Ensure that your system meets the resource requirements for running the services, especially for memory and CPU.

- You can customize environment variables and port mappings in the `docker-compose.yml` file as needed.

  

With these instructions, you should be able to successfully download and install the PixelView Services on your system. If you encounter any issues or have questions, refer to the Docker and Docker Compose documentation or feel free to ask for assistance.
