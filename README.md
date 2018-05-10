# docker-pue
Dockerfiles created durng pue formation


# Eliminar cosas de debian no necesarias
echo 'Yes, do as I say!'| apt-get -qq -y purge --force-yes $(dpkg -l | 

# create netowrk 
docker network create --attachable mired

# bridge utils
sudo apt-get install bridge-utils

# attach network
docker run --name debbie --network mired -ti debian

Attach containers to local network card
docker network create [opciones de rango de red, pool de IPs, attachable .... ] nombredemired
docker network ls -f name=nombredemired  <------ miramos el ID alfanumérico
brctl addif br-IDAlfanumérico MITARJETADERED
Ejecutar contenedores con "--network nombredemired"

# volumenes
docker volume create ejemplo
ls -la /var/lib/docker/volumes/ejemplo/_data
docker run -ti -v ejemplo:/misdatos centos

# Docker compose

Install: https://docs.docker.com/compose/install/#install-compose

Docker compose siempre eversion 3 para que sea compatible con SWRM

docker run -d -e MYSQL_ROOT_PASSWORD=tupassword --name mibbdd mysql:5.7
docker run --name some-wordpress --link mibbdd:mysql -p 8080:80 -d wordpress

Se pueden usar variables de entorno entre container si se ha hecho link, ejemplo en wordpress docker

docker-compose scale container-name=10


# Docker swarm 

Creamos un cluster para decir que vamos a meterle con docker stack
docker swarm init

Par saber como hacer join:
docker swarm join-token manager

Devuelve algo tal que así
docker swarm join --token SWMTKN-1-290chska8oa8hw0egopwar2cv1y4n2lwjyf6ck1alcfy2oh6sn-b4dbarhz2gyqly2t17cr4z061 192.168.20.61:2377

ocker stack: Despliega el compose en el cluster
  docker stack deploy -c docker-compose.yml wp_stack

docker services: Lista servicios del cluster
  docker service logs wp_stack_wordpress
  docker service ls
  docker node ls
  docker service scale wp_stack_wordpress=3
  docker stack ls
  
  docker node update -->> Asigna labels para poder hacer deploy constraints 
  



App portainer para desplegar containers
