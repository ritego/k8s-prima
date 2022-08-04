# K8s

## Prima

### Containers
1. Docker is a container runtime and a container images package manager. Also, perform low-level tasks such as starting and stopping the containers.

2. Dockerfile is a recipe for how to build the container image, while .dockerignore defines the set of files that should be ignored when copying files into the image.

3. Commands
```
$ docker 
- build
- run (-memory --memory-swap --cpu-shares)
- login
- tag
- push
- stop
- rm
- rmi
- images
- system prune
```

4. In general, you want to order your layers from least likely to change to most likely to change in order to optimize the image size for pushing and pulling.



