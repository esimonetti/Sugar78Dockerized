# Sugar 7.8 Dockerized
This repository will help you deploy a Docker based development full stack for Sugar 7.8 meeting all the platform requirements.

## Running the stack
* Run the stack with `docker-compose up -d`
* Stop the stack with `docker-compose down`

## System's details
* Browser url: http://localhost/sugar/
* MySQL hostname: sugar78-mysql
* MySQL user: root
* MySQL password: root
* MySQL is accessible from the host on port 3306 using ip 127.0.0.1 (not localhost)
* Elasticsearch hostname: sugar78-elasticsearch
* Redis hostname: sugar78-redis

## Core stack components
* Linux
* Apache
* Mysql
* PHP
* Redis
* Elasticsearch

## Docker containers
* Apache load balancer
* Two Apache web servers behind the load balancer
* Cronjob server
* MySQL database
* Elasticsearch
* Redis

## File locations
* The Sugar application files served from the web servers and leveraged by the cronjob server are located in ./data/app/sugar/. Within the web servers and the cronjob server the location is /var/www/html/sugar/
* MySQL files are located in ./data/mysql
* Elasticsearch files are located in ./data/elasticsearch
* Redis files are located in ./data/redis

## Challenges and lessons learned
### General challenges
* Connecting to MySQL from my host to the container, would not work with: `mysql -u root -p -P 3306 -h localhost` but it would instead with: `mysql -u root -p -P 3306 -h 127.0.0.1`. This has something to do with sockets on the host
* Zend Opcache is not loaded unless specified on the configuration file with `zend_extension=opcache.so`. Once doing so, the Docker log outputs an error where Opcache has been loaded multiple times, but at this point is it actually loaded.

### Possible OSX specific challenges
* File system slowness
* Network "weirdness" as documented on https://docs.docker.com/docker-for-mac/networking/#known-limitations-use-cases-and-workarounds

### The problems I encountered during the wizard installation are below
* AllowOverride error
This was due to the fact that I mapped port 8080 on my local machine to 80 of the container. When connecting to localhost:8080, Sugar completes a curl request to localhost:8080 to verify the rewrite rules. The local port 8080 is closed from and the call fails, making the application think that the rewrite rules are not working, while they are actually working. To overcome the issue you have to shutdown your local apache and map port 80 on the local machine to port 80 of the container.
* Timeout error
If Apache times out during installation due to file system slowness especially on OSX, increase the timeout. It is already set on Dockerfile of the web and on the lb containers to 600 seconds, and that should be plenty.

## Mentions
Based on the initial work from Cédric Mourizard (https://github.com/cmourizard/sugar-docker-example)
