# PixelView Zabbix Kubernetes Deployment Guide

## **Overview**

This guide provides a step-by-step process to deploy PixelView to fetch and expose metrics from Zabbix within a Kubernetes cluster using a pixelview namespace.

## **Prerequisites**    
- A running Kubernetes cluster

- kubectl configured with the cluster

- A MySQL/MariaDB instance for Zabbix

- Persistent storage for MySQL (optional but recommended)

- Network access between PixelView, Zabbix, MySQL, and Redis

## **Kubernetes Deployment Configuration**
---
### ConfigMap for Environment Variables

The following `ConfigMap` stores non-sensitive configuration values:
``` yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: pixelview-config
  namespace: pixelview
data:
  FLASK_ZABBIX_ROOT_URL: "http://zabbix-backend:8000/metrics"
  FLASK_ZABBIX_MYSQL_HOST: "mysql-service"
  FLASK_ZABBIX_MYSQL_PORT: "3306"
  FLASK_ZABBIX_MYSQL_DATABASE: "zabbix"
  CACHE_TYPE: "RedisCache"
  CACHE_REDIS_URL: "redis://redis-service:6379/0"
  CACHE_DEFAULT_TIMEOUT: "500"
  ALLOWED_HOSTS: "*"

```
---
### Secret for MySQL Credentials

To securely store MySQL credentials, use a Secret:
``` yaml
apiVersion: v1
kind: Secret
metadata:
  name: pixelview-secret
  namespace: pixelview
type: Opaque
data:
  FLASK_ZABBIX_MYSQL_USER: <base64-encoded-user>
  FLASK_ZABBIX_MYSQL_PASSWORD: <base64-encoded-password>
```
Replace `<base64-encoded-user>` and `<base64-encoded-password>` with base64-encoded values of your MySQL username and password.
---
### Deploying the Zabbix Backend

Create a Deployment for the API backend:
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: zabbix-backend
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: zabbix-backend
  template:
    metadata:
      labels:
        app: zabbix-backend
    spec:
      containers:
      - name: zabbix-backend
        image: ghcr.io/pixelvirt/zabbix-test:latest
        envFrom:
        - configMapRef:
            name: pixelview-config
        - secretRef:
            name: pixelview-secret
        ports:
        - containerPort: 8000
```
---
### Service for Zabbix Backend

Expose the backend service internally:
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: zabbix-backend-service
  namespace: pixelview
spec:
  selector:
    app: zabbix-backend
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 8000
  type: ClusterIP
```
---
### Deploying Redis for Caching
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: pixelview
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7.0
        ports:
        - containerPort: 6379
```

### Service for Redis

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-service
  namespace: pixelview
spec:
  selector:
    app: redis
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
  type: ClusterIP
```
### Deployment Steps

*Save the manifest file as `pixelview-k8s.yaml`.*

Apply the manifest to Kubernetes:

``` bash 
kubectl apply -f pixelview-k8s.yaml
```

Verify the deployment:

``` bash
kubectl get pods -n pixelview
```
## **Accessing Metrics**
Once deployed, access metrics at:
```
http://<your-domain-or-ip>:8000/metrics
```

To test the API:
```bash
curl -X GET http://<your-domain-or-ip>:8000/metrics
```

---

## **Visualizing Metrics in PixelView**
1. **Log in to the PixelView dashboard.**
2. **Click on** "Add New Chart."
3. **Click on** "Add Source."
4. **Provide an appropriate name** in the "Name" section.
5. **In the URL section, insert:**
   ```
   http://<your-domain-or-ip>:8000/metrics
   ```
6. **Click on Submit.**
7. After submitting, multiple metric options will appear.
8. **Select the desired items** to visualize them on the PixelView dashboard.

---

## **Troubleshooting**

### **1. API Not Responding**
- **Check logs:**

``` bash
kubectl logs -n pixelview deployment/zabbix-backend
```

- Ensure `MySQL` and `Redis` are accessible.

- Verify `ConfigMap` and `Secret` configurations.

### **2. Redis Connection Issues**

- Test Redis connectivity:

``` bash
kubectl exec -it -n pixelview deployment/redis -- redis-cli ping
```

- If not running, restart:

``` bash 
kubectl rollout restart deployment redis -n pixelview
```
### **3. MySQL Connection Issues**

- Check if MySQL is reachable:

``` bash 
mysql -h <mysql_host> -u <mysql_user> -p
```

---
## **security Considerations**

- Do not expose MySQL credentials publicly.

- Restrict access to Redis and the API using firewall rules.

- Use ClusterIP for internal services to limit exposure.

---
This guide ensures a complete Kubernetes deployment for fetching and visualizing Zabbix metrics in PixelView.