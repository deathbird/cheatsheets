
```bash
# alternative top
docker run --rm -ti -v /var/run/docker.sock:/var/run/docker.sock quay.io/vektorlab/ctop:latest

########################################################################
# COMMANDS
########################################################################

docker run -t -i centos /bin/bash	# run shell interactively

docker build -t ouruser/sinatra:v2 .	# build an image out of a Dockerfile in the current dir.

[sudo] docker [subcommand] [flags] [arguments] ..

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

# Start docker container , as user 1000, and see output:
sudo docker exec -it --user 1000 igc-web bash

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



