# Docker Cheat Sheet

Here in are some useful commands for docker related operations.

### Run an image

```bash
docker run 
```

link[https://docs.docker.com/engine/reference/run/]

some useful arguments:
-ti terminal interactive
-d detached
-p publish  	Publish a container's port(s) to the host. XXX:YYY (XXX host port, YYY docker port).
--memory maximum allowed memory
--cpu-shares 
--cpu-quotas 
--rm deletes the container when the container exits.

We can also chain commands, in the following we run a series of bash commands beffore exiting. 

```bash
docker run -ti ubuntu bash -c "sleep 3; echo all done"
```

### Show containers

```bash 
docker ps
```

-l last exited container. 
-a all containers

### Attach/detach from containers

Attach/detach to a running container

```bash
docker attach <container_name>
```

ctrl+p ctrl+q
exits you from the container but leaves it running. 

### Run something in a container:

```bash
docker exec -ti <container name> <process>
```

### Commit an image

Creates an image from an existing container
```bash
docker commit <container id> <image name>
```
  
### Tag an Image
  
Renames an image with a certain tag. 

```bash
docker tag <image id> <name>
```

### Docker Ports

You don't need to specify the host ports to which docker binds to, below we explose container port 8888 and it binds automatically to a host port.

```bash
docker run --rm -ti -p 8888 --name echo-server
```
 Here if we want to look up which port it bound to:
 
 ```bash
 docker port echo-server
 ```
where echo-server is the port name. 

We can also specify protocols:

```docker run -p outside-port:inside-port/protocol
```
where protocol is either (tcp/udp)

e.g. ```bash docker run -p 1234:1234/udp


### logs

You can look at the logs to determine why something crashed. 

```bash
docker logs <container name>
```

### Stopping and removing containers

Kill and removing - stopped containers still exist until they are explicitely removed.
```bash
docker kill <container name>
docker rm <container name>
```

### Docker Networks

Show networks:
```bash
docker netwwork ls
```

Bridge is the network used by container that don't specify a preference to be put into any other network
Host - When you want a container to not have any network isolation at all. 
None - when there should be no networking at all. 

Create a network:
creates network called network_name
```bash
docker network create network_name
```
Run a container called container_name on the network network_name, running image 'image' and command 'command':
```bash
docker run --rm -ti --net network_name --name container_name image command
```

Put a container on the network:
```bash
docker network connect network_name container_name
```

### Images
list images:

```bash
docker images
```

remove images
```bash
docker rmi image_name
```

commit and tag:
```bash
docker commit image_id name:version-tag
```

### Volumes

Persistent or ephemeral, note ephemeral are lost when the container is shutdown. 

```bash
docker run -ti -v entire/path/to/folder:/shared/folder/in/container image command
```

Sharing between containers
new argument: 'volume-from'
Shared 'discs' that exist only as long s they are being used. 
Can be shared between containers. 

```bash
docker run -ti --volumes-from container_name image command
```

Pull/Push image:

Dockerfile:
Full information from: https://docs.docker.com/engine/reference/builder/

FROM image <- starting image to start running from. 
MAINTAINER firstname lastname email <- who you contact when you have bugs
RUN cmd arg <- runs a command with an argument arg through the shell
CMD <- a command which runs when this image is started. 
ADD file1 file2 <- adds a file1 to the other file2, or
ADD file.tar.gz /dir <- adds contents of tar archives.
ADD https://my.url.download.rpm /dir <- downloads content at URL and saves to directory. 
ENV <- sets environment variablles, during build and when running result. 
ENTRYPOINT <- specifies the start of the command to run. E.g. if you have 
CMD <- specifies the whole command to run, 
EXPOSE NUMBER <- maps a port into the container. Two numbers 
VOLUME <- defines shared volumes or ephemeral volumes. Two arguments maps a host path into a container path. One argument it creates a volume that can be inherited by later containers. Avoid definind shared folders in Dockerfiles.
WORKDIR dir <- sets the directory the container starts in
USER name <- sets which user the container will run in. 

ENTRYPOINT, RUN and CMD can use Shell or Exec Form:
- Shell form:
```bash
nano notes.txt
```
- Exec Form:
```bash 
["/bin/nano","notes.txt"]
```


Quote on ENTRYPOINT vs CMD
ENTRYPOINT is much like CMD, but it specifies the beginning of the expression to use when starting your container and lets you tack more on the end.
So, if you container has an entry point of LS, then anything you type when you say Docker run my image name would be treated as arguments to the LS command.
CMD specifies the whole command to run, and if the person, when they're running the container, types something after Docker run image name,
that will be run instead of CMD.
ENTRYPOINT gets added to when people add arguments to your container and CMD gets replaced when people add arguments to your container.
You can actually use both of them together.
If you have them both, they get strung together, one after the other.
In general, if you're trying to make something that looks like a program and you want people to not care that it's running inside a Docker container,ENTRYPOINT is for making your containers look like normal programs.
CMD is probably what you want to use almost all the time, unless you're trying to do that.

Builder:
- Split the docker file into using builder, you can copy some aspects of other build files. 

Build tips:
- include installers in your project
- have a canonical build that builds everything from scratch
- tag your buids with the git hash of the code that built it. 
- use small base images - like alpine. 
- 



### Somethings for networking:
- No localhost in mac, use host.docker.internal
- For windows, put your computer IP name.

Some notes:
* Don't let containerrs fetch containers when they start - make them fully self contained.
* Don't leave things lying around in stopped containers - always clean up after onself. 
* Building images - each line is it's own process - processes you start on one line will not be running on the next line. 
* Environment variablles will be set on the next line using the ENV command. 
  
  
