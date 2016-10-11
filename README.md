Service Fabric on Docker
====================
----------
## Install Docker
Follow this [link](https://docs.docker.com/engine/installation/) to Docker official website to install Docker. For Ubuntu 16.04 distribution, you mainly need the follow the commands below.

> - `sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D`
> - `sudo vim /etc/apt/sources.list.d/docker.list`
> - *Copy paste the line below and save*

> `deb https://apt.dockerproject.org/repo ubuntu-xenial main`
> - `sudo apt-get update`
> - `sudo apt-get install docker-engine`
> - `sudo service docker start`

#### For Microsoft Internal only
Microsoft IT blocked the Google public DNS address 8.8.8.8. The Docker Ubuntu base image could not resolve DNS. You will need to change the systemd config to use the Microsoft internal DNS. Follow the steps below to config the DNS. For more details, please refer to this [doc](https://docs.docker.com/engine/admin/systemd/#custom-docker-daemon-options).

> - `sudo mkdir /etc/systemd/system/docker.service.d`
> - `sudo vim /etc/systemd/system/docker.service.d/docker.conf`
> - *Copy paste the config block below and save*
~~~~
[Service]
EnvironmentFile=-/etc/sysconfig/docker
EnvironmentFile=-/etc/sysconfig/docker-storage
EnvironmentFile=-/etc/sysconfig/docker-network
ExecStart=
ExecStart=/usr/bin/docker daemon -H fd:// $OPTIONS \
          --dns=10.222.118.22 \
          $DOCKER_STORAGE_OPTIONS \
          $DOCKER_NETWORK_OPTIONS \
          $BLOCK_REGISTRY \
          $INSECURE_REGISTRY
~~~~
> - `sudo systemctl daemon-reload`
> - `sudo systemctl restart docker`

## Run Service Fabric on Docker

Use **one** of the commands below to run Service Fabric on Docker.

> - `sudo docker run -it -P msjeffreychen/azure-service-fabric`
> - `sudo docker run -it -p 19080:19080 msjeffreychen/azure-service-fabric`

`-P` (uppercase) : publish all exposed ports, this will open both 19000 and 19080 ports, but Docker will map to different ports in the host machine to avoid conflicts

`-p` (lowercase) : publish a exposed port, this will open a port based on the specified value, -p 1000:2000 means map from port 1000 in the host machine to port 2000 in the container

> **Tip:**
> To see which ports in the host machine Docker chose when you used the -P (upper) option, you could run `sudo docker ps`. You will see a column **PORTS**. `0.0.0.0:32770->19080/tcp` means mapped from port 32770 in the host machine to port 19080 in the container.

After the container starts up, you could connect to the cluster using the Azure CLI.

> azure servicefabric cluster connect http://localhost:32770
