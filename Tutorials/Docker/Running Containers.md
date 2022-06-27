docker ps --all

this will print all the containers running or stopped

``` shell
docker ps --all

CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
91c4eea16b9e   hello-world   "/hello"   4 minutes ago    Exited (0) 4 minutes ago              elated_sutherland
34dd9cda3bdb   hello-world   "/hello"   4 minutes ago    Created                               frosty_torvalds
ea3c5e57f212   busybox       "ls"       18 minutes ago   Exited (0) 17 minutes ago             sweet_easley
```

If we want to run 