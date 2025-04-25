# Setup
The following steps should setup a server for a flexible and reliable IoT home weather station on a Raspberry Pi running the 64-bit version of Pi OS.

## SSH onto the server
```
ssh pi@raspberrypi.local
```

## Install updates, docker and git
Updates
```
sudo apt update && sudo apt upgrade -y
```
Docker
```
curl -sSL https://get.docker.com | sh
```
Docker compose
```
sudo apt install docker-compose-plugin -y
```
Check
```
docker --version
docker compose version
```
Install git if necessary
```
sudo apt install git
```

## Create project
Create the project folder by cloning this repository.
```
git clone https://github.com/TobiLxtr/weather-station-server.git ~/iot-server
```
```
cd ~/iot-server
```

Add a `.env` file in the same structure as the `.env.example` file to your project folder containing the initial credentials.
```
cp .env.example .env
```
```
nano .env
```
Edit the environment variables and save by hitting `ctrl + O` (WriteOut), `Enter` and `ctrl + X`(Exit).

## Docker compose
When everything is set up, run `docker compose`
```
sudo docker compose up -d
```

## Automatically start IoT server on boot
Create a systemd service.
```
sudo nano /etc/systemd/system/iot-server.service
```
Insert
```
[Unit]
Description=IoT Server Docker Compose Stack
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
WorkingDirectory=/home/pi/iot-server
ExecStart=/usr/bin/docker compose up -d
ExecStop=/usr/bin/docker compose down
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
```
Start on boot
```
sudo systemctl daemon-reexec      # If systemd itself needs a reload
sudo systemctl daemon-reload      # So that it knows the new .service file
sudo systemctl enable iot-server  # Starts the service automatically on boot
sudo systemctl start iot-server   # Starts the service immediately
```
Check
```
sudo systemctl status iot-server
```
