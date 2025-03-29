# 467N13
Home Automation Repo
# Install Docker and Set Up Home Assistant, Scrypted, and Z-Wave JS on Raspberry Pi OS Lite

## Step 1: Update and Prepare the System
1. Connect to your Raspberry Pi via SSH or terminal.
2. Update the package list and upgrade the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
3. Install required dependencies:
   ```bash
   sudo apt install -y git curl libffi-dev libssl-dev python3-dev python3-pip
   ```

---

## Step 2: Install Docker
1. Run the official Docker installation script:
   ```bash
   curl -fsSL https://get.docker.com | sudo sh
   ```
2. Add your user to the `docker` group to allow running Docker without `sudo`:
   ```bash
   sudo usermod -aG docker $USER
   ```
3. Apply the changes (log out and back in or run):
   ```bash
   newgrp docker
   ```
4. Verify installation:
   ```bash
   docker --version
   ```

---

## Step 3: Install Docker Compose
1. Install Docker Compose using `pip3`:
   ```bash
   sudo pip3 install docker-compose
   ```
2. Verify installation:
   ```bash
   docker-compose --version
   ```

---

## Step 4: Create the Docker Compose File
1. Create a directory for your Docker services:
   ```bash
   mkdir -p ~/docker && cd ~/docker
   ```
2. Create and open the `docker-compose.yml` file:
   ```bash
   nano docker-compose.yml
   ```
3. Paste the following configuration:
   ```yaml
   version: '3.8'
   services:
     homeassistant:
       container_name: homeassistant
       image: homeassistant/home-assistant:latest
       restart: unless-stopped
       network_mode: host
       privileged: true
       volumes:
         - ./homeassistant:/config
         - /etc/localtime:/etc/localtime:ro
       environment:
         - TZ=America/Los_Angeles # Change to your timezone

     zwavejs2mqtt:
       container_name: zwavejs
       image: zwavejs/zwavejs2mqtt:latest
       restart: unless-stopped
       network_mode: host
       devices:
         - /dev/serial/by-id/usb-XXXXX:/dev/zwave  # Replace XXXXX with your Z-Wave stick ID
       volumes:
         - ./zwave:/usr/src/app/store
       environment:
         - TZ=America/Los_Angeles

     scrypted:
       container_name: scrypted
       image: ghcr.io/koush/scrypted
       restart: unless-stopped
       network_mode: host
       volumes:
         - ./scrypted:/server/volume
   ```
4. **Save and exit** (Press `CTRL + X`, then `Y`, then `ENTER`).

---

## Step 5: Start the Containers
1. Run the following command to start everything:
   ```bash
   docker-compose up -d
   ```
2. Check if all containers are running:
   ```bash
   docker ps
   ```

---

## Step 6: Access Each Service
- **Home Assistant:** `http://<Raspberry_Pi_IP>:8123`
- **Z-Wave JS UI:** `http://<Raspberry_Pi_IP>:8091`
- **Scrypted:** `http://<Raspberry_Pi_IP>:11080`

---

## Step 7: Enable Docker to Start on Boot
```bash
sudo systemctl enable docker
```

---

## Step 8: Manage Docker Containers
- **Restart all containers:**
  ```bash
  docker-compose restart
  ```
- **Stop all containers:**
  ```bash
  docker-compose down
  ```
- **Update all containers:**
  ```bash
  docker-compose pull && docker-compose up -d
  ```

---

## 🎉 All Set!
Your Raspberry Pi is now running **Home Assistant, Z-Wave JS, and Scrypted** in Docker. 🚀 Let me know if you need any adjustments!
