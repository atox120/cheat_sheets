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



### Somethings for networking:
- No localhost in mac, use host.docker.internal
- For windows, put your computer IP name.

Some notes:
* Don't let containerrs fetch containers when they start - make them fully self contained.
* Don't leave things lying around in stopped containers - always clean up after onself. 
  
  
