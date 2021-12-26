## Installation with Docker ###
You should have Docker installed (if not, please refer to "Get Docker"), 
and you can create a Docker network to make containers communicate with each other. 
TARS can be run on physical machines, virtual machines, and containers, and it will work in the same way regardless of the environment that you choose.


# Create a bridge virtual network and set the name, subnet and gateway.
```
docker network create -d bridge --subnet=172.25.0.0/16 \
--gateway=172.25.0.1 tars

```

# Deploy TARS Framework with Docker: Start MySQL #

To start MySQL, run the following command in your terminal:

```
docker run -d \ 
    --net=tars \ 
    -e MYSQL_ROOT_PASSWORD="123456" \ 
    --ip="172.25.0.2" \ 
    -v /data/framework-mysql:/var/lib/mysql \ 
    -v /etc/localtime:/etc/localtime \ 
    --name=tars-mysql \ 
    mysql:5.6
    
```

# Deploy TARS Framework with Docker: Run tarscloud/framework Docker #

You are now ready to deploy the TARS framework with Docker.

Start by pulling Docker image:

```
docker pull tarscloud/framework:v2.4.0

```

Next, run Docker image:

# Mount timezone file /etc/localtime to container. Remove it if you don't have.
# expose port 3000 for web entry
# expose port 3001 for web authorization

```
docker run -d \
    --name=tars-framework \
    --net=tars \
    -e MYSQL_HOST="172.25.0.2" \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e MYSQL_USER=root \
    -e MYSQL_PORT=3306 \
    -e REBUILD=false \
    -e INET=eth0 \
    -e SLAVE=false \
    --ip="172.25.0.3" \
    -v /data/framework:/data/tars \
    -v /etc/localtime:/etc/localtime \
    -p 3000:3000 \
    -p 3001:3001 \
    tarscloud/framework:v2.4.0
    
```

Parameter Interpretation

MYSQL_HOST
IP of MySQL DB instance.
MYSQL_ROOT_PASSWORD
Root password for MySQL.
MYSQL_USER
MySQL user uses root as default.
MYSQL_PORT
Self-explanatory: MySQL port.
REBUILD
This parameter is usually set to false. However, if there is an error in the intermediate installation and you want to reset the database, you can set it to true.
INET
The name of the network interface (such as eth0, as you can see in ifconfig) indicates the native IP bound by the framework (note that it cannot be 127.0.0.1 - it’s a rule written in the installation script).
SLAVE
It refers to a slave node for TARS framework. If you need TARS framework’s high-availability, you should run a second tars-framework container with SLAVE=ture (to learn more about the master/slave model, see Wikipedia).
Directory Interpretation

The directory of Docker (/data/tars) will be mapped to the host directory (/data/framework). Please check the /data/framework directory after starting Docker to confirm that the following directories have been created:

app_log
Log directory of framework servers.
tarsnode-data
/usr/local/app/tars/tarsnode/data is a directory within Docker that stores data about servers published to Docker. It ensures that data won’t be lost when we restart Docker.
web_log
These are the web logs of the tars-node-web module that are only available to the master framework node.
demo_log
These are the web logs of the tars-user-system module that are only available to the master framework node.
patchs
It uploads a release package and is only available for the master framework node.
If any of these directories are missing, you will have to create it manually and restart Docker.

Enter http://${your_machine_ip}:3000 to access your TarsWeb. TarsWeb is a web management system that monitors the runtime status of servers and starts, stops, deploys, and publishes servers.
