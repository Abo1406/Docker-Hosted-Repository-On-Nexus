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

## Run Nexus Service as a Docker Container

To run the Nexus service inside a Docker container, follow these steps:

1. Pull the Nexus 3 Docker image:
   ```sh
   docker pull sonatype/nexus3
   ```
2. Open port 8081 in the firewall:
   ```sh
   firewall-cmd --add-port=8081/tcp --permanent
   firewall-cmd --reload
   ```
3. Create a Docker volume for persistent data storage:
   ```sh
   docker volume create --name nexus-data
   ```
4. Create a directory for Nexus data:
   ```sh
   mkdir -p /nexus-data
   ```
5. Set the appropriate permissions:
   ```sh
   chown -R 200 /nexus-data/
   ```
6. Run the Nexus container:
   ```sh
   docker run -p 8081:8081 -d --name nexus -v /nexus-data:/nexus-data sonatype/nexus3:latest
   ```
7. Access the Nexus service at: [http://localhost:8081](http://localhost:8081)

### Persist Nexus Service After Reboot

To ensure Nexus service persists after a reboot:

1. Create a systemd service file:
   ```sh
   vim /etc/systemd/system/nexus.service
   ```
   Add the following content:
   ```ini
   [Unit]
   Description=Docker Container mycontainer
   After=network.target docker.service
   Requires=docker.service

   [Service]
   Restart=always
   ExecStart=/usr/bin/docker start -a nexus
   ExecStop=/usr/bin/docker stop nexus

   [Install]
   WantedBy=multi-user.target
   ```
2. Reload the systemd daemon:
   ```sh
   systemctl daemon-reload
   ```
3. Enable and start the Nexus service:
   ```sh
   systemctl enable --now nexus.service
   ```
4. Check the status of the service:
   ```sh
   systemctl status nexus.service
   ```

## Conclusion
Following these steps, you will successfully set up and push a Dockerized Node.js application to a Nexus-hosted repository. This setup allows for better container image management and secure distribution.

---

For any issues, check the Nexus logs and Docker daemon logs for troubleshooting.



