# Installation

## Prerequisites

Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

## Steps to Install
### 1. Copy  Docker Compose File
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
        MONGOURL: "mongoservice:27017"
        RABBITURL: "amqp://alertagility:[RABBIT_MQ_PASSWORD]@rabbitmqservice:5673/alertagility"

    finEscalation:
      image: ghcr.io/pixelvirt/findescalation:latest
      restart: always
      depends_on:
        - rabbitmq
      extra_hosts:
        - "mongoservice:172.17.0.1"
        - "rabbitmqservice:172.17.0.1"
      environment:
        MONGOURL: "mongoservice:27017"
        RABBITURL: "amqp://alertagility:[RABBIT_MQ_PASSWORD]@rabbitmqservice:5673/alertagility"

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
        MONGOURL: "mongoservice:27017"
        DOMAIN: "demo.yourdomain.com"
        RABBITURL: "amqp://alertagility:wq:[RABBIT_MQ_PASSWORD]@rabbitmqservice:5673/alertagility"

    rabbitmq:
      image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
      restart: always
      ports:
        - "5673:5672"
      environment:
        - RABBITMQ_PASSWORD=[RABBIT_MQ_PASSWORD]
        - RABBITMQ_USER=alertagility

    mongodb:
      image: mongo:5
      network_mode: "host"
      volumes:
        - ./data:/data/db

    alert-frontend:
      image: ghcr.io/pixelvirt/alertagility-frontend:v2
      restart: always
      depends_on:
        - alertagility
      ports:
        - "3000:80"
      environment:
        - DOLLAR=$$
        - ALERTAGILITY_API=alertagility:9090
        - ALERTAGILITY_REPORT=alertagility:9191
        - VAULT_URL=`vault_host:port`
  ```
Find `DOMAIN` and replace with  your actual domain name and adjust environments according to your  system  

    
### 2. Start Services
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
Once the services are up and running, you can access the frontend application by opening a web browser and navigating to [`http://localhost:3000`](http://localhost:3000).
    
### 5. Stopping Services
To stop the running services, execute the following command:

  ```
  docker compose down
  ```

This will stop and remove all containers defined in the `docker-compose.yml` file.
    

## Notes

- The default username to access the frontend is `admin@localhost`
-    The default password is `password`
- The services are configured to automatically restart (`restart: always`) in case of failures or system reboots.
- The `depends_on` directive is used to define service dependencies, ensuring that dependent services are started before the dependent ones.
- Ensure that your system meets the resource requirements for running the services, especially for memory and CPU.
- You can customize environment variables and port mappings in the `docker-compose.yml` file as needed.

With these instructions, you should be able to successfully download and install the PixelView Services on your system. If you encounter any issues or have questions, refer to the Docker and Docker Compose documentation or feel free to ask for assistance.