# ¿Qué es un contenedor?

Es una unidad de desarrollo, contiene el código, las herramientas de sistema, librerías de sistema y otros recursos necesarios para ejecutar una aplicación.


# Por qué usar un contenedor?

- Movernos rápidamente desplegando pequeñas unidades.
- Usa menos recursos.
- Encajar más en el mismo host.
- Rápida automatización.
- Portabilidad.


# ¿Qué es el container registry?

- Repositorio centralizado de contenedores.
- Es como un GitHub para contenedores.
- Docker Hub es el container registry predeterminado.
- Hay container registries adicionales tales como AWS ECR, Azure ACR, Google GCR, etc.


# ¿ Qué es un orquestador?

- Administra:
    - Infraestructura.
    - Contenedorres.
    - Despliegues.
    - Escalamiento.
    - Failovers.
    - Monitoreo del estado de los contenedores.
    - Actualizaciones, hay 0 downtimes durante los despliegues.
- Podemos utilizar los predeterminados, tales como Kubernetes, Swarm, Service Fabric, etc.
- Orquestadores como un serrvicio:
    - Azure AKS, AWS EKS, Google GKE, etc.


# Docker

## Información de Docker

| | Comando |
|--|--|
| Mostrar información de sistema | `docker info` |
| Mostrar versión de sistema | `docker version` |


## Comandos básicos

| | Comando |
|--|--|
| Descargar una imagen desde un registry | `docker pull <imageName>` |
| Correr contenedor | `docker run <imageName>` |
| Correr contenedor en segundo plano | `docker run -d <imageName>` |
| Correr contenedor configurando puerto | `docker run -p <hostPort>:<containerPort> <imageName>` |
| Iniciar los contenedores detenidos | `docker start <containerId>` |
| Listar los contenedores en ejecución | `docker ps` |
| Listar contenedores en ejecución y detenidos | `docker ps -a` |
| Detener contenedores | `docker stop <containerId>` |
| Eliminar contenedores | `docker kill <containerId>` |
| Obtener información de imagen | `docker inspect <imageName>` |
| Acceder a terminal de contenedor | `docker exec -it <containerId> bash` |


## Administrar límites

| | Comando |
|--|--|
| Memoria máxima | `docker run --memory="256m" nginx` |
| CPU máxima | `docker run --cpus=".5" nginx` |


## Limpiar contenedores

| | Comando |
|--|--|
| Eliminar contenedor y imagen | `docker rm <containerId>` |
| Eliminar todos los contenedores detenidos | `docker rm $(docker ps -a -q)` |
| Listar imágenes en el host | `docker images` |
| Eliminar imagen | `docker rmi <imageName>` |
| Eliminar imágenes que no son usadas por ningún contenedor | `docker system prune -a` |


## Construir imágenes

| | Comando |
|--|--|
| Construir imagen desde Dockerfile ubicado en el mismo directorio | `docker build -t <imageName>:<tag> .` |
| Construir imagen desde Dockerfile ubicado en un directorio diferente | `docker build -t <imageName>:<tag> -f <DockerfilePath> .` |
| Agregar tag a imagen existente | `docker tag <imageName> <imageName>:<tag>` |

### Ejemplo de Dockerfile
```dockerfile
FROM alpine

RUN apk add -update nodejs nodejs-npm
COPY . /src
WORKDIR /src
RUN npm install

EXPOSE 8080
ENTRYPOINT ["node", "./app.js"]
```