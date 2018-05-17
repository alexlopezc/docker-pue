En el anfitrión:

apt install nfs-kernel-server
mkdir /srv/nfs
chmod 1777 /srv/nfs

vi /etc/exports. Afegeix:
-----------------------------
/srv/nfs        192.168.20.201(rw,no_subtree_check,no_root_squash) 192.168.20.202(rw,no_subtree_check,no_root_squash) 192.168.20.203(rw,no_subtree_check,no_root_squash)
-----------------------------
service nfs-server restart

------------------------------------------------------------------------
En las VMs

apt install nfs-common
service rpcbind restart
mkdir /srv/nfs

vi /etc/fstab, añade:
-----------------------------
TUIPDELANFITRION:/srv/nfs    /srv/nfs    nfs    nfsvers=3,hard,intr    0    0

En mi caso: 192.168.20.200:/srv/nfs    /srv/nfs    nfs    nfsvers=3,hard,intr    0    0

-----------------------------

mount -a
-----------------------------------------------------------------------------------------------------------------------------------------------------
Ja tens llesta la infraestructura NFS?+1+1+1+1+1+1+1+1+1+1+1+1

Prova a modificar el teu Stack de "Wordpress" per tal que empri  el NFS per a la persistència de dades, executa'l i valida que funciona.

---------------------------------------------------

Demostració de funcionament "productiu".

En l'amfitrió:

apt install nginx

Modifica /etc/nginx/sites-available/default :
    
    upstream backend {
        server 192.168.20.201:80;
        server 192.168.20.202:80;
        server 192.168.20.203:80;

}


server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name _;

        location / {
                proxy_pass      http://backend;
                proxy_set_header Host $host;
        }
}
------------------------------------------------------------------------

service nginx reload

-----------------------------------------------------------------------------------------------

    version: '3.1'


    services:


      web:

        image: php:7.0-apache

        volumes:

          - /srv/nfs/miweb:/var/www/html

        ports:

          - 80:80

        deploy:

          replicas: 1

------------------------------------------

En /srv/nfs/miweb, creamos index.php con:

    <?php phpinfo();
-----------------------------------------------------------------------------------------------------------------------------------
TIG Stack para métricas:
    
version: '3.4'

services:
  influxdb:
    image: library/influxdb:latest
    command: -config /etc/influxdb/influxdb.conf
    environment:
      - INFLUXDB_ADMIN_USER="admin"
      - INFLUXDB_ADMIN_PASSWORD="admin"
    volumes:
      - ${REMOTE_MOUNT}/metrics/influxdb/data:/var/lib/influxdb
      - ${REMOTE_MOUNT}/metrics/influxdb/config:/etc/influxdb
    deploy:
      replicas: 1

  grafana:
    image: grafana/grafana
    ports:
      - 3000:3000
    environment:
      GF_INSTALL_PLUGINS: 'grafana-clock-panel,grafana-piechart-panel,grafana-simple-json-datasource'
    volumes:
      - ${REMOTE_MOUNT}/metrics/grafana/data:/var/lib/grafana/
    depends_on:
      - influxdb
    deploy:
      replicas: 1


#   Fancy replacing placement on a static node. Add Net-storage and drop this out

  telegraf:
    image: kpeiruza/telegraf
    environment:
      HOST_PROC: '/rootfs/proc'
      HOST_SYS: '/rootfs/sys'
      HOST_ETC: '/rootfs/etc'
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /sys:/rootfs/sys:ro
      - /proc:/rootfs/proc:ro
      - /run:/rootfs/run:ro
      - /etc:/rootfs/etc:ro
      - /etc/telegraf/:/etc/telegraf/
    depends_on:
      - influxdb
    deploy:
      mode: global
      restart_policy:
        condition: on-failure
        delay: 5s

------------------------------------------------------------------------------------------------------------------------------
influxdb.conf:
    
reporting-disabled = false
bind-address = "127.0.0.1:8088"

[meta]
  dir = "/var/lib/influxdb/meta"
  retention-autocreate = true
  logging-enabled = true

[data]
  dir = "/var/lib/influxdb/data"
  index-version = "inmem"
  wal-dir = "/var/lib/influxdb/wal"
  wal-fsync-delay = "0s"
  query-log-enabled = true
  cache-max-memory-size = 1073741824
  cache-snapshot-memory-size = 26214400
  cache-snapshot-write-cold-duration = "10m0s"
  compact-full-write-cold-duration = "4h0m0s"
  max-series-per-database = 1000000
  max-values-per-tag = 100000
  max-concurrent-compactions = 0
  trace-logging-enabled = false

[coordinator]
  write-timeout = "10s"
  max-concurrent-queries = 0
  query-timeout = "0s"
  log-queries-after = "0s"
  max-select-point = 0
  max-select-series = 0
  max-select-buckets = 0

[retention]
  enabled = true
  check-interval = "30m0s"

[shard-precreation]
  enabled = true
  check-interval = "10m0s"
  advance-period = "30m0s"

[monitor]
  store-enabled = true
  store-database = "_internal"
  store-interval = "10s"

[subscriber]
  enabled = true
  http-timeout = "30s"
  insecure-skip-verify = false
  ca-certs = ""
  write-concurrency = 40
  write-buffer-size = 1000

[http]
  enabled = true
  bind-address = ":8086"
  auth-enabled = false
  log-enabled = true
  write-tracing = false
  pprof-enabled = true
  https-enabled = false
  https-certificate = "/etc/ssl/influxdb.pem"
  https-private-key = ""
  max-row-limit = 0
  max-connection-limit = 0
  shared-secret = ""
  realm = "InfluxDB"
  unix-socket-enabled = false
  bind-socket = "/var/run/influxdb.sock"

[[graphite]]
  enabled = false
  bind-address = ":2003"
  database = "graphite"
  retention-policy = ""
  protocol = "tcp"
  batch-size = 5000
  batch-pending = 10
  batch-timeout = "1s"
  consistency-level = "one"
  separator = "."
  udp-read-buffer = 0

[[collectd]]
  enabled = false
  bind-address = ":25826"
  database = "collectd"
  retention-policy = ""
  batch-size = 5000
  batch-pending = 10
  batch-timeout = "10s"
  read-buffer = 0
  typesdb = "/usr/share/collectd/types.db"
  security-level = "none"
  auth-file = "/etc/collectd/auth_file"

[[opentsdb]]
  enabled = false
  bind-address = ":4242"
  database = "opentsdb"
  retention-policy = ""
  consistency-level = "one"
  tls-enabled = false
  certificate = "/etc/ssl/influxdb.pem"
  batch-size = 1000
  batch-pending = 5
  batch-timeout = "1s"
  log-point-errors = true

[[udp]]
  enabled = false
  bind-address = ":8089"
  database = "udp"
  retention-policy = ""
  batch-size = 5000
  batch-pending = 10
  read-buffer = 0
  batch-timeout = "1s"
  precision = ""

[continuous_queries]
  log-enabled = true
  enabled = true
  run-interval = "1s"

--------------------------------------------------------------------------------------

telegraf.conf <---- posa'l a /etc/telegraf dels 3 nodes.

[global_tags]
[agent]
  interval = "10s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"
  precision = ""
  debug = false
  quiet = false
  logfile = ""
  hostname = "populated.by.this.docker.image"
  omit_hostname = false
[[outputs.influxdb]]
  urls = [ "http://influxdb:8086" ]
  database = "telegraf"
  retention_policy = ""
  write_consistency = "any"
  timeout = "5s"
[[inputs.cpu]]
  percpu = true
  totalcpu = true
  collect_cpu_time = false
[[inputs.disk]]
  ignore_fs = ["tmpfs", "devtmpfs", "devfs"]
[[inputs.diskio]]
[[inputs.kernel]]
[[inputs.mem]]
[[inputs.processes]]
[[inputs.swap]]
[[inputs.system]]
[[inputs.conntrack]]
    files = ["ip_conntrack_count","ip_conntrack_max",
             "nf_conntrack_count","nf_conntrack_max"]
    dirs = ["/proc/sys/net/ipv4/netfilter","/proc/sys/net/netfilter"]
[[inputs.docker]]
   endpoint = "unix:///var/run/docker.sock"
   timeout = "5s"
[[inputs.kernel_vmstat]]
[[inputs.net]]
[[inputs.netstat]]
[[inputs.ping]]
   count = 1
   timeout = 1.0







# Alex Swarm manager

docker swarm join --token SWMTKN-1-290chska8oa8hw0egopwar2cv1y4n2lwjyf6ck1alcfy2oh6sn-b4dbarhz2gyqly2t17cr4z061 192.168.20.61:2377

Edu swarm:
manager: docker swarm join --token SWMTKN-1-67qyj4jyeit8s7gbxbh3it9ijezq74dzbz7pnudwqyjh90v3yr-5390i7h3a3xd6g03s6yfw9a7p 192.168.20.46:2377
worker: docker swarm join --token SWMTKN-1-67qyj4jyeit8s7gbxbh3it9ijezq74dzbz7pnudwqyjh90v3yr-acmhxs3f5hsh9icyqsfj091gh 192.168.20.46:2377


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

#       Docker Stack to deploy ELK + Logspout
#       Based on .......
#       Updated by: Kenneth Peiruza, kenneth@floss.cat
#       Sun Mar  4 13:15:47 CET 2018
#
#      cluster.name: 'docker-cluster'
#      bootstrap.memory_lock: 'true'
version: '3.4'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch-oss:6.2.2
    environment:
      ES_JAVA_OPTS: '-Xms768m -Xmx768m'
    volumes:
      - /srv/docker/data/logs/elasticsearch/data:/usr/share/elasticsearch/data
    deploy:
      replicas: 1

  logstash:
    image: docker.elastic.co/logstash/logstash-oss:6.2.2
    volumes:
      - /srv/docker/data/logs/logstash/config:/usr/share/logstash/pipeline/
    depends_on:
      - elasticsearch
    deploy:
      replicas: 1
#   Fancy replacing placement on a static node. Add Net-storage and drop this out

  logspout:
    image: bekt/logspout-logstash
    environment:
      ROUTE_URIS: 'logstash://logstash:5000'
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - logstash
    deploy:
      mode: global
      restart_policy:
        condition: on-failure
        delay: 30s
# XPACK_SECURITY_ENABLED: 'false' XPACK_MONITORING_ENABLED: 'false'
  kibana:
    image: docker.elastic.co/kibana/kibana-oss:6.2.2
    ports:
        - 5601:5601
    depends_on:
      - elasticsearch
    environment:
      ELASTICSEARCH_URL: 'http://elasticsearch:9200'
    deploy:
      replicas: 1

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

logstash/config/logstash.conf 

input {
  udp {
    port  => 5000
    codec => json
  }
}

filter {
  if [docker][image] =~ /logstash/ {
    drop { }
  }
}

output {
  elasticsearch { hosts => ["elasticsearch:9200"] }
}

-------------------------------------------------------------------------------
Panel de control Portainer:
    
version: '3'


services:
  portainer:
    image: portainer/portainer
    ports:
      - 9000:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /srv/docker/data/portainer/portainer/data:/data
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]

---------------------------------------------------------------------------------------------

version: '3'


services:
  portainer:
    image: portainer/portainer
    ports:
      - 9000:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer:/data
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints:
          - node.hostname == node1.swarm.lan
volumes:
  portainer:

---------------------------------------------------------------------------------------------

version: '3.4'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch-oss:6.2.2
    environment:
      ES_JAVA_OPTS: '-Xms768m -Xmx768m'
    networks:
      - default
      - elasticsearch
    volumes:
      - /srv/docker/data/logs/elasticsearch/data:/usr/share/elasticsearch/data
    deploy:
      replicas: 1

  logstash:
    image: docker.elastic.co/logstash/logstash-oss:6.2.2
    volumes:
      - /srv/docker/data/logs/logstash/config:/usr/share/logstash/pipeline/
    depends_on:
      - elasticsearch
    networks:
      - default
      - logstash
    deploy:
      replicas: 1
#   Fancy replacing placement on a static node. Add Net-storage and drop this out

  logspout:
    image: bekt/logspout-logstash
    environment:
      ROUTE_URIS: 'logstash://logstash:5000'
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - logstash
    networks:
      - default
    deploy:
      mode: global
      restart_policy:
        condition: on-failure
        delay: 30s
# XPACK_SECURITY_ENABLED: 'false' XPACK_MONITORING_ENABLED: 'false'
  kibana:
    image: docker.elastic.co/kibana/kibana-oss:6.2.2
    depends_on:
      - elasticsearch
    networks:
      - default
      - proxy
    environment:
      ELASTICSEARCH_URL: 'http://elasticsearch:9200'
    deploy:
      replicas: 1
      labels:
        traefik.port: 5601
        traefik.frontend.rule: "Host:TUURL.DEL.KIBANA"
        traefik.docker.network: "proxy"


networks:
  default:
    driver: 'overlay'
  proxy:
    external: true
  logstash:
    external: true
  elasticsearch:
    external: true

-----------------------------------------------------------------------------------------------------
PORTAINER:

version: '3.4'

services:
  portainer:
    image: portainer/portainer
    networks:
      - proxy
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ${REMOTE_MOUNT}/portainer/portainer/data:/data
    deploy:
      mode: replicated
      replicas: 1
      labels:
        traefik.port: 9000
        traefik.frontend.rule: 'Host:portainer.${SWARM_DOMAIN}'
        traefik.docker.network: 'proxy'
      placement:
        constraints: [node.role == manager]

networks:
  proxy:
    external: true
    
    ----------------------------------------------------------------------------------------------------------------------

Docker Registry ----> El servidor de imágenes

docker run -d -p 5000:5000 --restart=always -v /srv/registry:/var/lib/registry --name registry registry:2

https://docs.docker.com/registry/deploying/#copy-an-image-from-docker-hub-to-your-registry


root@node1:/srv/nfs/LocalStacks# cat /etc/docker/daemon.json 
{
        "dns": [ "8.8.8.8" ],
        "insecure-registries": [ "192.168.20.201:5000" ]

}

docker tag lamevadebian:1.0 192.168.20.201:5000/debiancustom:1.0
docker push 192.168.20.201:5000/debiancustom:1.0

------------------------------------------------------------

Configurar servidor "registry" con HTTPS y autenticación: https://docs.docker.com/registry/recipes/nginx/#setting-things-up
Pruebas GitLab
Pruebas Portus


DS automatico con consul de los nuevos servicios levantados

apt install bind9
root@node2:/etc/bind# cat /etc/bind/named.conf.local 
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "consul" {
	type forward;
	forward only;
	forwarders {
		127.0.0.1 port 8600;
	};
};

root@node2:/etc/bind# cat /etc/docker/
daemon.json  key.json     

root@node2:/etc/bind# cat /etc/docker/daemon.json 
{
	"dns": [ "192.168.20.62" ]
}




