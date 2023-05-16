# Docker
```docker
# verify docker is installed
docker version
```
Now that Docker is installed, run a test image: 
```bash
docker run nginx
```
If you do not already have an nginx image in the local docker registry, docker will pull the latest image from online. 

## Docker Registry

Docker Registry is where you can story your images or pull official images from. The Registry can be a privately held registry as well.

## Key Commands

```docker
# Set alias in PowerShell
Set-Alias d docker
# Build 
docker build . # builds from your local path
# Deploy
docker run . # runs the Dockerfile from where you are
```

## Concepts
You're building a set of instructions for each command you need to run to build your web-app. 
```docker
FROM # specify base image
WORKDIR # specify working directory
COPY # files
ADD # files
RUN # executing os commands 
ENV # env var's
EXPOSE # ports
USER # user 
CMD # commands to execute during boot
ENTRYPOINT # where to place yourself after install
```

## Container Controls with CLI 
```bash
# install image
docker install nginx

# pulls image and runs a container with that image
docker run nginx

# list running containers
docker ps

# shows running and stopped containers
docker ps -a 

# stop a container
docker stop some-container-name

# remove a container
docker rm some-container-name

# remove registry image
docker images # list all images
docker rmi image-name # delete image

# Container Sleep
docker run nginx sleep 5 # in seconds

# execute a command on a running container
docker exec container-name cat /etc/hosts

# run image as attached and detached
docker run nginx # attached to command window
docker run -d nginx # runs detached in the background, not attached to terminal
docker attach container-id # re-attach terminal to running container

# build a container from local repository and add a name
docker build . -t my-app-name # . sets local repo with -t setting name:tag

```

## Log into docker container 
```bash
# to automatically log into the container after running an image, add -it flag
docker run -it nginx bash # runs bash terminal within the container with nginx image
```

---
# Run
```bash
# basic command to run an image
docker run redis # or any image name

# specify an image tag, so it doesn't pull latest by default
docker run redis:1.1 # 1.1 being the tag

# interactive mode
docker run -i redis

# attach terminal in interactive mode
docker run -it redis

# port map
docker run -p 80:5000 redis # routes 80 public to 5000 internal

# volume map
docker run -v /opt/datadir:/var/lib/mysql mysql

# details of a container
docker inspect container-name

# container logs
docker logs container-name
```

---
# Custom Image
A docker file `Dockerfile` is just a set of instructions to run a “process” as a container image.
## Instruction Format
```DOCKERFILE
INSTRUCTION ARGUMENT
FROM        Ubuntu
RUN         apt-get update
```

Here's the basics of a Dockerfile, which you will 'run' after creation. 
```DOCKERFILE
# sample flask application 
FROM Ubuntu
RUN apt-get update
RUN apt-get install python

RUN pip install flask
RUN pip install flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```
then, you'll build it
'docker build Dockerfile -t repouser/my-custom-app'

and, push it to docker registry
'docker push repouser/my-custom-app'

## Custom Image Workflow
It's smart to develop a readme of all configurations necessary for a custom image. Open a terminal and run your initial image in interactive terminal mode so you can manually run each command and see what fails live so you can properly build.
```bash
# interactive mode to test build your image 
docker run -it ubuntu bash

# sample terminallook once you are running inside of terminal
root@df5893475978:

# install updates
root@df5893475978: apt-get update

# view install history
root@df5893475978: history # <-- use this to view your terminal history
1 apt-get update
2 clear
3 apt-get install -y python
4 clear
5 ...
```

using the history command, build your DOCKERFILE
```DOCKERFILE
FROM Ubuntu

RUN apt-get update
RUN apt-get install -y python
...

```
---
# build
It's important to note that any options you'll pass in the CLI will go before the image name and tag. 
```bash
docker build -OPTIONS imageName:Tag

# example
docker build -p 8282:8080 my-web-app:v2
```
---
# inspect
```bash
docker inspect containerName
[
	{
		"ID": "",
		"State": {
			"Status": "",
			"Running": true,
		},
	}
]
```
---
# compose
Creates a configuration file to combine the various services to run the image. 

## Base Command for Docker Compose
'docker-compose up'

## Sample Docker Compose File
```yaml
services:
	web:
		image: "userprofile/simple-webapp"
	database:
		image: "mongodb"
	messaging:
		image: "redis:alpine"
```

```yml
# version 1
redis:
	image: redis
db:
	image: postgres:9.4
vote: 
	image: voting-app
	ports:
		- 5000:80
	links:
		- redis
---

docker-compose up
```

```bash
# version 2 docker compose
version: 2
services:
	redis:
		image: redis
	...
	vode:
		image: voting-app
		ports:
			- 5000:80
		depends_on: # depends on was added in v2
			- redis
```

The old method: 
```bash
# docker cmd detached -- application name and options
docker run -d --name=redis redis
docker run -d --name=db postgres
docker run -d --name=vote -p 5000-80 voting-app
docker run -d --name=result -p 5001:80 result-app
docker run -d --name=worker worker
```

The problem, you're not defining replica counts, and other various details. 
## docker run --links 
*deprecated*
```bash
docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
```






#containers #cloudnative 