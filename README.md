# home-assistant

## Зміст
- [Settings instructions](#1-settings-instructions)
- [Setup steps](#2-setup-steps)
  - [Portainer](#201-portainer)

---

### 1. Settings instructions
Інструкції, які використовуються для налаштувань:
- Docker
  - [Встановлення Debian](https://io-home.ru/home-assistant/docker-container/docker-chast-1-ustanovka-debian/)
  - [Docker та Portainer](https://io-home.ru/home-assistant/docker-container/docker-chast-2-docker-i-portainer/)
  - [Home Assistant](https://io-home.ru/home-assistant/docker-container/docker-chast-3-home-assistant-container/)
  - [File Editor](https://io-home.ru/home-assistant/docker-container/docker-chast-4-file-editor/)
  - [MariaDB](https://io-home.ru/home-assistant/docker-container/docker-chast-5-mariadb/)
  - [Mosquitto MQTT](https://io-home.ru/home-assistant/docker-container/docker-chast-6-mosquitto-mqtt/)
  - [Zigbee2MQTT](https://io-home.ru/home-assistant/docker-container/docker-chast-7-zigbee2mqtt/)
  - [ESPHome](https://io-home.ru/home-assistant/docker-container/docker-chast-8-esphome/)
  - [Node-Red](https://io-home.ru/home-assistant/docker-container/docker-chast-9-node-red/)
  - [Bluetooth](https://io-home.ru/home-assistant/docker-container/docker-chast-10-bluetooth/)

---

### 2. Setup steps
#### Етапи налаштувань:
##### 2.01. Portainer
   - command: `docker compose up portainer`
   - config:
     - host: `http://127.0.0.1:9002/`
     - username: `admin`
     - password: `0123456789pP`
   - нюанси налаштувань:
     - `environment: CSP=false` - не секюрно, може бути доступ за межами мережі

##### 2.02. Portainer


