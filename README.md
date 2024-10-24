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


## Publicar en Docker Hub

| | Comando |
|--|--|
| Autenticarse en Docker Hub | `docker login -u <username> -p <password>` |
| Agregar tag a imagen previamente construida | `docker tag <imageName> <username>/<imageName>:<tag>` |
| Publicar imagen en Docker Hub | `docker push <username>/<imageName>:<tag>` |
| Descargar imagen en Docker Hub | `docker pull <username>/<imageName>:<tag>` |


# Kubernetes (K8s)

- Es la herramienta de orquestación de contenedores más popular.
- Es un sistema de contenedores que se ejecuta en una o más máquinas.
- Permite el balanceo de carga y la escalabilidad.
- Rollouts y rollbacks automatizados.
- Administración de configuración y secretos.
- Usa la misma API a través de on-premises y en la nube.


## ¿Qué no puede hacer K8s?

- No despliega el código fuente.
- No construye nuestra aplicación.
- No provee servicios de nivel de aplicación.

Cada contenedor se ejecuta en un pod, cada pod se ejecuta en un nodo, y cada nodo se ejecuta en un cluster.


## Contexto

- Un contexto es un grupo de parámetros de acceso a un cluster K8s.
- Contiene un cluster de Kubernetes, un usuario y un namespace.
- El contexto actual es el cluster que está actualmente por defecto para kubectl.

| | Comando |
|--|--|
| Obtener el contexto actual | `kubectl config current-context` |
| Listar todos los contextos | `kubectl config get-contexts` |
| Setear el contexto actual | `kubectl config use-context <contextName>` |
| Eliminar un contexto desde el archivo de configuración | `kubectl config delete-context <contextName>` |
| Renombrar contexto | `kubectl config rename-context <oldContextName> <newContextName>` |


## Modo imperativo vs modo declarativo

- Imperativo:
  - Usando comandos de kubectl, toca escribir muchos comandos para generar los recursos.
  - Bueno para el aprendizaje, testing y debug.
  - Es como programar.
- Declarativo:
  - Usando un archivo de configuración, se generan los recursos.
  - Bueno para la producción.
  - Es como configurar un sistema.

### Ejemplo imperativo
```bash
kubectl run mynginx --image=nginx --port=80

kubectl create deploy mynginx --image=nginx --port=80 --replicas=3

kubectl create service nodeport myservice --targetPort=8080

kubectl delete pod nginx
```

### Ejemplo declarativo

```yaml
apiVersion: v1

kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: myapp-container
      image: nginx
```

- **apiVersion:** Versión de la especificación de kubernetes.
- **kind:** Tipo de recurso.
- **metadata.name:** Nombre único del objeto.
- **metadata.namespace:** Espacio de nombres del recurso.
- **spec:** Especificación del recurso.


## Ejemplo de despliegue

Para este ejemplo se utiliza el archivo en la carpeta 04_kubernetesBasics.

### Imperativo
```bash
kubectl create deployment mynginx1 --image=nginx

kubectl get deploy
```

### Declarativo
```bash
kubectl create -f ./04_kubernetesBasics/deploy-example.yaml
```

### Limpiar despliegues
```bash
kubectl delete deployment mynginx1

kubectl delete deploy mynginx2
```


## Namespaces

- Nos permite agrupar recursos, por ejemplo dev, test y prod.
- K8s crea un namespace por defecto.
- Los objetos en un namespace pueden acceder a objetos en un namespace diferente.
  - Ejemplo: objectname.prod.svc.cluster.local
- Al eliminar un namespace se eliminarán todos sus objetos hijos.

| | Comando |
|--|--|
| Listar todos los namespaces | `kubectl get namespace` |
| Versión corta | `kubectl get ns` |
| Configurar el contexto actual para usar un namespace | `kubectl config set-context --current --namespace=<namespaceName>` |
| Crear un namespace | `kubectl credelete ns <namespaceName>` |
| Listar todos los pods en todos los namespaces | `kubectl get pods --all-namespaces` |
| Listar todos los pods en un namespace | `kubectl get pods --namespace=<namespaceName>` |

### Definir un namespace
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prod
```

### Definir un pod en un namespace
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: prod
spec:
  containers:
  - name: nginx-container
    image: nginx
```

Al hacer esto nuestro pod vivirá en el namespace prod.

### Definir una política de rápidamente
```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: clientb
  name: deny-from-other-namespaces
spec:
  podSelector:
    matchLabels:
  ingress:
  - from:
    - podSelector: {}
```

### Definir una quota de recurso
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: prod
spec:
  hard:
    pods: "10"
    limits.cpu: "5"
    limites.memory: 10Gi
```


## Nodos

Son máquinas físicas o virtuales que ejecutan un cluster K8s. Estas pueden tener pods, kube-proxies, kubelets y runtimes de los contenedores.

| | Comando |
|--|--|
| Listar nodos | `kubectl get nodes` |
| Listar nodos con su ip | `kubectl get nodes -o wide` |
| Ver detalles de todos los nodos | `kubectl describe node` |
| Ver detalles de un nodo | `kubectl describe node <nodeName>` |

### kubelet
- Administra el ciclo de vida de los pods.
- Se asegura de que los contenedores descritos en el pod estén corriendo y están en el estado correcto.

### kube-proxy
- Un proxy de red.
- Administra las reglas de red en los nodos.

### Runtime de contenedores
- K8s soporta muchos runtimes de contenedores.
- Se debe implementar la interfaz del runtime de contenedores de Kubernetes.
  - Moby.
  - Containerd.
  - Cri-0.
  - Rkt.
  - Kata.
  - Virtlet.

### Node pool
- Es un grupo de máquinas virtuales, todas del mismo tamaño.
- Un cluster puede tener múltiples node pools.
  - Estos node pools pueden hostear diferentes tamaños de máquinas virtuales.
  - Cada node pool puede ser autoescalado de forma independiente respecto a los otros node pools.