docker run = docker create + docker start

docker run command is a combination of two commands

```shell
docker create hello-world
91c4eea16b9e8ffa99226022fab5d0e704f5d3e058b0054ef9ea58dcc586bbf6

docker start -a 91c4eea16b9e8ffa99226022fab5d0e704f5d3e058b0054ef9ea58dcc586bbf6
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

-d flag means detached mode
-it flag to make it as an interactive mode
