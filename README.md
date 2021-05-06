# server
Server software setup using Docker

## Setup Hardware

Setup [ecoquants Droplet | Digital Ocean](https://cloud.digitalocean.com/projects/55541477-9e0e-4fc4-adde-4793a47fe9ce/resources?i=c03c66):

- Image: Ubuntu 20.04 (LTS) x64
- Size: 4 vCPUs; 8GB / 160GB Disk; ($40/mo)
- Region: SFO2
- IPv4: 138.68.43.230

SSH using `~/.ssh/id_rsa.pub` on Ben's Macbook Pro added to * [Account > Settings > Security > SSH Keys | DigitalOcean](https://cloud.digitalocean.com/account/security?i=c03c66)

## Shell into server

```bash
ssh root@138.68.43.230
```

## Setup domain

- [ecoquants.com | Google Domains](https://domains.google.com/m/registrar/ecoquants.com/dns) with subdomains added under **Custom resource records** with:

- Type: **A**, Data:**138.68.43.230** and Name:
  - **api**
  - **ckan**
  - **erddap**
  - **geo**
  - **rstudio**
  - **shiny**
  - **www-dev**

## Install Docker

```bash
snap install docker
```

```
docker 19.03.13 from Canonical✓ installed
```

[Install Docker Compose | Docker Documentation](https://docs.docker.com/compose/install/):

```bash
# download the current stable release of Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# apply executable permissions to the binary
sudo chmod +x /usr/local/bin/docker-compose
```

### Test Docker

- [How To Run Nginx in a Docker Container on Ubuntu 14.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)

```bash
docker run --name test-web -p 80:80 -d nginx

# confirm working
docker ps
curl http://localhost
```

Test: http://www-dev.ecoquants.com, http://138.68.43.230

```bash
docker stop test-web
docker rm test-web
docker ps -a
```

## Docker with Caddy

* [caddy + rstudio | Rocker Project](https://www.rocker-project.org/use/networking/)

```bash
# get latest docker-compose files
git clone https://github.com/ecoquants/server.git
cd ~/server

# set environment variables: HOST=local \n PASSWORD=
vi .env

# launch
docker-compose up -d

# see all containers
docker ps -a


