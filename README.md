#  Docker Hosted Repository on Nexus

This project demonstrates how to build and push a Dockerized Node.js application to a Nexus-hosted Docker repository.

## Prerequisites

Before proceeding, ensure you have the following installed and configured:
- Docker
- Nexus Repository Manager
- FirewallD (if applicable)
- A user account on Nexus with the necessary permissions

## Steps to Set Up and Push the Docker Image

### 1. Create a Docker Hosted Repository on Nexus
- In Nexus, create a new repository named `docker-hosted` with the appropriate configurations.

### 2. Define a New Role and Assign Permissions
- Create a new role in Nexus and grant it all necessary permissions for the Docker repository.
- Assign this role to your Nexus user account.

### 3. Configure Nexus Repository to Listen on Port 8083
- Set the HTTP port for the Docker repository to `8083` in Nexus settings.

### 4. Allow Traffic on Port 8083 in Firewall
- Run the following command to open port 8083:
  ```sh
  firewall-cmd --add-port=8083/tcp --permanent
  firewall-cmd --reload
  ```

### 5. Configure Docker Daemon
- Create a configuration file at `/etc/docker/daemon.json` with the following content:
  ```json
  {
    "insecure-registries": ["localhost:8083"]
  }
  ```
- Restart the Docker service:
  ```sh
  systemctl restart docker
  ```

### 6. Enable Docker Bearer Token Realm in Nexus
- Navigate to `Security > Realms` in Nexus and enable **Docker Bearer Token Realm**.

### 7. Authenticate Docker with Nexus
- Run the following command to log in to the Nexus repository:
  ```sh
  docker login localhost:8083
  ```

### 8. Build and Push the Docker Image
- Navigate to your application directory and build the Docker image:
  ```sh
  docker build -t my-app:1.2 .
  ```
- Tag the image for Nexus:
  ```sh
  docker tag my-app:1.2 localhost:8083/my-app
  ```
- Push the image to the Nexus repository:
  ```sh
  docker push localhost:8083/my-app
  ```

## Conclusion
Following these steps, you will successfully set up and push a Dockerized Node.js application to a Nexus-hosted repository. This setup allows for better container image management and secure distribution.

---

For any issues, check the Nexus logs and Docker daemon logs for troubleshooting.


