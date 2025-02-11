# **PixelView Zabbix Metrics Setup Guide**

## **Overview**
This guide provides a step-by-step process to deploy PixelView to fetch and expose metrics from Zabbix using Docker and visualize them on the PixelView dashboard.

## **Prerequisites**
- A running Zabbix instance with MySQL/MariaDB as the backend.
- Docker and Docker Compose installed.
- Redis for caching.
- Network access between PixelView, Zabbix, MySQL, and Redis.

---

## **Deployment Configuration**

### **1. Docker Compose File**
Create a `docker-compose.yml` file:
```yaml
version: "3"

services:
  api:
    image: ghcr.io/pixelvirt/zabbix-test:latest
    env_file:
      - ./env
    network_mode: "host"
    container_name: zabbix-backend

  redis:
    image: redis:7.0
    network_mode: "host"
```

### **2. Environment Variables**
Create a `.env` file (excluding sensitive details):
```env
FLASK_ZABBIX_ROOT_URL=http://<your-domain-or-ip>:8000/metrics
FLASK_ZABBIX_MYSQL_HOST=<mysql_host>
FLASK_ZABBIX_MYSQL_PORT=3306
FLASK_ZABBIX_MYSQL_USER=<mysql_user>
FLASK_ZABBIX_MYSQL_PASSWORD=<mysql_password>
FLASK_ZABBIX_MYSQL_DATABASE=zabbix

CACHE_TYPE=RedisCache
CACHE_REDIS_URL=redis://localhost:6379/0
CACHE_DEFAULT_TIMEOUT=500
ALLOWED_HOSTS=*
```

Replace `<your-domain-or-ip>`, `<mysql_host>`, `<mysql_user>`, and `<mysql_password>` with actual values.

---

## **Installation Steps**
1. **Ensure Docker and Docker Compose are installed:**
   ```bash
   docker --version
   docker-compose --version
   ```

2. **Start the services:**
   ```bash
   docker-compose up -d
   ```

3. **Verify the containers are running:**
   ```bash
   docker ps
   ```

---

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
- Check logs:  
  ```bash
  docker logs zabbix-backend
  ```
- Ensure MySQL and Redis are accessible.
- Verify `.env` configurations.

### **2. Redis Connection Issues**
- Test Redis connectivity:
  ```bash
  redis-cli -h localhost -p 6379 ping
  ```
- If not running, restart:
  ```bash
  docker restart redis
  ```

### **3. MySQL Connection Issues**
- Check if MySQL is reachable:
  ```bash
  mysql -h <mysql_host> -u <mysql_user> -p
  ```

---

## **Security Considerations**
- Do **not** expose MySQL credentials publicly.
- Use a secure network mode instead of `host` for production.
- Restrict access to Redis and the API using firewall rules.

---

This documentation should help you deploy and visualize Zabbix metrics on PixelView efficiently.