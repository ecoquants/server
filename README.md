# server
Server software setup using Docker

## Setup Hardware

Setup [ecoquants Droplet | Digital Ocean](https://cloud.digitalocean.com/projects/55541477-9e0e-4fc4-adde-4793a47fe9ce/resources?i=c03c66):

- Image: Docker 19.03.12 on Ubuntu 20.04
- Size: 4 vCPUs; 8GB / 160GB Disk; ($40/mo)
- Region: SFO2
- IPv4: 159.89.130.72

SSH using `~/.ssh/id_rsa.pub` on Ben's Macbook Pro added to * [Account > Settings > Security > SSH Keys | DigitalOcean](https://cloud.digitalocean.com/account/security?i=c03c66)

## Shell into server

```bash
ssh root@159.89.130.72
```

## Setup domain

- [ecoquants.com | Google Domains](https://domains.google.com/m/registrar/ecoquants.com/dns) with subdomains added under **Custom resource records** with:

- Type: **A**, Data:**159.89.130.72** and Name:
  - **api**
  - **ckan**
  - **erddap**
  - **geo**
  - **rstudio**
  - **shiny**
  - **www-dev**
  - **drupal**

### Test Docker

- [How To Run Nginx in a Docker Container on Ubuntu 14.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)

```bash
docker run --name test-web -p 80:80 -d nginx

# confirm working
docker ps
curl http://localhost
```

Test: http://www-dev.ecoquants.com, http://159.89.130.72

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
cd server

# set environment variables: echo echo
echo 'PASSWORD=a*####*b' > .env

# setup local directories
mkdir /share
chmod -R 775 /share

# docker launch as daemon
docker-compose up -d

# To rebuild this image you must use:
#   docker-compose up --build
```

### Rebuild caddy 

eg after updating caddy/Caddyfile

```bash
C=caddy
docker stop $C
docker rm $C
docker-compose up -d
docker logs $C
```


