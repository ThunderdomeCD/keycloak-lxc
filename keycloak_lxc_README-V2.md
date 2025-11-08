<!-- ABOUT THE PROJECT -->
## Keycloak deployment in Proxmox

This is a step-by-step guide for creating an LXC inside Proxmox that will host a Keycloak 
deployment inside a podman container 



<!-- GETTING STARTED -->
## Reason for the Design

This instance was deployed this way to keep the keycloak deployment isolated to its own LXC to 
afford the ability to dedicate its own IP and to ensure the ability to migrate the singular service
between hosts as needed within a proxmox cluster environment. The container inside the container 
affords fast and reliable updates to the software image while maintaining persistent volumes apart 
from the application.

### Prerequisites

Prior to starting the install and basic configuration, you will need to deploy the container on your 
proxmox host utilizing the keycloak-lxc-create.sh script in this repo. If only wanting to deploy within
podman or docker, you just need a suitable podman or docker host with at least 2 cores and 4GB or RAM
available. Once the LXC is created and powered on, ensure it is updated, install podman, and install 
postgresql with the command below.
* npm
  ```sh
  apt update && apt install podman postgresql curl unzip
  ```

A DNS host A record needs to be created to resolve the keycloak authentication hostname for the container.
The record needs to read auth.mydomain.tld and be for the IP of the LXC.

### Deployment

1. Enable postgres and create the Keycloak DB inside the LXC
   ```sh
   sudo -u postgres psql
   CREATE DATABASE keycloak;
   CREATE USER keycloak WITH PASSWORD 'ChangeMe1!';
   GRANT ALL PRIVILEGES ON DATABASE keycloak TO keycloak;
   \q
   ```

2. Create the persistent directories
   ```sh
  mkdir -p /opt/keycloak/{data,tmp,conf,logs,providers}
  chown -R 1000:0 /opt/keycloak
  chmod -R u+rwX,g+rwX /opt/keycloak
  mkdir /etc/keycloak
   ```

3. Pull the image for Keycloak
   ```sh
   podman pull quay.io/keycloak/keycloak:latest
   ```

4. Create .env file for the keycloak container
   Use the keycloak.env in the repo as your .env template. Be sure to change the default passwords.
   Place the .env inside /opt/keycloak

5. Create podman-compose yaml for deployment
   Use the podman-compose.yml in this repo as your template. Be sure to change passwords.
   Place in /opt/keycloak

6. Run container with following command:





<p align="right">(<a href="#readme-top">back to top</a>)</p>
