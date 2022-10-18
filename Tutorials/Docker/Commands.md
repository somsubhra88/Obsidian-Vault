# Commands
- **Docker Version** - docker --version
- **Pull an Image from docker hub** - docker pull ubuntu
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
- **Getting Logs** - docker logs _container id_ / docker logs -f _container id_ (with follow option)
- **Docker Remove Image**  -  docker rmi image_id

## Copy file from container to local
docker cp _container _