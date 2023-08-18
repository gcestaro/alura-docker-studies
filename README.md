# alura-docker-studies

Por que são mais leves?
- Processos no SO do host
- Imagem read only reutilizável. Cria-se uma camada read write para cada container, mas reutiliza a base da imagem

Como garantem o isolamento? Como funcionam sem instalar um SO? Como fuca 

- PID: Isolamento dos processos que correm no container
- NET: Isolamento das interfaces de rede
- IPC: Isolamento da comunicação entre processos e memória compartilhada
- MNT: Isolamento do sistema de arquivos / ponto de montagem
- UTS: Isolamento do kernel, permite ao container agir como se fosse outro host
- Namespaces: isolamento entre os containers e o host

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
  - Run in background for arm64: ```docker run -d --platform "linux/amd64" dockersamples/static-site```
    - Map container port to localhost port: ```docker run -d -P --platform "linux/amd64" dockersamples/static-site```
  - Map container to specific port: ```docker run -d -p 8080:80 --platform "linux/amd64" dockersamples/static-site```
  - Show port mapping: ```docker port CONTAINER_ID```
  - Show images: ```docker images``` or ```docker image ls```
  - Inspect image: ```docker inspect IMAGE_ID```
  - Image history ```docker history IMAGE_ID```

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
