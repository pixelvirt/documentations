### PixelView Kubernetes Deployment and Service Setup Guide

This guide will walk you through deploying and configuring the PixelView application and its services using Kubernetes.

#### Prerequisites

- Kubernetes cluster up and running
- `kubectl` configured to interact with your cluster

#### Step-by-Step Instructions

##### 1. Create Deployment and Service YAML Files

Save the following content into separate YAML files.

**Combined Deployment File:** `pixelview-deployment.yaml`\
```yaml  linenums="1"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: escalation
spec:
  replicas: 1
  selector:
    matchLabels:
      app: escalation
  template:
    metadata:
      labels:
        app: escalation
    spec:
      containers:
      - name: escalation
        image: ghcr.io/pixelvirt/escalation:latest
        ports:
        - containerPort: 80
        env:
        - name: MONGOURL
          value: "mongoservice:27017"
        - name: RABBITURL
          value: "amqp://<username>:<password>@rabbitmqservice:5673/alertagility"
      imagePullSecrets:
      - name: github-auths
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fin-escalation
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fin-escalation
  template:
    metadata:
      labels:
        app: fin-escalation
    spec:
      containers:
      - name: fin-escalation
        image: ghcr.io/pixelvirt/findescalation:latest
        ports:
        - containerPort: 80
        env:
        - name: MONGOURL
          value: "mongoservice:27017"
        - name: RABBITURL
          value: "amqp://<username>:<password>@rabbitmqservice:5673/alertagility"
      imagePullSecrets:
      - name: github-auths
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alertagility
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alertagility
  template:
    metadata:
      labels:
        app: alertagility
    spec:
      containers:
      - name: alertagility
        image: ghcr.io/pixelvirt/alertagility:latest
        ports:
        - containerPort: 9090
        - containerPort: 9191
        env:
        - name: MONGOURL
          value: "mongoservice:27017"
        - name: DOMAIN
          value: "demo.pixelvirt.com"
        - name: RABBITURL
          value: "amqp://<username>:<password>@rabbitmqservice:5673/alertagility"
        - name: AUTOMATIONURL
          value: "<automation-url>:443"
      imagePullSecrets:
      - name: github-auths
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rabbitmq
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rabbitmq
  template:
    metadata:
      labels:
        app: rabbitmq
    spec:
      containers:
      - name: rabbitmq
        image: ghcr.io/pixelvirt/inithive-rabbitmq:latest
        ports:
        - containerPort: 5672
        env:
        - name: RABBITMQ_PASSWORD
          value: <rabbitmq-password>
        - name: RABBITMQ_USER
          value: <rabbitmq-username>
      imagePullSecrets:
      - name: github-auths
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb
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
        image: mongo:5
        volumeMounts:
        - mountPath: /data/db
          name: mongodb-data
      volumes:
      - name: mongodb-data
        hostPath:
          path: /data/db
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alert-frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alert-frontend
  template:
    metadata:
      labels:
        app: alert-frontend
    spec:
      containers:
      - name: alert-frontend
        image: ghcr.io/pixelvirt/alertagility-frontend:v2
        ports:
        - containerPort: 80
        env:
        - name: DOLLAR
          value: "$$"
        - name: ALERTAGILITY_API
          value: "alertagility:9090"
        - name: ALERTAGILITY_REPORT
          value: "alertagility:9191"
        - name: MONITORING_URL
          value: "<monitoring-url>:8000"
        - name: VAULT_URL
          value: "<vault-url>"
      imagePullSecrets:
      - name: github-auths

```
**Combined Service File:** `pixelview-service.yaml`
```yaml  linenums="1"
apiVersion: v1
kind: Service
metadata:
  name: rabbitmqservice
spec:
  selector:
    app: rabbitmq
  ports:
    - protocol: TCP
      port: 5672
      targetPort: 5672
---
apiVersion: v1
kind: Service
metadata:
  name: alert-frontend-service
spec:
  selector:
    app: alert-frontend
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 80
  externalIPs:
    - <external-ip>
---
apiVersion: v1
kind: Service
metadata:
  name: alertagility
spec:
  ports:
    - name: "9090"
      port: 9090
      targetPort: 9090
  selector:
    app: alertagility
---
apiVersion: v1
kind: Service
metadata:
  name: mongoservice
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017

```
#### 2. Apply the YAML Files

Apply the combined deployment and service YAML files using `kubectl apply -f` command:
```sh
kubectl apply -f pixelview-deployment.yaml
kubectl apply -f pixelview-service.yaml
```
#### 3. Verify Deployments and Services

To ensure that your deployments and services are running correctly, use the following commands:

```sh
kubectl get deployments
kubectl get services
```
You should see a list of deployments and services along with their statuses.
### Accessing the Services

Use the external IPs and ports specified in the service configurations to access the services. For example, to access the `alert-frontend-service`, navigate to:
```
http://<external-ip>:3000
```
Replace `<external-ip>` with the actual IP address specified in the `pixelview-service.yaml`.
### Conclusion

You have successfully deployed and configured the PixelView application and its services on your Kubernetes cluster using the combined YAML files. Ensure to monitor your deployments and services for any issues and refer to Kubernetes documentation for advanced configurations and troubleshooting.