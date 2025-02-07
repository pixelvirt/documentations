# OpenStack Integration into PixelView Environment

This documentation provides a step-by-step guide to integrate OpenStack monitoring into the PixelView environment using both Kubernetes and Docker deployment methods. Choose the appropriate method based on your infrastructure setup.


## Kubernetes Deployment
## Prerequisites

1. **Kubernetes Cluster**: Ensure you have access to a running Kubernetes cluster with `kubectl` configured.

2. **clouds.yaml File**: Verify that your `clouds.yaml` file is available locally. This file contains OpenStack configuration details.

3. **Base64 Encoding Tool**: Ensure you have `base64` installed for encoding the `clouds.yaml` file.

---

## Steps to Create and Use Secrets

### 1. Prepare the `clouds.yaml` File

Ensure the `clouds.yaml` file is complete and correct. It should be located in a directory accessible to your machine, e.g., `/etc/openstack/clouds.yaml`.

### 2. Encode the `clouds.yaml` File

Run the following command to encode the `clouds.yaml` file in base64 format:

```bash
echo "$(cat /etc/openstack/clouds.yaml | base64 -w 0)"
```

Copy the output of this command for use in the next step.

### 3. Create a Kubernetes Secret

Use the following YAML template to create a secret in your Kubernetes cluster. Replace `<base64_encoded_clouds.yaml>` with the base64-encoded content from the previous step.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: openstack-clouds
  namespace: default
  labels:
    app: openstack
    type: config
  annotations:
    description: "Secret containing OpenStack clouds.yaml configuration."
data:
  clouds.yaml: <base64_encoded_clouds.yaml>
```

Save this file as `openstack-secret.yaml` and apply it to your cluster:

```bash
kubectl apply -f openstack-secret.yaml
```

### 4. Create a Secret for Environment Variables

To centralize and manage configuration securely, create a secret with the necessary environment variables for both applications:

```yaml
# env-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: env-secret
  namespace: pixelview
type: Opaque
data:
  DATA_URL: aHR0cDovLzE5Mi4xNjguMTAxLjEwMTo1MDAw  # http://192.168.101.101:5000
  MONGO_URI: bW9uZ29kYjovL3Jvb3Q6cGFzc3dvcmRAbW9uZ29kYjoyNzAxNw==  # mongodb://root:password@mongodb:27017
  MONGO_DBNAME: b3BlbnN0YWNr  # openstack
  AUTH_URL: aHR0cDovLzE5Mi4xNjguMTAxLjEwMTozMDk5MC9hcGkvY2hlY2tfdXNlcl9pbmZv  # http://192.168.101.101:30990/api/check_user_info
  AUTH_KEY: NmM2NzNmNTEtNjA0NS00N2IwLTg3NDUtZWVmOWQxNjVhMzEw  # 6c673f51-6045-47b0-8745-eef9d165a310
```

Save this file as `env-secret.yaml` and apply it to your cluster:

```bash
kubectl apply -f env-secret.yaml
```

### 5. Deploy MongoDB and OpenStack Integration

#### Combined Deployment for MongoDB and OpenStack Integration

```yaml
# combined-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:latest
          ports:
            - containerPort: 27017
          env:
            - name: MONGOURL
              value: "mongodb:27017"
            - name: MONGO_INITDB_ROOT_USERNAME
              valueFrom:
                secretKeyRef:
                  name: mongodb-secret
                  key: mongo-root-username
            - name: MONGO_INITDB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mongodb-secret
                  key: mongo-root-password
            - name: MONGO_INITDB_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mongodb-secret
                  key: mongo-database
          volumeMounts:
            - name: mongodb-data
              mountPath: /data/db
      volumes:
        - name: mongodb-data
          persistentVolumeClaim:
            claimName: mongodb-pvc  # Ensure you have created this PVC before deploying

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openstack-integration
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: openstack-integration
  template:
    metadata:
      labels:
        app: openstack-integration
    spec:
      containers:
      - name: openstack-integration
        image: ghcr.io/pixelvirt/openstack-go:openstack-test
        imagePullPolicy: Always 
        ports:
        - containerPort: 8005
        envFrom:  # Use envFrom to load all key-value pairs from the secret
        - secretRef:
            name: env-secret  # Reference the env-secret
        volumeMounts:
        - name: openstack-config
          mountPath: /etc/openstack
          readOnly: true
      volumes:
      - name: openstack-config
        secret:
          secretName: clouds-secret
          items:
          - key: clouds.yaml
            path: clouds.yaml
```
<div style="border-left: 5px solid #0c2d7a; padding: 10px; border-radius: 5px;">
  <span style="font-size: 1.2em;">&#128161;</span> <strong>Note:</strong> 
 If your cluster does not have Persistent Volumes (PV) and Persistent Volume Claims (PVC), you need to create them first. If you already have them, you can specify your existing PV configuration.
   ></div>

**Note:** If your cluster does not have Persistent Volumes (PV) and Persistent Volume Claims (PVC), you need to create them first. If you already have them, you can specify your existing PV configuration.

### 6. Downloading and Modifying Manifests from GitHub

To download the manifests from a GitHub repository, follow these steps:

```bash
git clone https://github.com/your-repository/k8s-manifests.git
cd k8s-manifests
```

Modify the necessary environment variables in `env-secret.yaml`:

```bash
nano env-secret.yaml
```

After modifying the file, apply the manifests:

```bash
kubectl apply -f env-secret.yaml
kubectl apply -f combined-deployment.yaml
kubectl apply -f combined-services.yaml
```

## Docker Deployment

## Prerequisites

1. **Docker and Docker Compose**:
   Ensure Docker and Docker Compose are installed and running on the host system.
   
2. **OpenStack Configuration**:
   Verify that OpenStack is configured correctly, and the `clouds.yaml` file exists under `/etc/openstack/clouds.yaml` on the host system.

3. **Environment Variables**:
   Prepare the necessary environment variables as described below.

---

## Docker Compose Configuration

### Docker Compose File
The provided `docker-compose.yml` file includes two services:

- **openstack-integration**: Integrates OpenStack with PixelView.
- **mongodb**:A MongoDB database for storing custom OpenStack quota details.

```yaml
version: '3'
services:
  openstack-integration:
    restart: always
    image: ghcr.io/pixelvirt/openstack-go:openstack-test
    env_file:
      - .env
    ports:
      - "5000:8005"
    container_name: openstack-integrations
    volumes:
      - /etc/openstack/clouds.yaml:/etc/openstack/clouds.yaml

  mongodb:
    image: mongo:latest
    container_name: mongodb
    volumes:
      - ./data:/data/db
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: password
      MONGO_INITDB_DATABASE: openstack
```

### Environment Variables File (.env)
Create a `.env` file in the same directory as your `docker-compose.yml` file. Populate it with the following environment variables:

```env
DATA_URL="http://10.0.0.1:5000"
MONGO_URI="mongodb://root:password@mongodb:27017"
MONGO_DBNAME="openstack"
AUTH_URL="http://10.0.0.1:9090/api/check_user_info"
AUTH_KEY="6c673f51-6045-47b0-8745-eef9d165a310"
```

- **DATA_URL**: The URL where the OpenStack integration service will be accessible.
- **MONGO_URI**: Connection URI for MongoDB, including username, password, and database.
- **MONGO_DBNAME**: Name of the database used for monitoring.
- **AUTH_URL**: URL of the API used for authentication.
- **AUTH_KEY**: Authentication key used to validate API access.

---

## Steps to Set Up and Run

### 1. Clone or Prepare the Files
Ensure the `docker-compose.yml` file and `.env` file are present in the working directory.

### 2. Mount the OpenStack Configuration File
Ensure the `clouds.yaml` file is available at `/etc/openstack/clouds.yaml` on the host machine.

### 3. Start the Services
Run the following command to start the services:

```bash
docker-compose up -d
```

This will:
- Start the OpenStack integration service on port 5000.
- Start a MongoDB instance for storing monitoring data.

### 4. Verify the Services
- Check if the containers are running:

  ```bash
  docker ps
  ```

- Access the OpenStack integration service at `http://<DATA_URL>/openstack/api/get-charts`.

---

## Monitoring OpenStack on PixelView

2. **Authenticate API Requests**:
   - Ensure that the `AUTH_URL` and `AUTH_KEY` are properly set up for authentication.

3. **Check Data in MongoDB**:
   - Access the MongoDB container:
     ```bash
     docker exec -it mongodb mongo -u root -p password
     ```
   - Switch to the `openstack` database:
     ```
     use openstack
     ```
   - Run queries to verify monitoring data.

4. **View Logs**:
   - To debug or monitor logs from the OpenStack integration service, use:
     ```bash
     docker logs -f openstack-integrations
     ```

5. **PixelView Monitoring**:
   - Navigate to the PixelView dashboard.
   - To view OpenStack metrics, follow these steps:
     1. Click on **ADD New Charts**.
     2. Select **Add Source**.
     3. Provide an appropriate name and insert the following URL:
        ```
        <DATA_URL>/openstack/api/get-charts
        ```
     4. Click on **Submit**.
     5. After submission, locate the **Regions** option in the chart list and select it.
     6. Click on **Submit** again.
     7. Once completed, you will see your OpenStack clouds displayed.

   - Click on any OpenStack cloud to view detailed information, including:
     - OpenStack hypervisors
     - Instance networks
     - Volumes
     - OpenStack projects
     - Quota classes

---

## Common Issues and Troubleshooting

### 1. **Containers Failing to Start**
- Check the logs:
  ```bash
  docker logs <container_name>
  ```
- Verify the `.env` file for correct values.

### 2. **Connection Issues with MongoDB**
- Ensure the `MONGO_URI` in the `.env` file matches the MongoDB container settings.
- Verify MongoDB container status with:
  ```bash
  docker ps
  ```

### 3. **Authentication Errors**
- Double-check the `AUTH_URL` and `AUTH_KEY` values in the `.env` file.

---

## Maintenance

1. **Backup MongoDB Data**:
   Regularly back up the `./data` directory used by MongoDB.

2. **Update Images**:
   To update the Docker images, run:
   ```bash
   docker-compose pull
   docker-compose up -d
   ```

3. **Monitor Logs**:
   Regularly check logs for errors or unusual activity.

---
By following these steps, you can integrate OpenStack monitoring into the PixelView environment using either Kubernetes or Docker. You can use the manual method or the provided GitHub repositories for quicker setup.