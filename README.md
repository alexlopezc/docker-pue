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

# volumenes
docker volume create ejemplo
ls -la /var/lib/docker/volumes/ejemplo/_data
docker run -ti -v ejemplo:/misdatos centos

# Docker ocmpose

Install: https://docs.docker.com/compose/install/#install-compose
Docker cmpose isempr eversion 3 para que sea compatible con SWRM

docker run -d -e MYSQL_ROOT_PASSWORD=tupassword --name mibbdd mysql:5.7
docker run --name some-wordpress --link mibbdd:mysql -p 8080:80 -d wordpress

Se pueden usar variables de entorno entre container si se ha hecho link, ejemplo en wordpress docker

