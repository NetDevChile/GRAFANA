# HOW TO CONFIGURE GRAFANA (USING DOCKER)
Show in real time the data metrics and let you analyze the data

###################### creamos Usuario Grafana ###########

```
groupadd grafana -g 472
useradd grafana -u 472 -g 472 -m -s /bin/false
```

####################### Creamos Directorios ##############

```
mkdir -p /docker-data/grafana/etc
mkdir -p /docker-data/grafana/scripts
mkdir -p /docker-data/grafana/log

chown -R grafana.grafana /docker-data/grafana

docker run -d --name "grafana" grafana/grafana 
docker cp grafana:/etc/grafana/grafana.ini /docker-data/grafana/etc
docker cp grafana:/usr/share/grafana/public/dashboards .
docker cp grafana:/var/lib/grafana /docker-data/grafana/lib
chown grafana.grafana lib
chmod 755 lib
cd /docker-data/grafana/lib
chown -R grafana.grafana *
chmod 640 grafana.db
chmod 755 plugins png
docker stop grafana
docker rm grafana
```

################## Configurar docker-compose ################

```
nano /docker-data/grafana/docker-compose.yml
```

----------------- Archivo docker-compose.yml ----------------

```
version: '3.8'
services:
 grafana:
   container_name:  grafana
   image: grafana/grafana
   restart: unless-stopped
   environment: 
      - TZ="America/Santiago"
   volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./etc:/etc/grafana
      - ./lib:/var/lib/grafana
      - ./log:/var/log/grafana
      - ./dashboards:/usr/share/grafana/public/dashboards
   environment: 
      - "GF_SECURITY_ADMIN_PASSWORD=password"
      - "GF_SECURITY_ADMIN_USER=admin"
      - "GF_SECURITY_SECRET_KEY=password"
   ports:
      - 3000:3000
   networks:
      - dockerlink
networks:
 dockerlink:
   external:
     name: dockerlink
```

################ Iniciamos Compose #############

```
cd /docker-data/grafana
docker-compose up -d
```

############## Entrar a Grafana ##########

```
http://localhost:3000/


usuario: admin
password: admin
```

############ configurar Data Sources con base de dato influxdb v1 ############

```
name : InfluxDB/Telegraf
query language: InfluxQL
Url: http://influx1:8086
Access: Server

Database : telegraf
User: telegraf_r
Password: password
```
