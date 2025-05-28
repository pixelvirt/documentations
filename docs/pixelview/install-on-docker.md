# Installation
Like Docker? So do we!

One of the quickest ways to get pixelview running is using Docker. This page will show you the basics of how to use pixelview  with Docker.

## Host Requirements


Before proceeding with the installation, ensure that you have Docker and Docker Compose installed on your system. To do so you can follow the instructions provided on the [docker's website](https://docs.docker.com/get-docker/).

## Steps to Install


### 1. Copy Docker Compose File

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
      MONGOURL: mongoservice
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

  finEscalation:
    image: ghcr.io/pixelvirt/findescalation:latest
    restart: always
    depends_on:
      - mongoservice
      - rabbitmqservice
    environment:
      MONGOURL: mongoservice
      RABBITURL: "amqp://alertagility:vcW41MPUlM54uw@rabbitmqservice:5673/alertagility"

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
      MONGOURL: mongoservice
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

  mongoservice:
    image: mongo:5
    volumes:
      - ./pixelview-data:/data/db

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
      - MONGO_URI=mongodb://mongoservice
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

!!! tip "Setting nginx to proxy (with SSL) to chatbot"

    ```shell
       server {
    listen 443 ssl;
    server_name pixelview.pixelvirt.com;

    ssl_certificate /etc/letsencrypt/live/pixelview.pixelvirt.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/pixelview.pixelvirt.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        proxy_pass http://localhost:80;

        # WebSocket headers
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Standard proxy headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
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

The chatbot only work with https backend. As such we suggest  to use letsencrypt or similar to quickly setup
your installation with certificate. Once that is done, run nginx to proxy calls to the application backend.
You can use the nginx config snippet from above to get this done.
 

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
