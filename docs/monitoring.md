### Prerequisites

Before integrating the `PixelView` service with your OpenStack environment, ensure that you have the following prerequisites:

- Access to an OpenStack environment (with Keystone authentication enabled).
- Docker installed on your system.
### Configure Environment Variables

Create a file named `.env` in the same directory as your `docker-compose.yml` file. This file should contain environment variables required for the `openstack_mon` service. Here's an example `.env` file based on the provided information:
```shell title=".env"
ALLOWED_ORIGINS="*"
DB_USERNAME="[OPENSTACK_DB_USERNAME]"
DB_PASSWORD="[OPENSTACK_DB_PASSWORD]"
DB_HOST="[OPENSTACK_DB_HOST]"
DB_PORT=[RUNNING_DB_PORT]
ROOT_URL="[URL:PORT]"
SECRET_KEY="[SECRET_KEY]"
```
### Integrate with OpenStack
**Pull Docker Image**: Pull the latest version of the `monitoring_openstack` Docker image by running the following command:
```
docker pull ghcr.io/pixelvirt/monitoring_openstack:latest
```
**Create Docker Compose File**: Create a `docker-compose.yml` file with the following content:
```
version: '3'
services:
  openstack_mon:
    restart: always
    image: ghcr.io/pixelvirt/monitoring_openstack:latest
    env_file:
      - .env
    ports:
      - "5000:5000"    
    container_name: openstack_mon
    command: "gunicorn main:app -b 0.0.0.0:5000 --reload --log-level=debug --log-file=api.log"
```
**Start the Service**:
Run the following command to start the `monitoring_openstack` service:
```
docker compose up -d
```
**Configure OpenStack Integration**: In your monitoring application or system, configure the OpenStack integration using the API endpoint provided by `openstack_mon`. Provide the necessary authentication details and endpoints to enable monitoring of your OpenStack environment.
### Verify Integration

Once the integration is configured, verify that your monitoring system can successfully retrieve metrics and monitor your OpenStack environment using the provided API.
## Conclusion

By following these steps and providing the necessary environment variables, you can integrate the `PixelView`  with your OpenStack environment and monitor it effectively. If you encounter any issues during the integration process, refer to the documentation of `monitoring_openstack` or seek assistance from the service provider.