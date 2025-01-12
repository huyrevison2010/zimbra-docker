# zimbra-docker
The project to build a Zimbra 9 (built by Zextras) Docker image based on Rocky Linux 8.

Check out the built image on Docker Hub: https://hub.docker.com/r/iwayvietnam/zimbra_all

### How to build a new Docker image
##### Firstly, of course, install Docker and setup to manage Docker as a non-root user
See: https://docs.docker.com/engine/install/

##### Pull the latest Rocky Linux based docker image
> $ docker pull rockylinux/rockylinux

##### Checkout this git repo
> $ git clone https://github.com/iwayvietnam/zimbra-docker.git
>
> $ cd zimbra-docker

##### Download the latest Zimbra 9 (built by Zextras)
> $ wget -O opt/zimbra-install/zcs-9.0.0_OSE_RHEL8_latest-zextras.tgz https://download.zextras.com/zcs-9.0.0_OSE_RHEL8_latest-zextras.tgz

##### Build Zimbra a new docker image
> $ docker build --rm -t zimbra_all .

### How to start a new Zimbra container from prebuilt Docker image
##### Create a new Docker network dedicated for Zimbra
> $ docker network create zimbranet

##### Check the newly created IP range:
> $ docker network ls | grep zimbranet

(show network_id, e.g. 74f97a45ae8c, use in the next command line)

> $ ip addr | grep 74f97a45ae8c

(show IP, e.g. inet 172.20.0.1/16, use in the next step)

##### Run a new dnsmasq container:
> $ docker pull 4km3/dnsmasq
>
> $ docker run --name dnsmasq-iwaytest2 -d -it -p 172.20.0.1:53:53/tcp -p 172.20.0.1:53:53/udp --net=zimbranet --cap-add=NET_ADMIN 4km3/dnsmasq --address=/iwaytest2.com/172.20.0.3 --domain=iwaytest2.com --mx-host=iwaytest2.com,mail.iwaytest2.com,0

(assumption: the new Zimbra domain is iwaytest2.com)

##### Create a new Docker volume for Zimbra container:
> $ docker volume create zimbra-iwaytest2

##### Run a new Zimbra container:
> $ docker run --name zimbra-iwaytest2 -p 25:25 -p 80:80 -p 465:465 -p 587:587 -p 110:110 -p 143:143 -p 993:993 -p 995:995 -p 443:443 -p 3443:3443 -p 9071:9071 -h mail.iwaytest2.com --net=zimbranet --dns 172.20.0.1 -v zimbra-iwaytest2:/opt/zimbra -i -t -e PASSWORD=Zimbra2021 iwayvietnam/zimbra_all
(and WAIT...)
# About for Docker compose :
##### Require : Install docker-compose for your server
##### Create storage for docker-compose
> $ cd docker-compose && mkdir zimbra-storage
##### Run docker-compose 
> $ docker-compose up -d
##### Command check status
> $ docker-compose status
##### Command check logs  
> $ docker-compose logs -f
(and WAIT...)

### LICENSE
This work is released under GNU General Public License v3 or above.
