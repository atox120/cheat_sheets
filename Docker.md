# Docker Cheat Sheet

Here in are some useful commands for docker related operations.

### Run an image

'''bash
docker run 
'''

some useful arguments:
-t terminal
-i interactive

### Show containers

'''bash 
docker ps
'''

-l last exited container. 
-a all containers

### Commit an image

Creates an image from an existing container
'''bash
docker commit <container id>
'''
  
### Tag an Image
  
Renames an image with a certain tag. 

'''bash
docker tag <image id> <name>
'''
  
  
