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


## Persistencia de datos

Los contenedores no tienen data persistente, es decir que una vez se elimina el contenedor, todos los datos se perderán.

Para evitar esto, se pueden usar los volumenes. Estos nos permiten almacenar datos persistentes en el host. Podemos crear un volumen Docker Desktop o generarlo de manera local y tenerlo en el host.


## Administrar volumenes

| | Comando |
|--|--|
| Listar volumenes en el host | `docker volume ls` |
| Listar volumenes en el contenedor | `docker container inspect <containerId>` |
| Crear volumen | `docker volume create <volumeName>` |
| Eliminar volumen | `docker volume rm <volumeName>` |
| Mostrar información de volumen | `docker volume inspect <volumeName>` |
| Eliminar todos los volumenes no montados | `docker volume prune` |
| Montar volumen en contenedor | `docker run -v <volumeName>:<mountPath> <imageName>` |


## Docker Compose

Supongamos que en nuestro proyecto tenemos 3 contenedores, el frontend, el backend y el database. Si queremos desplegar todos los contenedores en un solo host, tendremos que hacer un despliegue de 3 contenedores. Para evitar esto, podemos crear una aplicación multicontenedor. Con esto se introduce el uso de docker-compose, el cual nos permite crear un archivo de configuración para nuestra aplicación y todos los servicios que necesitemos.

```yaml
version: "3.9"

services:
  frontend:
    build: ./01_dockerBasics
    ports:
      - "3000:3000"
    depends_on:
      - backend
      - database
    restart: always

  backend:
    build: ./02_dockerCompose
    ports:
      - "3001:3001"
    restart: always

  database:
    image: postgres
    ports:
      - "5432:5432"
    restart: always
```

| | Comando |
|--|--|
| Construir imágenes | `docker compose build` |
| Iniciar contenedores | `docker compose start` |
| Detener contenedores | `docker compose stop` |
| Construir e iniciar contenedores | `docker compose up -d` |
| Listar lo que se está ejecutando | `docker compose ps` |
| Eliminar desde memorua y imágenes | `docker compose rm` |
| Detener y eliminar | `docker compose down` |
| Ver los logs | `docker compose logs` |
| Ejecutar comandos en los contenedores | `docker compose exec <containerId> bash` |

### Compose V2

| | Comando |
|--|--|
| Ejecutar una instancia como un proyecto | `docker compose --project-name test1 up -d` |
| Versión más corta | `docker compose -p test-2 up -d` |
| Listar proyectos corriendo | `docker compose ls` |
| Copiar archivos desde el contenedor | `docker compose cp <containerId>:<path> <localPath>` |
| Copiar archivos desde el host | `docker compose cp <localPath> <containerId>:<path>` |