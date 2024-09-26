# OpenStack Integration into PixelView Environment

This documentation provides a step-by-step guide to integrate OpenStack monitoring into the PixelView environment using both Kubernetes and Docker deployment methods. Choose the appropriate method based on your infrastructure setup.

## Prerequisites

- **Kubernetes cluster**: Ensure you have a Kubernetes cluster running with proper access to namespaces and deployment capabilities.
- **Docker setup**: Ensure Docker is installed on the server for Docker-based deployments.
- **OpenStack**: Set up your OpenStack environment with the necessary credentials and configurations.
- **Access to manifest files** and **Docker Compose files**.

Both the Kubernetes manifests and Docker files can be found in the following GitHub repositories:

- **Kubernetes Repository**: [k8s-pixelview repository](https://github.com/pixelvirt/k8s-pixelview.git)
- **Docker Repository**: [pixelview-docker repository](https://github.com/pixelvirt/pixelview-docker.git)

---

## 1. Kubernetes Deployment

### Step 1: Create a Secret for OpenStack Credentials

Create a Kubernetes Secret to store sensitive environment variables such as the OpenStack authentication details. Save the following manifest as `openstack-secrets.yaml`:
``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: env-openstack-secret
  namespace: pixelview
type: Opaque
stringData:
  DATA_IP: "http://10.0.0.1" # Replace with the IP address of the host machine where the application is deployed
  DATA_PORT: "5000" # Replace with the port number on which the application is running
  # The domain name for the OpenStack project (set to 'Default')
  OS_PROJECT_DOMAIN_NAME: "Default"  # Found in OpenStack project configuration

  # The unique identifier for the OpenStack project
  OS_PROJECT_ID: "7c65161dd910483fac5e34ca53c5af07"  # Obtainable from the OpenStack dashboard

  # The domain name for the user in OpenStack (set to 'Default')
  OS_USER_DOMAIN_NAME: "Default"  # Found in OpenStack user configuration

  # The name of the OpenStack project
  OS_PROJECT_NAME: "admin"  # Available in the OpenStack dashboard under Projects

  # The tenant name associated with the OpenStack project (set to 'admin')
  OS_TENANT_NAME: "admin"  # Typically found in the OpenStack dashboard

  # The username for authenticating with OpenStack
  OS_USERNAME: "admin"  # Find in OpenStack user settings

  # The password associated with the OpenStack user account
  OS_PASSWORD: "VE67QUSy4G4oY2WqPL7zxy2VLL9eK98OrzE2ZZ5y"  # Set during user creation in OpenStack

  # The authentication URL for OpenStack
  OS_AUTH_URL: "http://10.0.0.16:5000"  # This is the Keystone service endpoint URL

  # The interface type for the OpenStack API (set to 'internal')
  OS_INTERFACE: "internal"  # Found in the OpenStack network configuration

  # The type of endpoint being used for the OpenStack API
  OS_ENDPOINT_TYPE: "internalURL"  # Obtainable from OpenStack service configurations

  # The API version of the OpenStack identity service
  OS_IDENTITY_API_VERSION: "3"  # Documentation available in OpenStack API references

  # The name of the OpenStack region (set to 'RegionOne')
  OS_REGION_NAME: "RegionOne"  # Configured in OpenStack deployment settings

  # The authentication plugin to use (set to 'password')
  OS_AUTH_PLUGIN: "password"  # Available in OpenStack authentication documentation

  # The name of the cloud environment (set to 'openstack')
  OS_CLOUD: "openstack"  # Typically defined in cloud configuration files

  # The unique identifier for the domain in OpenStack
  OS_DOMAIN_ID: "default"  # Found in OpenStack domain settings

```
Apply the secret to the `pixelview` namespace:
```
kubectl apply -f openstack-secret.yaml
```

Alternatively, you can clone the Kubernetes repository and apply the secret directly:
```
git clone https://github.com/pixelvirt/k8s-pixelview.git
cd k8s-pixelview/openstack-integration
kubectl apply -f openstack-secret.yaml
```

### Step 2: Deploy OpenStack Monitoring Application

Create a Kubernetes Deployment that pulls the OpenStack monitoring container and sets up the necessary environment variables from the previously created secret. Save the following manifest as `openstack_deployment.yml`:
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: openstack-monitoring
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: openstack-mon
  template:
    metadata:
      labels:
        app: openstack-mon
    spec:
      containers:
      - name: openstack-monitoring-pixelview
        image: ghcr.io/pixelvirt/openstack-go:latest
        ports:
        - containerPort: 5000
        env:
        - name: DATA_IP
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: DATA_IP
        - name: DATA_PORT
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: DATA_PORT
        - name: OS_PROJECT_DOMAIN_NAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_PROJECT_DOMAIN_NAME
        - name: OS_PROJECT_ID
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_PROJECT_ID
        - name: OS_USER_DOMAIN_NAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_USER_DOMAIN_NAME
        - name: OS_PROJECT_NAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_PROJECT_NAME
        - name: OS_TENANT_NAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_TENANT_NAME
        - name: OS_USERNAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_USERNAME
        - name: OS_PASSWORD
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_PASSWORD
        - name: OS_AUTH_URL
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_AUTH_URL
        - name: OS_INTERFACE
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_INTERFACE
        - name: OS_ENDPOINT_TYPE
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_ENDPOINT_TYPE
        - name: OS_IDENTITY_API_VERSION
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_IDENTITY_API_VERSION
        - name: OS_REGION_NAME
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_REGION_NAME
        - name: OS_AUTH_PLUGIN
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_AUTH_PLUGIN
        - name: OS_CLOUD
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_CLOUD
        - name: OS_DOMAIN_ID
          valueFrom:
            secretKeyRef:
              name: env-openstack-secret
              key: OS_DOMAIN_ID
```

Apply the deployment:
``` bash
kubectl apply -f openstack_deployment.yml

```
Alternatively, use the GitHub repo for the deployment:
```
cd k8s-pixelview/openstack-integration
kubectl apply -f openstack_deployment.yml
```
### Step 3: Expose the OpenStack Monitoring Service

To make the monitoring service externally accessible, create a service with the following manifest and save it as `openstack_service.yml`:
``` yaml 
apiVersion: v1
kind: Service
metadata:
  name: openstack-mon-service
  namespace: pixelview
spec:
  selector:
    app: openstack-mon
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
  externalIPs:
    - 10.0.0.1
```
Apply the service:
``` bash
kubectl apply -f openstack_service.yml

```
Alternatively, use the GitHub repo for the deployment:
```
cd k8s-pixelview/openstack-integration
kubectl apply -f openstack_service.yml

```
## 2. Docker Deployment

### Step 1: Create the `.env` File

Create the following `.env` file manually:
``` bash
DATA_IP="http://10.0.0.1"  # Replace with the IP address of the host machine where the application is deployed
DATA_PORT="4001"  # Replace with the port number on which the application is running

# The domain name for the OpenStack project (set to 'Default')
OS_PROJECT_DOMAIN_NAME="Default"  # Found in OpenStack project configuration

# The unique identifier for the OpenStack project
OS_PROJECT_ID="7c65161dd910483fac5e34ca53c5af07"  # Obtainable from the OpenStack dashboard

# The domain name for the user in OpenStack (set to 'Default')
OS_USER_DOMAIN_NAME="Default"  # Found in OpenStack user configuration

# The name of the OpenStack project
OS_PROJECT_NAME="admin"  # Available in the OpenStack dashboard under Projects

# The tenant name associated with the OpenStack project (set to 'admin')
OS_TENANT_NAME="admin"  # Typically found in the OpenStack dashboard

# The username for authenticating with OpenStack
OS_USERNAME="admin"  # Find in OpenStack user settings

# The password associated with the OpenStack user account
OS_PASSWORD="VE67QUSy4G4oY2WqPL7zxy2VLL9eK98OrzE2ZZ5y"  # Set during user creation in OpenStack

# The authentication URL for OpenStack
OS_AUTH_URL="http://10.18.1.216:5000"  # This is the Keystone service endpoint URL

# The interface type for the OpenStack API (set to 'internal')
OS_INTERFACE="internal"  # Found in the OpenStack network configuration

# The type of endpoint being used for the OpenStack API
OS_ENDPOINT_TYPE="internalURL"  # Obtainable from OpenStack service configurations

# The API version of the OpenStack identity service
OS_IDENTITY_API_VERSION="3"  # Documentation available in OpenStack API references

# The name of the OpenStack region (set to 'RegionOne')
OS_REGION_NAME="RegionOne"  # Configured in OpenStack deployment settings

# The authentication plugin to use (set to 'password')
OS_AUTH_PLUGIN="password"  # Available in OpenStack authentication documentation

# The name of the cloud environment (set to 'openstack')
OS_CLOUD="openstack"  # Typically defined in cloud configuration files

# The unique identifier for the domain in OpenStack
OS_DOMAIN_ID="default"  # Found in OpenStack domain settings

```
### Step 2: Create the Docker Compose File

Manually create the `docker-compose.yml` file:
``` yaml
version: '3'
services:
  openstack-monitoring:
    restart: always
    image: ghcr.io/pixelvirt/openstack-go:latest
    env_file:
      - .env
    ports:
      - "9092:9092"
    container_name: openstack-monitoring
```

Alternatively, clone the GitHub repository and use the provided Docker Compose file:
``` bash
git clone https://github.com/pixelvirt/pixelview-docker.git
cd pixelview-docker/openstack-integration
docker-compose up -d

```
### Step 3: Deploy the Docker Container

To deploy the container manually:
```
docker-compose up -d
```
Alternatively, use the GitHub repository:
```
cd pixelview-docker/openstack-integration
docker-compose up -d
```
---
## Steps to Verify Metrics on the PixelView Dashboard

### 4. Access Dashboard Edit Mode:

- **Navigate to PixelView Dashboard**: Go to the PixelView dashboard URL where the metrics are visualized.
- **Click on "Edit Dashboard"**: This will allow you to customize and add new charts to visualize the metrics.

#### 4.1. Add a New Chart:

- **Locate "Add New Chart" Button**: Click on the option to create a new chart or widget on the dashboard.

#### 4.2. Add a New Data Source:

- **Look for "Add New Source"**: You will need to add a data source to fetch metrics from the monitoring service.
- **Click on "Add New Source"**: This option allows you to link a new data source to the chart.

#### 4.3. Configure the Data Source:By following these steps, you can integrate OpenStack monitoring into the PixelView environment using either Kubernetes or Docker. You can use the manual method or the provided GitHub repositories for quicker setup.

- **Name the Data Source**: Provide a relevant name for your data source.
    
- **Set the Endpoint URL**: In the URL field, enter the following format:
    
``` bash
	http://<DATA_IP>:<DATA_PORT>/openstack/api/get-charts
```
- Replace the placeholder values:
    
- **<DATA_IP>**: Enter the actual IP address where your service is running.
    
- **<DATA_PORT>**: Use the port number configured for your application.
    

#### 4.4. Save and Display the Metrics:

- **Save the Data Source Configuration**: After entering the URL and configuring the data source, click on "Save" to apply the changes.
- **View the Chart**: The PixelView dashboard should now fetch the metrics from the monitoring service and display them in the new chart.
---
By following these steps, you can integrate OpenStack monitoring into the PixelView environment using either Kubernetes or Docker. You can use the manual method or the provided GitHub repositories for quicker setup.