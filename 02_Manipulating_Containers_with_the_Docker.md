## 12: Docker Run in Detail

```
$ docker run <image name>
$ docker run hello-world
```

***

## 13: Overriding Default Commands

```
$ docker run <image-name> <command>
$ docker run busybox echo hi there      # 'echo hi there' is the override command
$ docker run busybox echo bye there
$ docker run busybox echo how are you
$ docker run busybox ls
```

`busybox` image as FS Snapshot = {bin, dev, etc, home, proc, root}

```
$ docker run hello-world ls            # Error
$ docker run hello-world echo hi there # Error
```

Note: The `hello-world` image doesn't have the executables for `ls` and `echo` commands.

***

## 14: Listing Running Containers

```
$ docker ps                              # What containers are currently running and their id?
$ docker run <image-name> <command>
$ docker run busybox ping google.com
$ docker ps
$ docker ps --all                        # List of ALL containers that we have ever created.
```

CONTAINER_ID -- IMAGE -- COMMAND -- CREATED -- STATUS -- PORTS -- NAMES

***

## 15: Container Lifecycle

docker run = docker create + docker start

```
$ docker create hello-world         # A long string of characters printed out. This is CONTAINER_ID.
$ docker start    CONTAINER_ID
$ docker start -a CONTAINER_ID      # -a (attach) makes docker watch for output coming from "container" and print it to your terminal.
```

***

## 16: Restarting Stopped Containers

```
$ docker ps --all                   # List of ALL containers that we've ever created
```

CONTAINER_ID -- IMAGE -- COMMAND -- CREATED -- STATUS -- PORTS -- NAMES

```
$ docker run <image-name> <command>
$ docker run busybox echo hi there
```

```
$ docker ps --all                   # If STATUS = Exited, we can still start it up.
$ docker start CONTAINER_ID
$ docker start -a CONTAINER_ID      # -a (attach)
```

***

## 17: Removing Stopped Containers

```
$ docker ps --all			        # List of all containers that we've ever created
```

CONTAINER_ID -- IMAGE -- COMMAND -- CREATED -- STATUS -- PORTS -- NAMES

```
$ docker system prune
```

* WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all build cache

***

# 18: Retrieving Log Outputs

```
$ docker create <image-name> <command>
$ docker create busybox echo hi there       # This will print a long CONTAINER_ID
$ docker start CONTAINER_ID
$ docker start -a CONTAINER_ID
$ docker logs CONTAINER_ID                  # A log of all the records emitted by that container)
```

***

## 19: Stopping Containers

```
$ docker create busybox ping google.com 	 # This will generate a CONTAINER_ID
$ docker start CONTAINER_ID
$ docker logs  CONTAINER_ID
$ docker ps
$ docker stop  CONTAINER_ID                  # SIGTERM: Graceful shutdown
$ docker start CONTAINER_ID
$ docker kill  CONTAINER_ID                  # SIGKILL: Instant kill
```

***

## 20: Multi-command Containers

```
$ docker run redis                          # Ready to accept connections. 
```

***

## 21: Executing Commands in Running Containers

```
$ docker exec -it CONTAINER_ID <command>    # it: "input" to container
$ docker exec -it CONTAINER_ID redis-cli
$ set myvalue 5
$ get myvalue
```

***

## 22: The Purpose of `IT` Flag

* How processes run inside a Linux environment? 
  - When we run Docker on our machine. Every single container that we are running runs inside inside a VM running Linux. Processes are inside a Linux world.

* Every 'process' we create in Linux is attached with three communication channels: `STDIN | STDOUT | STDERR`

```
$ docker exec -it 
$ docker exec -i -t                     # -i flag means attach our terminal to the STDIN channel of the new running process; 
                                        # -t means to show the input / output text in a nice formatted fashion
$ docker exec -i <image-id> redis-cli   # Notice: we've removed -t flag
```

***

## 23: Getting Command Prompt in a Container

```
$ docker exec -it CONTAINER_ID sh      # check this command ?? exec or run ??
$ ctrl-d or exit
```

***

## 24: Starting with a Shell

```
$ docker run -it busybox sh
$ ping google.com
$ ctrl-c / ctrl-d / exit
```

***

## 25: Container Isolation

```
$ docker run -it busybox sh         # In one window
$ touch hithere
$ ls
```

* Two DIFFERENT containers have separate file systems and are ISOLATED to each other unless CONNECTED through channel.

```
$ docker run -it busybox sh             # In another window
$ ls                                    # Notice, you will "not" see this file there.
```

***
