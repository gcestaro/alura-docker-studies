# alura-docker-studies

Why lighter than VMs?
- Running processes in OS's host
- Read only image reusable. Every container has its own read write layer, but share image's read only basis.

How to ensure isolation? How work without installing a OS?
- PID: Isolation of process that run in the container
- NET: Isolation of network interfaces
- IPC: Isolation of processes communication and shared memory
- MNT: Isolation of file system / mount points
- UTS: Isolation of kernel, allows containers to act as another host
- Namespaces: Isolation between containers and the host

MAC Installation: https://docs.docker.com/desktop/install/mac-install/

Docker HUB: https://hub.docker.com/

Container table:
- Show running containers: ```docker container ls``` or simply ```docker ps```
- Show all containers: ```docker ps -a```
  - CONTAINER ID: unique id
  - IMAGE: image used
  - COMMAND: command executed in the container
  - CREATED: timestamp
  - STATUS: current state
  - PORTS: network ports
  - NAMES: given or random container name
- Show sizing: ```docker ps -s```
  - Container size: inside container sizing
  - Virtual size: image size + container size

Docker run:
- Check locally for the image
- Pull image from docker hub if doesn't exist locally
- Verify image hash
- Run image with default command

Examples:
- Run ubuntu image: ```docker run ubuntu```
  - Download oficial ubuntu image: ```docker pull ubuntu```
  - Run ubuntu image: ```docker run ubuntu```
  - Run ubuntu image for a day: ```docker run ubuntu sleep 1d```
  - Run with command bash and in iterative mode: ```docker run -it ubuntu bash```
  - Stop container: ```docker stop CONTAINER_ID```
    - By default it waits 10s, so can change for immediatly: ```docker stop -t=0 CONTAINER_ID```
    - Stop all: ```docker stop -t=0 $(docker ps -a -q)```
  - Pause container: ```docker pause CONTAINER_ID```
  - Unpause container: ```docker unpause CONTAINER_ID```
  - Start container: ```docker start CONTAINER_ID```
  - Execute command in iterative mode: ```docker exec -it CONTAINER_ID COMMAND```
    - Example: ```docker exec --it abc1234 base```
  - Remove container: ```docker rm CONTAINER_ID```
    - Force: ```docker rm CONTAINER_ID --force```
    - Remove all: ```docker rm $(docker ps -a -q) --force```
  - Run in background for arm64: ```docker run -d --platform "linux/amd64" dockersamples/static-site```
    - Map container port to localhost port: ```docker run -d -P --platform "linux/amd64" dockersamples/static-site```
  - Map container to specific port: ```docker run -d -p 8080:80 --platform "linux/amd64" dockersamples/static-site```
  - Show port mapping: ```docker port CONTAINER_ID```
  - Show images: ```docker images``` or ```docker image ls```
  - Inspect image: ```docker inspect IMAGE_ID```
  - Remove image: ```docker rmi IMAGE_ID --force```
    - Remove all: ```docker rmi $(docker images -aq) --force```
  - Image history ```docker history IMAGE_ID```
  - Copy image with other name/tag: ```docker tag gabscestaro/app-node:1.2 gabcestaro/app-node:1.2```
  - Push image: ```docker push IMAGE_ID```

Dockerfile:
  - Docks: https://docs.docker.com/engine/reference/builder/
  - FROM: base image
  - WORKDIR: where to execute commands in container
  - ARG: variable used during image creation
  - ENV: environment variable accessible in container
  - EXPOSE: container port to be exposed
  - COPY: copy local data to container
  - RUN: run base commands
  - ENTRYPOINT: command to run once entering the container
  - Examples:
    - Build image from Dockerfile: ```docker build -t gabscestaro/app-node:1.0 .```
    - Run: ```docker run -it -d -p 8081:3000 gabscestaro/app-node:1.0```
    - After defining env variables in Dockerfile: ```docker run -it -d -p 9090:6000 gabscestaro/app-node:1.2```

Docker login:
- Create account in https://www.docker.com/
- ```docker login -u gabcestaro```

Persistence:
- bind mount: Filesystem
  - Map a path in container to a local path: ```docker run -it -v ./docker-volume:/app ubuntu bash```
  - Example of recommended usage instead of ```-v```: ```docker run -it --mount type=bind,source="$(pwd)"/docker-volume,target=/app ubuntu bash```
- volume (most recommended for production environments): Filesystem -> Docker area
  - List: ```docker volume ls```
  - Create: ```docker volume create NAME```
  - Usage with created volume: ```docker run -it --mount source=NAME,target=/app ubuntu bash```
    - Example: ```docker run -it --mount source=my-volume,target=/app ubuntu bash```
    - Auto create volume: ```docker run -it --mount source=my-volume-creating-now,target=/app ubuntu bash```
- tmpfs mount:
  - https://docs.docker.com/storage/tmpfs/
  - Linux only
  - Stored in host's memory
  - Not shared between container instances

Network:
- ```docker network ls```
  - Default:
    - host: https://docs.docker.com/network/drivers/host/
      - Remove network isolation from container and host, so if container exposes an app at port 3000, it is possible to access the app through localhost:3000 in the host
      - ```docker run -d --network host ubuntu sleep 1d```
    - bridge: https://docs.docker.com/network/drivers/bridge/
      - Auto resolve DNS entre containers
      - ```docker run -d --network bridge ubuntu sleep 1d```
    - none: https://docs.docker.com/network/drivers/none/
      - No network interface
      - ```docker run -d --network none ubuntu sleep 1d```
- 2x Run ubuntu image: ```docker run -it ubuntu bash```
  - Install ping: ```apt-get install iputils-ping -y```
  - ```ping OTHER_CONTAINER_IP```
- Inspect each container network configs: ```docker inspect CONTAINER_ID```
- Create network: ```docker network create --driver bridge my-bridge```
- Naming containers: ```docker run -it --name ubuntu-test --network my-bridge ubuntu bash```
  - Install ping: ```apt-get install iputils-ping -y```
  - Run other container: ```docker run -d --name pong --network my-bridge ubuntu sleep 1d```
  - Ping other container by its name: ```ping pong```

Alura example:
- ```docker run -d --network my-bridge --name meu-mongo mongo:4.4.6```
  - ```docker pull mongo:4.4.6```
- ```docker run -d --network my-bridge --name alurabooks -p 3000:3000 --platform "linux/amd64" aluradocker/alura-books:1.0```
  - ```docker pull aluradocker/alura-books:1.0```
- Access: ```localhost:3000/seed``` then ```localhost:3000```

Docker-compose:
- Docs: https://docs.docker.com/compose/
- Run in the same folder docker-compose.yml is defined: ```docker-compose up```
  - Background: ```docker-compose -d up```
- ```docker-compose ps```
- ```docker-compose down```
