
Docker check the env or login to the Image 
```bash
docker run -it --entrypoint /bin/sh <docker_image_name_or_id>
```

if you want install some package in docker image or add new commit ID 
```bash
docker run -exec -it <ImageID> /bin/sh
docker commit $(docker ps output id) <New image name>
# open new terminal
docker ps and docker commit <dockerid>
```
