
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

########################################################################
# COMPOSE
########################################################################
http://localhost:9000/  (portainer admin/123abc!!)
# run portainer container
docker run -d -p 9000:9000  -v "/var/run/docker.sock:/var/run/docker.sock" portainer/portainer

# Start ALL docker containers as deamons (use --build if there are changes in compose's configurations):
cd /home/jdi/projects/igc-projects
sudo docker-compose up --build -d

# Start ALL docker containers and see output:
sudo docker-compose up

# Start docker container as user 1000, and see output:
sudo docker exec -it --user 1000 igc-web bash
# Start docker container as root, and see output:
sudo docker exec -it --user root txc-web bash

# Build container again if configuration is changed????:
sudo docker-compose up --build  nodejs
sudo docker-compose up --build  redis

# stop docker containers:
sudo docker-compose stop

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


