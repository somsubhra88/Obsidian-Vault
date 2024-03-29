# Commands
- **Docker Version** - docker --version
- **Pull an Image from the docker hub** - docker pull ubuntu
- **[[Build a docker image]]** - docker build -t hello-docker *path*
- **See all the available images** - docker image ls
- **[[Creating and Running]]** - docker run --name any_name -d -p 8080:8080 -p 3000:8080 hello-docker _override default command_
- **Running a Container in interactive mode** - docker run -it ubuntu
- **Docker Container Terminal** - docker exec -it _container id_ bash
- **[[Running Containers]]** - docker container ls __or__ docker ps -a
- **Stopping** - docker stop _container id_
- **Start** - docker start _container id_
- **Killing** - docker kill _container id_
- **Delete Container** - docker rm _container id_
- **Every Container** - docker rm -f $(docker ps -aq)
- **Clean Up** - docker system prune
- **Getting Logs** - docker logs _container id_ or docker logs -f _container id_ (with follow option)
- **Docker Remove Image**  -  docker rmi image_id
- **Copy file from container to local** - docker cp _container id_:file/location current/machine/location
- **Copy file from local to the container** - docker cp file/from/current/machine/location _container id_:container/location
- **Docker Usages** - docker stats _container id_
- **Processes Running Inside the Containers** - docker top _container id_
- **Docker Image Layers** - docker history _image name_
- **Building an Image from Container** - docker commit _container id_ image_name
- **Creating Volume** - docker volume create volume_name (/var/lib/docker/volumes)
- **See all volumes** - docker volume ls
- **Docker Run with Volume Mount** -  docker run -d -p 80:8080 -v jenkinsvolume:/var/jenkins_home jenkins:2.60.3

- **Docker Push** - docker tag _image name_ imasomsubhra/apache; docker push imasomsubhra/apache

docker run --env-file envfile env env
