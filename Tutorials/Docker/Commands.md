# Commands
- **Docker Version** - docker --version
- **Build a docker image** - docker build -t hello-docker *path*
- **See all the available images** - docker image ls
- **Run** - docker run -d -p 8080:80 -p 3000:80 hello-docker
- **Running a Container in interactive mode** - docker run -it ubuntu
- **Pull an Images from docker hub** - docker pull ubuntu
- **Running Containers** - docker container ls / docker ps -a
- **Stopping** - docker stop <docker id>