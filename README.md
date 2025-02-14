# Rocket.Chat and MongoDB Docker Compose Setup

This repository provides a `docker-compose.yml` configuration for running Rocket.Chat and MongoDB using Docker. Follow the instructions below to get started.

## Prerequisites

Before you start, make sure you have the following installed:

- **Docker**: Docker is required to run containers.
- **Docker Compose**: Docker Compose helps to manage multi-container Docker applications.

### 1. Install Docker

Follow the steps below to install Docker on your system:

- **For Ubuntu (Linux)**:
  ```bash
  sudo apt update
  sudo apt install -y docker.io
  sudo systemctl enable --now docker
  ```

- **For Windows**: Download Docker Desktop from the official website: [Docker for Windows](https://www.docker.com/products/docker-desktop)

- **For MacOS**: Download Docker Desktop from the official website: [Docker for Mac](https://www.docker.com/products/docker-desktop)

### 2. Install Docker Compose

Docker Compose is used to define and run multi-container Docker applications. To install Docker Compose:

- **For Ubuntu**:
  ```bash
  sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  sudo chmod +x /usr/local/bin/docker-compose
  ```

- **For Windows/Mac**: Docker Compose comes bundled with Docker Desktop, so no additional installation is needed.

### 3. Disable Firewall or Allow Docker

If you have a firewall enabled, you need to either disable it or allow Docker to communicate. Follow these steps to allow Docker:

#### Option 1: Disable the Firewall
You can disable the firewall temporarily (not recommended for production environments):
```bash
sudo ufw disable
```

#### Option 2: Allow Docker through the Firewall (Recommended)
To allow Docker through the firewall while keeping the firewall enabled:
```bash
sudo ufw allow 2375/tcp
```

### 4. Clone the Repository and Set Up Docker Compose

Once Docker and Docker Compose are installed, follow these steps to run Rocket.Chat and MongoDB:

1. Clone the repository:
    ```bash
    git clone https://github.com/NisaargPendal/rocket-chat-compose.git
    cd rocket-chat-compose
    ```

2. Ensure you are running the following commands with root privileges:
    ```bash
    sudo su
    ```

3. Copy the provided `docker-compose.yml` into your project folder:
    ```yaml
    services:
      mongo:
        image: mongo:5
        volumes:
          - ./data/db:/data/db
        healthcheck:
          test: ["CMD-SHELL", "echo 'db.runCommand(\"ping\").ok' | mongo --quiet localhost:27017"]
          interval: 10s
          timeout: 5s
          retries: 5

      rocketchat:
        image: rocketchat/rocket.chat:latest
        environment:
          - PORT=3000
          - ROOT_URL=http://localhost:3000
          - MONGO_URL=mongodb://mongo:27017/rocketchat
          - MONGO_OPLOG_URL=mongodb://mongo:27017/local
        ports:
          - 3000:3000
        depends_on:
          mongo:
            condition: service_healthy
        volumes:
          - ./uploads:/app/uploads
    ```

4. Once the `docker-compose.yml` file is in place, start the services:
    ```bash
    docker-compose up -d
    ```

### 5. Access Rocket.Chat

After the containers start, you can access Rocket.Chat by navigating to [http://localhost:3000](http://localhost:3000) in your web browser.

---

### Troubleshooting

- If you run into issues with Docker containers not starting, check the logs:
  ```bash
  docker-compose logs
  ```

- If MongoDB or Rocket.Chat fails to start, check the health of the containers:
  ```bash
  docker-compose ps
  ```

- You can stop and remove all containers with:
  ```bash
  docker-compose down
  ```

---

### Notes

- The `docker-compose.yml` file is set up to pull the latest Rocket.Chat and MongoDB images. You can change the versions if needed.
- The data will persist in the `./data/db` and `./uploads` directories. Make sure these directories have the correct permissions.

Enjoy using Rocket.Chat with Docker!
