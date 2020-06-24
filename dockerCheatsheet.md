# Basic Tutorial  
* detach from the container shell and return to host, the container still runs in the background
Ctrl+p Ctrl+q  
```
>docker container run -it —name=bb busybody:1.26

# -d flag to run the container in the background
>docker container run -it -d —name=bb busybody:1.26

# detach from the container shell and return to host, the container still runs in the background
Ctrl+p Ctrl+q

# re-attach to a running container -> return to it’s shell
docker container attach bb

# stop the container
docker container stop bb

# if container re-run with same run command error:
docker container run -it —name=bb busybody:1.26
docker: Error response from daemon: Conflict. The container name "/bb" is already in use by container "38d9ee93109a4c9fc5de4b46bce60d3ad2d5fee2d8b4fa776e04709502cddbf5". You have to remove (or rename) that container to be able to reuse that name.

# start the container in the background and then attach to it
docker container start bb

# list running containers
docker container ls
docker container ls -a

# remove container
docker container rm bb
```

```bash
# alternative top
docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock quay.io/vektorlab/ctop:latest

########################################################################
# COMMANDS
########################################################################

docker run -t -i centos /bin/bash	# run shell interactively

docker build -t ouruser/sinatra:v2 .	# build an image out of a Dockerfile in the current dir.

[sudo] docker [subcommand] [flags] [arguments] ..

# copy a file from a container (txgit_txgit_1) path to a host path!
docker cp txgit_txgit_1:/tmp/payload_many.txt ~/.

docker start <port_name> # restarts a previously stopped container
docker restart <port_name>	# restarts (stop+start) a running container
docker attach --help	# help for attach sub-command
docker ps 		# Lists running containers.
docker ps -a		# show all containers (started + stopped) of system + status:
docker logs 		# Shows the standard output of a container.
docker stop 		# Stops running containers.
docker rmi <IMAGE>	# remove image
docker rm <CONTAINER>	# remove container
docker pull <IMAGE>	# download (preload) image to host.
docker version  	# show version
docker port <port_name> <port>	# shows the public facing port
docker top <port_name> <port>	# shows the processes running inside the container
docker logs -f <port_name>	# tail the logs of the specified container
docker inspect --format='{{json .State.Health.Status}}' <containername>      # check container's health status

docker-machine ip	# show the ip of the host running docker

docker-machine ls       		# list machines
docker-machine ip <machine-name>	# display the ip of a machine in swarm
docker-machine ssh <machine-name>	# 
docker node ls				# list nodes in swarm

########################################################################
# Remove container the hard way
########################################################################
$ sudo systemctl stop docker
$ sudo rm -rf /var/lib/docker/containers/<container hash>
$ sudo systemctl start docker
```

# COMPOSE
```
http://localhost:9000/  (portainer admin/123abc!!)
# run portainer container
docker run -d -p 9000:9000  -v "/var/run/docker.sock:/var/run/docker.sock" portainer/portainer

# Start ALL docker containers as deamons (use --build if there are changes in compose's configurations):
cd /home/jdi/projects/igc-projects
sudo docker-compose up --build -d

# Start ALL docker containers and see output:
sudo docker-compose up

# list services
docker-compose ps

# Start docker container as user 1000, and see output:
sudo docker exec -it --user 1000 igc-web bash
# Start docker container as root, and see output:
sudo docker exec -it --user root txc-web bash

# Build container again if configuration is changed????:
sudo docker-compose up --build  nodejs
sudo docker-compose up --build  redis

# stop docker containers:
sudo docker-compose stop
# stop AND remove containers
docker-compose down

# watch docker containers status:
sudo watch -n 1 -d docker ps -a

# show installed docker images:
sudo docker images

```

# Network

You can also use ping or telnet to troubleshoot network connectivity issues!!
```
docker network ls

# Docker runs an internal DNS server configured automatically for containers that is available at 127.0.0.11 from within a container
ping -c 2 127.0.0.1

# to configure the DNS upstream resolution for a container
# —dns flag set the DNS upstream resolver for a container (if not configured uses Google’s public DNS servers)
# —dns-search flag sets the search domain for containers, lookups for host 1 will also lookup for host 1.example.com
>docker container run -it —dns=192.168.1.1 —dns-search=“example.com” —name=bb busybody:1.26

# to configure dns globally locate daemon.json (better through Docker Desktopt UI in Mac, Preferences > Daemon
# or (?) in ~/.docker/daemon.json file and the restart docker daemon to take effect!
{
    “dns” : [“192.168.1.1”, “192.168.1.2”],
    “dns-search” : “example.com”
}

# User-defined bridge networks are the preferred way to allow containers to connect to each other.
# By default containers in separate networks are isolated from each other.
# Docker provides DNS service discovery based on container’s name or network alias
# DNS service discovery is not available at the default bridge network -> must build custom bridge network!!!

# create 2 custom bridge networks to test connectivity:
docker network create --driver bridge frontend
docker network create --driver bridge backend
docker network ls

# now create two containers for each network, front runs in network frontend etc.
docker container run -it -d --network=frontend --name=front busybox:1.26
docker container run -it -d --network=backend --name=back busybox:1.26
# also create a 3rd container to test connectivity
docker container run -it -d --network=frontend --name=test busybox:1.26

# test isolation
docker attach test
/ # ping -c 2 front
PING front (172.25.0.2): 56 data bytes
64 bytes from 172.25.0.2: seq=0 ttl=64 time=0.291 ms

/ # ping -c 2 back
ping: bad address 'back'

# now we can add dynamically the container test to the backend network! On the host:
docker network connect backend test

~
➜ docker attach test
/ # ping -c 2 back
PING back (172.26.0.2): 56 data bytes
64 bytes from 172.26.0.2: seq=0 ttl=64 time=0.359 ms
```

# Alpine image
```
# install package on alpine image:
# 1. connect to the container as root user
docker exec -it --user root <container> bash
# 2. install the telnet package through apk:
apk update
apk add busybox-extras
```

# Build
```
# When a container is run based on an image it adds a thin R/W layer on-top of the image! When we remove the container we effectively delete this layer!

# Base image: The image defined in the FROM command of a Dockerfile.

docker build -t <image_name> -f <Dockerfile>  <build_dir>
# For example
docker build -t demo -f Dockerfile .

# SHELL instruction changes the default shell (eg in Windows when you want to use Powershell)

#use —no-cache to rebuild from start, especially useful in automated builds as a last step before building prod image!
docker build —no-cache …

# some shells like bash generate a build error this way and safeguard from a wrong build (set -o pipefail)
RUN /bin/bash -c “set -o pipefail && wget -0 - https://google/ | wc -l”

# Some commands like CMD, ENTRYPOINT, RUN have two forms:
* Shell form:  RUN touch /this-is-${HOSTNAME}
* Exec form   RUN [“touch”, “/this-is-${HOSTNAME}”]

# SOS, exec form (json format) RUN contrary to shell form doesn’t replace variables!!!
RUN touch /this-is-${HOSTNAME}
# but this doesn’t replace the env variable
RUN [“touch”, “/this-is-${HOSTNAME}”]

# always put apt-get update in the same command with the packages!
RUN apt-get update && \
         apt-get -y install nginx …

CMD / ENTRYPOINT

* Can use both shell or exec form
* Specify a default command to run when a container starts, NOT during build
* CMD can only be specified once
* CMD can be overridden altogether with arguments of docker run
* Use ENTRYPOINT with exec form (json), it’s better for server software since it does not pass the cmd through the shell (/bin/sh -c)
* You can override the ENTRYPOINT in Dockerfile with —entrypoint flag when executing docker container run

# example usage of ENTRYPOINT:
FROM …
ENTRYPOINT [“ls”]

# when container is run you can pass additional arguments to ENTRYPOINT to list the contents of a different dir than worker:
docker container run -it demo /var/log

* ENTRYPOINT and CMD can be used together in exec form . In this way we pass arguments to ENTRYPOINT with the CMD:
FROM…
ENTRYPOINT [“echo”]
CMD [“hello”, “world”]

Exposing & Publishing ports

#use -P to publish all exposed ports in Dockerfile in random ports of the host
docker container run -it -P demo

# use -p <container_port> to publish selected exposed ports in Dockerfile in random ports of the host
# you can use -p multiple times in run command
docker container run -it -p 3000 demo

# use -p <host_port>:<container_port> to publish a selected exposed port in Dockerfile in a specific port of the host
docker container run -it -p 80:3000 demo

Environment variables

ENV variable_name1 variable_value1
ENV variable_name2 variable_value2
…

# or in the same line via using =
ENV variable_name1=variable_value1 variable_name2=variable_value2

# environment variables cannot be used in RUN, CMD, ENTRYPOINT shell form since they are resolved by shell at runtime!!!

# pass environment variables when running a container using the -e flag:
docker container run -it -e  variable_name2=variable_value2 <image_name>

# to avoid typing a lot of env vars using -e, define them in a file and pass it with the —env-file flag:
docker container run -it —env-file <env file path> <image_name>

# sample env file:
VAR1=VALUE1
VAR2=VALUE2
…

Build arguments
# Are used during build time and can be override from build command with —build-arg <name>=<value>
docker container build -t <container_name> —build-arg <name>=<value2> -f Dockerfile .

# in Dockerfile
…
ARG <name>=<value1>
```

# Volumes
```
docker volume ls
docker volume inspect <volume_name>
docker volume rm <volume_name>

# use the —mount option instead of the older -v:
docker container run -rm -it —mount type=bind,source=/tmp/data,dst=/data <image_name>

# the type=bind mounts a host directory from source to the dst (destination) directory on the container. The volume IS NOT destroyed when the container is removed!
```

# Logs
```
docker container logs -f <container_name>
docker container logs —tail N <container_name>
docker container logs -f —tail N <container_name>
# show the last minute logs
docker container logs -f —since 1m <container_name>
```

# Other commands
```
# exec command executes our command inside the container:
docker exec [options] <container_name> COMMAND [ARG…]
docker exec -d <container_name> touch /tmp/readme.txt
docker top <container_name>
docker stats [options]
# extract from inspect’s json output a specific node!!!
docker inspect <container_name> | jq “[].Mounts”

# update a container's restart policy
docker update --restart=no <container_id>
```

# Various
```
# Multiple configurations:
# use multiple yml conf files to override values in previous yml files (ie for different environments dev, test etc or to define
Different networks for dev and production)
docker-compose up -f <main compose yml> -f <2nd compose yml> -f <another yml> build

# PROJECT NAME!!:
# project name is by default the folder that contains the application eg txgit. By effect all container, network and volume names
# are prefixed by the project name eg txgit_worker etc
# To override the project name (default is the folder name of the compose file) pass the -p flag (txgit_worker -> transifex_worker):
docker-compose up -p transifex -d
# or alternatively:
COMPOSE_PROJECT_NAME=transifex docker-compose up -d

# To stop/down you MUST use the project name again
docker-compose stop -p txserver
# or alternatively:
COMPOSE_PROJECT_NAME=transifex docker-compose stop
```
