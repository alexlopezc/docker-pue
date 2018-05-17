Consul service discovery


docker run -d --net=host -e 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' consul agent -server -bind=192.168.20.201 -client=0.0.0.0 -ui  -bootstrap

Registrator:
    docker run -d --name=registrator --net=host --volume=/var/run/docker.sock:/tmp/docker.sock gliderlabs/registrator:latest consul://localhost:8500
    
    
    -------------------------------
    
    docker run -d --name nginx1 -P nginx
docker run -d --name webpersonal -P nginx
docker run -d -e SERVICE_NAME=webmama  --name webmama -P nginx
dig SRV -p 8600 @localhost webmama.service.dc1.consul +short
dig A -p 8600 @localhost webmama.service.dc1.consul +short
dig A -p 8600 @localhost nginx.service.dc1.consul +short
dig SRV -p 8600 @localhost nginx.service.dc1.consul +short


---------------------

Descarga https://releases.hashicorp.com/consul-template/0.19.4/consul-template_0.19.4_linux_amd64.tgz en un servidor con Docker.


consul-template -template "tuplantilla.ctmpl:/tmp/archivo-que-genero.txt:comando-que-lanzo"

{{range services}}{{if (contains "frontal" .Tags)}}{{.Name}} {{end}}{{end}}

consul-template -consul-addr=http://localhost:8500 -dry -once -template="/root/plantilla.ctmpl:/tmp/listado"




