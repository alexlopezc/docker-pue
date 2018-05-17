
Tercera Sesión: https://gtd-docker.pad.floss.cat/p/swarm


apt-get update
apt-get -y upgrade
apt-get -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt-get update
apt-get -y install   docker-ce
usermod -aG docker ubuntu

docker run hello-world

Si te gusta Vim: apt -y install vim-syntax-docker vim-nox


apt install docker-compose

--------------------------------------------------------------

Ja tens el docker instal·lat? +1
+1+1+1+1+1+1+1+1+1+1+1+1+1+1


                      then

A les ubuntus:

apt purge network-manager
killall dhclient
vim /etc/network/interfaces

modifiquem "iface enp0s3 inet dhcp" per:
iface enp0s3 inet static
    address 192.168.20.XXX/24
    gateway 192.168.20.254


ip -4 addr show dev enp0s3 <----- borra la IP que tienes ahora
ip addr del YOURIP/MASK dev enp0s3
ip link set enp0s3 down
ifup enp0s3
rm /etc/resolv.conf
echo "nameserver 192.168.20.30" > /etc/resolv.conf

DONE! (i.e. test ping www.pg.com)

-----------------------------------------------------------------------------------------------------
CTRL+P+Q <--- dettach

docker run hello-world
docker run -ti centos
docker run -d nginx
docker pull centos debian ubuntu 
for image in centos debian ubuntu ; do docker pull $image ; done
docker search ellk
docker search elk
docker pull sebp/elk
docker ps
docker stop b9b38402096f 
docker kill b9b38402096f 
docker rm b9b38402096f 
docker ps
docker inspect 293749f1faea
docker ps
docker 
docker ps
docker stop 293749f1faea
docker ps
docker ps -a
docker run -ti centos
docker run -ti library/centos:latest
docker run -d mysql
docker ps
docker ps -a
docker logs 1de7b5ac8c8e
docker run -e MYSQL_ROOT_PASSWORD=pepito -d mysql 
docker logs 12b25d4cebce0a19ce7301f27c6b2587150461c69561550e3e711cf24357fffc
docker logs -f 12b25d4cebce0a19ce7docker run -d --name mibbdd mysql:5.7
301f27c6b2587150461c69561550e3e711cf24357fffc
docker ps
docker stop vibrant_meninsky
docker ps -a
docker ps -aq
docker ps -q
docker ps -qa
docker ps -qa | xargs docker rm
docker run -d -p 8888:80 nginx
docker logs -f 6951f998da2615fd389fd3632a28530e4b746e39dc72ae0e8428f51705a5e8bf
docker run -ti debian
docker ps -a
docker start cocky_lovelace
docker attach cocky_lovelace
docker ps
docker run -ti debian
docker run -ti ubuntu
docker p
docker ps
docker attach determined_montalcini
docker run --name centitos -ti centos
docker ps
docker ps -a
docker rm centitos
docker ps
docker rm focused_allen
docker rm -f focused_allen
docker ps
docker run -v /root/miweb:/usr/share/nginx/html  -d -p 80:80 nginx
docker ps
docker rm -f hopeful_northcutt
docker run --name miweb -v /root/miweb:/usr/share/nginx/html  -d -p 80:80 nginx
docker rm -f miweb
docker run --name miweb -v /root/miweb:/usr/share/nginx/html  -d -p 80:80 nginx
docker stop miweb
docker start miweb
docker ps
docker rm -f miweb
docker run --name miweb -v /root/miweb:/usr/share/nginx/html  -d -P nginx
docker run  -v /root/miweb:/usr/share/nginx/html  -d -P nginx
docker ps
docker inspect 1c0ee9c3381f
docker inspect  1c0ee9c3381f -f '{.NetworkSettings.IPAddress}'
docker inspect  1c0ee9c3381f -f '{{.NetworkSettings.IPAddress}'
docker inspect  1c0ee9c3381f -f '{{.NetworkSettings.IPAddress}}'
docker 
docker stats
docker ps
docker top f34704fd5351
docker search elk
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -it --name elk sebp/elk
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -it --name elk sebp/elk
docker rm -f elk
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -it --name elk sebp/elk
docker search bitcoin
docker search ethereum
docker run -ti ethereum/client-go
docker images
docker pull kpeiruza/standalone-bind-jessie
docker pull kpeiruza/bind-standalone-jessie
docker search kpeiruza
docker pull kpeiruza/standalone-bind9
docker images
tar c * | docker import - michroot:latest
docker run -ti michroot:latest /bin/ladem
docker run -ti michroot:latest /bin/ladem
docker images
docker search elk
docker build -t pinguito:latest .
docker run -ti pinguito
docker run -ti pinguito 8.8.4.4
docker run -ti pinguito 1.1.1.1
docker ps
docker attach thirsty_minsky
docker ps
docker exec -ti brave_chatterjee /bin/bash
docker build -t otraweb:latest .
docker build -t otraweb:latest .
docker run -d -p 8888:80  otraweb:latest 
docker logs 507f83eaa60c2786cd0beb948d13250a17d1a2be282e9653e18e78b87543931b
docker exec -ti 507f83eaa60c2786cd0beb948d13250a17d1a2be282e9653e18e78b87543931b /bin/bash
docker ps
docker rm -f xenodochial_williams
docker rm -f upbeat_haibt
docker build -t otraweb:latest .
docker run -ti ubuntu
docker ps -a
docker commit 808e6d10a31a ubinti:latest
docker run -ti ubinti
docker ps -a | more
docker diff 29b570a1eaaf
docker diff 808e6d10a31a 
docker ps
docker ps -a
docker start f4c5344409bd
docker ps
docker inspect f4c5344409bd

----------------------------------------------------------------------------------------------------------------------------
Dockerfile:

FROM library/debian:stretch
MAINTAINER kenneth@floss.cat

RUN     apt-get update && \
        apt-get -y --no-install-recommends install libapache2-mod-php7.0 && \
        rm /var/www/html/*.html && \
        ln -sf /dev/stdout /var/log/apache2/access.log && \
        ln -sf /dev/sterr /var/log/apache2/error.log
COPY    index.html      /var/www/html

EXPOSE 80
ENTRYPOINT [ "/usr/sbin/apachectl", "-D", "FOREGROUND" ]


Objetivo: Imágenes livianas.

Genera una IMG de Docker en la que se hayan suprimido el máximo de datos innecesarios para el runtime. Verifica el ahorro de espacio en la IMG resultante VS la no purgada.

Ya lo tienes hecho? ganaste mucho? :-)
------------------------------------------------------------------------------------------------------------

Docker Volumes:



Docker Networks:

docker network create [opciones de rango de red, pool de IPs, attachable .... ] nombredemired

docker network ls -f name=nombredemired  <------ miramos el ID alfanumérico

brctl addif br-IDAlfanumérico MITARJETADERED

Ejecutar contenedores con "--network nombredemired"

-------------------------------------------------------------------------

Aplicaciones multi-contenedor, Docker-compose & Docker-stack


docker run -d -e MYSQL_ROOT_PASSWORD=tupassword --name mibbdd mysql:5.7

docker run --name some-wordpress --link mibbdd:mysql -p 8080:80 -d wordpress



----

docker-compose.yaml:

version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    volumes:
      - uploads:/var/www/html/wp-content
    ports:
      - 80
    environment:
      WORDPRESS_DB_PASSWORD: example

  mysql:
    image: mysql:5.7
    restart: always
    volumes:
      - bbdd:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: example

volumes:
  bbdd:
  uploads:

----------------------------------------------------------------------------------


  mysql:
    image: mysql:5.7
    restart: always
    volumes:
      - bbdd:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
    deploy:
      placement:
        constraints:
          - node.hostname == node3.swarm.lan

---------------------------------------------------------------------------------------------------------------------------------

apt install nfs-kernel-server
mkdir /srv/nfs
chmod 1777 /srv/nfs

vi /etc/exports. Afegeix:

/srv/nfs        192.168.20.201(rw,no_subtree_check) 192.168.20.202(rw,no_subtree_check) 192.168.20.203(rw,no_subtree_check)

--------------------------------------------------
service nfs-server start

Arranca tus VMs. En ellas, configuraremos el NFS en el /etc/fstab

apt install nfs-common
service rpcbind restart

vi /etc/fstab, añade:

TUIPDELANFITRION:/srv/nfs    /srv/nfs    nfs    nfsvers=3,hard,intr    0    0

En mi caso: 192.168.20.200:/srv/nfs    /srv/nfs    nfs    nfsvers=3,hard,intr    0    0

mkdir /srv/nfs
mount -a







