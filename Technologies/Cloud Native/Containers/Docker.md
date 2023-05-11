```docker
# verify docker is installed
docker version
```
Now that Docker is installed, run a test image: 
```bash
docker run nginx
```
If you do not already have an nginx image in the local docker registry, docker will pull the latest image from online. 

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
```


# Custom Image
A docker file `Dockerfile` is just a set of instructions to run a “process” as a container image.

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

#containers #cloudnative 