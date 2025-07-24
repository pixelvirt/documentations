### Overview

This guide outlines how to integrate Kubernetes with the PixelView application, allowing it to utilize Kubernetes resources effectively. You will learn how to deploy the application using Kubernetes with appropriate configurations.

## Deployment Methods
1. Kubernetes [K8s-manifest-files](https://github.com/pixelvirt/k8s-pixelview.git).
2. Docker [DockerFile](https://github.com/pixelvirt/pixelview-docker.git).

### 1. Deploy on Kubernetes
### Prerequisites

1. **Kubernetes Cluster**: Ensure you have a Kubernetes cluster running and `kubectl` configured to connect to it.
2. **Service Account**: Create a service account named `pixelview-sa` in the `pixelview` namespace with appropriate permissions.
### Configuration Files

You will use the following YAML files:

- `configmap.yaml`: Defines configuration parameters for the application.
- `deployment.yaml`: Deploys the application as a Kubernetes Deployment.
- `service.yaml`: Exposes the application externally using a LoadBalancer type Service.
- `clusterrole.yaml`: Defines a ClusterRole with permissions to access various Kubernetes resources.
- `clusterrolebinding.yaml`: Binds the ClusterRole to the `pixelview-sa` service account.
### Step-by-Step Setup
#### 1.1 Create a ConfigMap

Create a ConfigMap named `app-config` in the `pixelview` namespace using the following `configmap.yaml`:
``` yaml linenums="1"
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: pixelview
data:
  GET_CONFIG_FROM: in-cluster
  KUBECONFIG_FILE: /usr/src/app/kube-config
  DATA_IP: <DATA_IP>  # Replace with the IP address of the host machine where the application is deployed
  DATA_PORT: <DATA_PORT>  # Replace with the port number on which the application is running

```
Apply the ConfigMap:
``` bash
kubectl apply -f configmap.yaml
```
#### 1.2 Create Service Account
To incorporate the creation of a service account (`pixelview-sa`) into your Kubernetes setup, follow these steps. This service account will be used by your application deployment (`k8s-info-deployment`) within the `pixelview` namespace.

Create a YAML file (`serviceaccount.yaml`) with the following content:
``` yaml linenums="1"
apiVersion: v1 
kind: ServiceAccount
metadata:
  name: pixelview-sa
  namespace: pixelview

```
Apply the ServiceAccount to create it in your Kubernetes cluster :
``` bash
kubectl apply -f serviceaccount.yaml
```
#### 1.3 Deploy Application

Deploy your application using a Deployment named `k8s-info-deployment` in the `pixelview` namespace with the following `deployment.yaml`:

``` yaml linenums="1"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: k8s-monitoring
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: k8s-info
  template:
    metadata:
      labels:
        app: k8s-info
    spec:
      serviceAccountName: pixelview-sa
      containers:
        - name: k8s-monitoring-pod
          image: ghcr.io/pixelvirt/kubernetes-go:latest
          ports:
            - containerPort: 9090 
          env:
            - name: GET_CONFIG_FROM
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: GET_CONFIG_FROM
            - name: KUBECONFIG_FILE
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: KUBECONFIG_FILE
            - name: DATA_IP
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: DATA_IP
            - name: DATA_PORT
              valueFrom:
                configMapKeyRef:
                  name: app-config
                  key: DATA_PORT
```
Deploy the application:
``` bash
kubectl apply -f deployment.yaml
```

#### 1.4 Expose Application

Expose your application externally using a LoadBalancer type Service named `k8s-info-service` in the `pixelview` namespace with the following `service.yaml`:
``` yaml linenums="1"
apiVersion: v1 
kind: Service
metadata:
  name: k8s-monitoring-service
  namespace: pixelview
spec:
  selector:
    app: k8s-info
  ports:
    - protocol: TCP
      port: 9091
      targetPort: 9091
  externalIPs:
    - <EXTERNAL_IP> # Replace with your desired external IP
```

#### 1.5 Configure RBAC

Ensure your `pixelview-sa` service account has the necessary permissions using RBAC. Use the following `clusterrole.yaml` to define a ClusterRole:
``` yaml linenums="1"
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: full-access
rules:
  - apiGroups: [""]
    resources: ["pods", "services", "endpoints", "nodes", "namespaces", "ingresses", "events"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["apps"]
    resources: ["deployments", "daemonsets", "replicasets", "statefulsets"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["batch"]
    resources: ["jobs", "cronjobs"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["rbac.authorization.k8s.io"]
    resources: ["roles", "rolebindings", "clusterroles", "clusterrolebindings"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["autoscaling"]
    resources: ["horizontalpodautoscalers"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["policy"]
    resources: ["poddisruptionbudgets"]
    verbs: ["list", "get", "watch"]
  - apiGroups: ["extensions", "networking.k8s.io"]
    resources: ["ingresses"]
    verbs: ["list", "get", "watch"]

```
Apply the ClusterRole:
``` sh
kubectl apply -f clusterrole.yaml

```
Bind the ClusterRole to `pixelview-sa` with `clusterrolebinding.yaml`:
``` yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: full-access-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: full-access
subjects:
  - kind: ServiceAccount
    name: pixelview-sa
    namespace: pixelview
```
Apply the ClusterRoleBinding:
``` sh
kubectl apply -f clusterrolebinding.yaml

```

#### 1.6 **Clone Repository (Alternative to Manually Creating Deployment Files):**

If you prefer not to create deployment files manually, you can clone the repository containing the Kubernetes manifests:
``` bash 
git clone https://github.com/your-repo-path/k8s-pixelview 
cd k8s-pixelview
kubectl apply -f k8s-integration
```

##  Verify Deployment

### Check Pods

Verify that the deployment is running:

```
kubectl get pods -n pixelview
```

### View Logs

To view logs:
```
kubectl logs <pod-name> -n pixelview
```

## Section 2: Docker Deployment for PixelView Integration

#### 2.1 Docker Compose Configuration

Create a `docker-compose.yml` file with the following content:
``` yaml
version: '3'
services:
  k8s-monitoring:
    restart: always
    image: ghcr.io/pixelvirt/kubernetes-go:latest
    env_file:
      - .env
    ports:
      - "9091:9091"
    container_name: k8s-monitoring
    volumes:
      - /usr/src/app/:/usr/src/app/

```
Additionally, create a `.env` file with the following content:
``` makefile 
GET_CONFIG_FROM=out-of-cluster
KUBECONFIG_FILE=/usr/src/app/kube-config
DATA_IP=http://10.0.0.15 # Replace with the IP address of the host machine where the application is deployed
DATA_PORT=9091   # Replace with the port number on which the application is running
```

The `.env` file stores the environment variables that the Docker service will use during deployment.


!!! note
    To integrate Kubernetes on PixelView using Docker, you need a valid `kubeconfig` file. Save this file at the following location on the host machine: `/usr/src/app/kube-config`.This file is essential for connecting to the Kubernetes cluster.
      
#### **2.2 Deploy the Application with Docker**

Once the `docker-compose.yml`, `.env`, and `kubeconfig` files are ready, navigate to the directory where these files are located and run the following command to deploy the application:
``` bash
docker-compose up -d
```
This command will start the service in the background.
#### **2.3 Clone Git Repository (Alternative to Manually Creating Deployment Files)**

If you'd rather not manually create the `docker-compose.yml` and `.env` files, you can clone the repository that contains the Docker manifests for Kubernetes integrations:

``` bash
git clone https://github.com/pixelvirt/pixelview-docker.git
cd pixelview-docker/k8s-integration
```

#### 2.4 Verification

To verify the application is running:

1. Check the logs:
```
docker-compose logs k8s-monitoring
```
---

### Verify Metrics on the PixelView Dashboard

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
        - Replace `<DATA_IP>` with the actual IP address of the host machine where your application is deployed.
        - Replace `<DATA_PORT>` with the port number configured for your application.
5. **Save and Display Metrics:**
    - After configuring the data source, save your changes.
    - The dashboard should now fetch metrics from the specified endpoint and display them in the new chart.
### Example Configuration

If your application is running at `http://10.0.0.15:9091`, the URL you would enter in the dashboard configuration would be:
```
http://10.0.0.15:9091/kubernetes/api/get-charts
```


### Tips

- Ensure that the endpoint `http://<DATA_IP>:<DATA_PORT>/kubernetes/api/get-charts` is accessible from the network where your Pixelview dashboard is hosted.
- Test the endpoint URL separately to verify that it returns the expected metrics data.
- Adjust dashboard configurations or metrics visualization settings as needed based on the data retrieved from the endpoint.