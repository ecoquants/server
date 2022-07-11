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

## Rebuild with Marina 2022-06-06

### Creat Virtual Machine

Create Virtual Machine (VM) Instance on Google Cloud Console.

```
gcloud compute instances create shiny-server --project=ucsd-sio-calcofi --zone=us-central1-a --machine-type=e2-standard-2 --network-interface=network-tier=PREMIUM,subnet=default --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=199066946721-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --create-disk=auto-delete=yes,boot=yes,device-name=shiny-server,image=projects/debian-cloud/global/images/debian-11-bullseye-v20220621,mode=rw,size=40,type=projects/ucsd-sio-calcofi/zones/us-central1-a/diskTypes/pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=any
```

### Install docker & git

```bash
# check version of Linux
uname -a
# Linux shiny-server 5.10.0-15-cloud-amd64 #1 SMP Debian 5.10.120-1 (2022-06-09) x86_64 GNU/Linux

# install git
sudo apt update
sudo apt install git
git --version
# git version 2.30.2

# install docker per https://docs.docker.com/engine/install/debian/
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Launch docker instance

Setup folders:
- `/share` for sharing across docker containers and host machine
- `/share/github` for storing Github repositories
- `/share/github` for storing Github repositories

```bash
sudo mkdir /share
sudo mkdir /share/github

# permissions before 
ls -la
# drwxr-xr-x  2 root root 4096 Jul  6 16:37 github

# check groups
sudo groups bebest
# bebest adm dip video plugdev google-sudoers
sudo groups mfrants
# mfrants adm dip video plugdev

# change group writable
sudo chmod g+w /share/github
sudo chgrp adm /share/github

# permissions after
ls -la
# drwxrwxr-x  2 root adm  4096 Jul  6 16:37 github

# get repo with docker configs:
cd /share/github
git clone https://github.com/CalCOFI/server.git

cd /share/github/server

# set env variable for password
echo 'PASSWORD=*secrethere*' > .env

sudo docker compose  up -d

sudo docker ps -a
```

## DNS

* [Reserving a static external IP address | Compute Engine Documentation | Google Cloud](https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address#:~:text=To%20reserve%20a%20static%20external,used%20with%20global%20load%20balancers.)

```
POST https://www.googleapis.com/compute/v1/projects/ucsd-sio-calcofi/regions/us-central1/addresses
{
  "description": "IP address for Shiny & Postgres server",
  "name": "shiny-server-ip",
  "networkTier": "PREMIUM",
  "region": "projects/ucsd-sio-calcofi/regions/us-central1"
}
```

- Permanent IP: 34.123.163.255

* [Google Domains - DNS](https://domains.google.com/registrar/calcofi.io/dns)


## BB 2022-07-10

```
dropdb gis

createdb gis

dump='/Users/bbest/My Drive/projects/calcofi/db_backup/gis_2022-07-08.dump'
echo $dump

pg_restore --verbose --create --dbname=gis $dump

psql -d gis --command='ALTER ROLE "admin" WITH LOGIN;'
```

Logged into rstudio.calcofi.io, in Terminal:

```bash
cd /share/github
git clone https://github.com/CalCOFI/api.git
git clone https://github.com/CalCOFI/apps.git
git clone https://github.com/CalCOFI/scripts.git
git clone https://github.com/CalCOFI/capstone.git
git clone https://github.com/CalCOFI/calcofi4r.git
```

### TODO

- [ ] update `pg_restore` instructions
- [ ] `rclone` install & configure for db bkups to Gdrive
