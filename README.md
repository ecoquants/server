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

```
Creating network "server_default" with the default driver
Creating volume "server_caddy_data" with default driver
Creating volume "server_caddy_config" with default driver
Creating volume "server_shiny-apps" with default driver
Pulling caddy (caddy:latest)...
latest: Pulling from library/caddy
339de151aab4: Pull complete
8b7a0cd9cca5: Pull complete
b5666523658a: Pull complete
b097228407f1: Pull complete
40682d93cc09: Pull complete
Digest: sha256:88fc125055b8c4dafe67a48219fd3a89c0a472aa254d2835b2e298b93493b28e
Status: Downloaded newer image for caddy:latest
Building rstudio
Sending build context to Docker daemon   2.56kB
Step 1/10 : FROM rocker/geospatial:4.0.5
4.0.5: Pulling from rocker/geospatial
a70d879fa598: Pull complete 
c4394a92d1f8: Pull complete 
10e6159c56c0: Pull complete 
27b92bb916fa: Pull complete 
c65ba6b41c9f: Pull complete 
61d7e8080820: Extracting [=====>                ]  25.07MB/220.4MB
9ced04101494: Download complete 
a688fa1fc176: Download complete 
277998b344ee: Downloading [=================>   ]  537.6MB/563MB
9b722d09cf72: Download complete 
...

Step 10/10 : CMD ["/init"]
 ---> Running in 536d9e507264
Removing intermediate container 536d9e507264
 ---> e850e80dfddf
Successfully built e850e80dfddf
Successfully tagged server_rstudio:latest
Traceback (most recent call last):
  File "docker-compose", line 3, in <module>
  File "compose/cli/main.py", line 81, in main
  File "compose/cli/main.py", line 203, in perform_command
  File "compose/metrics/decorator.py", line 18, in wrapper
  File "compose/cli/main.py", line 1189, in up
  File "compose/cli/main.py", line 1185, in up
  File "compose/project.py", line 664, in up
  File "compose/service.py", line 364, in ensure_image_exists
  File "compose/service.py", line 1133, in build
  File "compose/service.py", line 1948, in build
FileNotFoundError: [Errno 2] No such file or directory: '/tmp/tmp6mvmtnlk'
[14295] Failed to execute script docker-compose
```

```bash
docker-compose up -d
```

```
Creating rstudio ... error
Creating caddy   ... 
Creating caddy   ... done

ERROR: for rstudio  Cannot start service rstudio: error while creating mount source path '/share': mkdir /share: read-only file system
```

```bash
mkdir /share
chmod 775 /share

```


# see all containers
docker ps -a


