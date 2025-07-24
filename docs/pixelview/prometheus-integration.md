# Prometheus Integration Documentation for PixelView


**Introduction:** <br>
Prometheus is an open-source monitoring and alerting toolkit widely used for collecting and querying time-series data. Integrating Prometheus into PixelView allows for efficient monitoring of various metrics and systems within the PixelView environment. This documentation outlines the steps required to deploy Prometheus integration on PixelView, including deployment files for Kubernetes resources, Docker deployment methods, and configuration details.


## Deployment Methods

1. Kubernetes [k8s-pixelview repository](https://github.com/pixelvirt/k8s-pixelview.git)
2. Docker [pixelview-docker repository](https://github.com/pixelvirt/pixelview-docker.git)


### **1. Kubernetes Deployment Files:**

**1.1. Deployment:** The deployment file specifies the configuration for deploying the Prometheus service on PixelView. Below is the deployment file (`prometheus-deployment.yaml`):
```yamal linenums="1"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-openstack
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus-openstack
  template:
    metadata:
      labels:
        app: prometheus-openstack
    spec:
      containers:
        - name: prometheus-openstack
          image: ghcr.io/pixelvirt/prom-go:latest
          ports:
            - containerPort: 4000
          env:
            - name: DATA_ADDR_URL
              valueFrom:
                configMapKeyRef:
                  name: prom-env
                  key: DATA_ADDR_URL
            - name: PROMETHEUS_HOST
              valueFrom:
                configMapKeyRef:
                  name: prom-env
                  key: PROMETHEUS_HOST
      restartPolicy: Always


```
**1.2. Service:** The service file defines the network service for accessing the Prometheus deployment. Below is the service file (`prometheus-service.yaml`):
``` yamal linenums="1"
apiVersion: v1
kind: Service
metadata:
  name: prometheus-openstack
spec:
  ports:
    - name: http
      port: 4000
      targetPort: 4000
  selector:
    app: prometheus-openstack
  externalIPs:
    - [REPLACE_WITH_EXTERNAL_IP]

```
**1.3. ConfigMap:** The ConfigMap contains sensitive data such as URLs and addresses required for the Prometheus deployment. Below is the ConfigMap file (`prometheus-configmap.yaml`):
```yamal linenums="1"
apiVersion: v1
kind: ConfigMap
metadata:
  name: prom-env
data:
  DATA_ADDR_URL: [REPLACE_WITH_DATA_ADDR_URL]
  PROMETHEUS_HOST: [REPLACE_WITH_PROMETHEUS_HOST]

```
### **2. Apply Kubernetes Deployment**

#### **2.1. Clone Repository (Alternative to Manually Creating Deployment Files):**

If you prefer not to create deployment files manually, you can clone the repository containing the Kubernetes manifests:


``` bash
git clone https://github.com/your-repo-path/k8s-pixelview 
cd k8s-pixelview 
kubectl apply -f prometheus-integration
```

#### **2.2. Replace Placeholder Values:**

Before deploying the Kubernetes resources, replace the placeholder values in the deployment files with the actual values specific to your environment. The placeholder values include:

- `[REPLACE_WITH_EXTERNAL_IP]`: External IP address for accessing the Prometheus service.
- `[REPLACE_WITH_DATA_ADDR_URL]`: Data address URL for Prometheus (URL where your application is running).
- `[REPLACE_WITH_PROMETHEUS_HOST]`: Prometheus Server host URL.

**2.3. Apply Deployment:** Apply the deployment files using the `kubectl apply` command:
```bash
kubectl apply -f prometheus-deployment.yaml
kubectl apply -f prometheus-service.yaml
kubectl apply -f prometheus-configmap.yaml

```
---
### **3. Docker Deployment**

For those who prefer deploying Prometheus using Docker, here are the deployment instructions:

#### **3.1. Create a `docker-compose.yml` File:**

Below is the Docker Compose file for deploying Prometheus on PixelView:
``` yaml
version: '3'
services:
  prometheus_openstack:
    restart: always
    image: ghcr.io/pixelvirt/prom-go:latest
    env_file:
      - .env
    ports:
      - "4000:4000"    
    container_name: prometheus_openstack

```



#### **3.2. Create an Environment Variable File:**

Create a file named `.env` with the following content:


``` bash
export PROMETHEUS_HOST=http://162.242.209.130:9091/  # Prometheus Server  host URL

```
#### **3.3. Run Prometheus with Docker Compose:**

Once the `docker-compose.yml` and `.env` files are ready, follow the steps below to start the Prometheus service.

1. Navigate to the directory where the `docker-compose.yml` file is located.
2. Run the following command to start Prometheus:

``` bash
docker compose up -d 

```
This command will download the Prometheus image (if not already available locally) and start the service.

#### **3.4. Clone Git Repository:**

Clone the repository containing the Docker manifests for Prometheus:


``` bash
git git@github.com:pixelvirt/pixelview-docker.git
cd pixelview-docker/prometheus-integration
```
#### **3.5. Adjust Environment Variable File:**

Modify the `.env` file to set the environment variable for `PROMETHEUS_HOST` as follows:

``` bash
export PROMETHEUS_HOST=http://162.242.209.130:9091/  # Prometheus host URL
```

---

### 4. Verify Metrics on the PixelView Dashboard:

To verify or check metrics on the PixelView dashboard after deployment, follow these steps to configure a new chart:

1. **Access Dashboard Edit Mode:** 
    - Navigate to your PixelView dashboard.
    - Click on "Edit Dashboard" to enter edit mode.
2. **Add New Chart:**
    - Locate the option to "Add New Chart" and click on it to create a new visualization.
3. **Add New Source:**
    - Look for an option to "Add New Source" and click on it to configure a new data source.
4. **Configure Data Source:**
    - Provide an appropriate name for the data source.
    - In the URL section, enter the endpoint URL: `http://<DATA_IP>:<DATA_PORT>/kubernetes/api/get-charts`.
        - Replace `<DATA_IP>` with the actual IP address where your application is running.
        - Replace `<DATA_PORT>` with the port number configured for your application.
5. **Save and Display Metrics:**
    - After configuring the data source, save your changes.
    - The dashboard should now fetch metrics from the specified endpoint and display them in the new chart.

---

#### **Conclusion:**

This documentation provides a detailed guide for deploying Prometheus integration on PixelView using both Kubernetes and Docker deployment methods. By following these steps, users can effectively monitor and analyze metrics within the PixelView environment using Prometheus.