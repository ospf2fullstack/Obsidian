```docker
# verify docker is installed
docker version
```

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