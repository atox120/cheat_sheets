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


Some notes:
* Don't let containerrs fetch containers when they start - make them fully self contained.
* Don't leave things lying around in stopped containers - always clean up after onself. 
  
  
