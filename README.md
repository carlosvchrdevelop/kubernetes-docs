# Kubernetes

# kubectl

kubectl es la interfaz de línea de comandos de Kubernetes, que permite a los usuarios interactuar y gestionar clústeres de Kubernetes mediante comandos. Se utiliza para desplegar, gestionar y monitorizar aplicaciones y recursos en entornos basados en Kubernetes.

## Instalación de kubectl

La instlación de kubectl depende del sistema operativo que estemos ejecutando. Podemos encontrar la documentación de cada uno de ellos:

-   [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
-   [MacOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
-   [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

Para ejemplificarlo se mostrará la instalación para MacOS ARM64.

```bash
brew install kubectl
```

Realizamos una prueba para garantizar la version instlada.

```bash
kubectl version --client
```

# Minikube

Minikube es una herramienta que permite ejecutar clústeres de Kubernetes en entornos de desarrollo local para facilitar el desarrollo, la prueba y la experimentación con aplicaciones basadas en contenedores. Proporciona una instancia de Kubernetes de un solo nodo en una máquina virtual para simplificar el despliegue y la gestión de aplicaciones en un entorno local.

## Instalación de minikube

En la [documentación oficial](https://minikube.sigs.k8s.io/docs/start/) se detalla cómo instalar Minikube para cada sistema operativo. Para ejemplificarlo, se realizará la instalación para `MacOS ARM64`.

```bash
brew install minikube
```

Ahora iniciamos el clúster desde una terminal (no root).

```bash
minikube start
```

Para ver las opciones de minikube escribe el siguiente comnado para obtener la documentación.

```bash
minikube
```

Alunas de las opciones más útiles que necesitaremos son conocer la IP de la máquina o acceder a ella a través de ssh:

```bash
minikube ip
minikube ssh
```

## CRUD cluster de minikube

Para obtener información de los clusters de minikube ejecutamos:

```bash
minikube profile list
```

Para conocer qué cluster es el que se está utilizando por defecto (el contexto):

```bash
minikube profile
```

Para cambiar de contexto (de cluster por defecto):

```bash
minikube profile nombreCluster
```

Para crear un nuevo cluster con varios nodos ejecutamos:

```bash
minikube start --driver=docker -p nombreCluster --nodes=2
```

Para borrar el cluster:

```bash
minikube delete -p nombreCluster
```

Para modificar la configuración del cluster tenemos el comando config, del cual podemos consultar la documentoción [aquí](https://minikube.sigs.k8s.io/docs/commands/config/).
Por ejemplo, para cambiar la memoria, podemos ejecutar el siguiente comando:

```bash
minikube config set memory 4G -p nombreCluster
```

Si queremos consultar ahora la memoria asignada y comprobar que se ha efectuado el cambio correctamente podemos ejecutar el comando `get` de minikube. Este comnado, no obstante, no muestra toda la configuración del cluster, sólo la que se ha modificado con el comando `set`.

```bash
minikube config get memory -p nombreCluster
```

Estos cambios son almacenados en un archivo `.json` ubicado en la siguiente ruta. `~/.minikube/config/config.json`.

Además de estos comandos, podemos modificar directamente el archivo de configuración ubicado en `~/.kube/config`

# Pods

Un Pod es la unidad mínima de kubernetes. Un Pod es una burbuja que incluye a uno o varios contenedores de Docker y les agrega funcionalidades adicionales (como direccionaiento IP propio y demás). Lo normal es tener un contenedor por Pod, excepto en contadas ocasiones donde dos contenedores estén intrínsecamente relacionados y constituyan una misma unidad lógica.

Hay dos formas de crear Pods, la forma imperativa que consiste en ejecutar comandos y la forma declarativa que se crean mediante archivos `yaml`. Lo ideal es usar los archivos `yaml` ya que garantizará que kubernetes mantenga el estado deseado en todo momento. En la forma imperativa kubernetes no se preocupa de recuperar el estado en caso de que algo haya cambiado.

Para **lanzar un Pod** de forma imperativa podemos ejecutar el siguiente comando:

```bash
kubectl run nombrePod --image=dockerImage
```

Por ejemplo, para lanzar un Pod con una imagen de nginx:

```bash
kubectl run nginx1 --image=nginx
```

Para borrar un pod usamos el comando `delete`. El parámetro `--now` es opcional y le indica a kubernetes que borre el pod sin esperar la finalización de los procesos pendientes. Además de --now, podemos usar la opción `--grace-period=5` para indicarle que borre el pod en un tiempo máximo de 5 segundos.

```bash
kubectl delete pod nombrePod --now
```

Para borrar todos los pods de golpe podemos ejecutar el delete de esta manera.

```bash
kubectl delete pods --all
```

Para **ver los Pods creados y en ejecución** podemos ejecutar el comando `get pods`. Si al comnado le pasamos el parámetro `-o wide` podemos obtener una visión mucho más detallada.

```bash
kubectl get pods -o wide
```

Para **exportar la configuración** de un pod. Con `-o yaml` exportamos la configuración en formato `yaml`, y con `-o json`, en formato `json`. Esto nos permite editar detalladamente la configuración de nuestros Pods.

```bash
kubectl get pod nombrePod -o yaml > configNombrePod.yaml
```

Para **obtener información detallada de un pod** en particular (por ejemplo el de nginx1 creado anteriormente) podemos ejecutar el comando `describe`.

```bash
kubectl describe pod nginx1
```

Para **ejecutar un comando dentro de un pod** ejecutamos el siguiente comando, similar a cómo se hace en Docker.

```bash
kubectl exec nginx1 -- ls
```

Para poder acceder al servidor en fase de desarrollo, podemos habilitar un `port-forward`, donde mapearemos el puerto externo al puerto interno del contenedor del pod.

```bash
kubectl port-forward nginx1 8088:80
```

Ahora podemos acceder a nuestro servidor de nginx a través de `localhost:8088`.

Para **entrar en modo interactivo**:

```bash
kubectl exec nginx1 -it -- bash
```

Por ejemplo para apache podríamos lanzar el contenedor y consultar los logs de este modo:

```bash
kubectl run apache --image=httpd
kubectl logs apache
```

También podemos dejarlo a la escucha para ir mostrando los logs en tiempo real con el parámetro `-f` o mostrar solo las 20 útimas líneas del log con `--tail=20`.

```bash
kubectl logs -f apache
kubectl logs apache --tail=20
```

# Deployments

Un `Deployment` es un workload más avanzado que un Pod. Podemos decir que un Deployment es una burbuja que engloba a un Pod y le aporta superpoderes, como la posibilidad de hacer updates y rollbacks, la recuperación ante caídas y el escalado. Dentro de un deployment se crea silenciosamente un `Replica Set` que va a ser el encargado de escalar nuestro Pod hasta alcanzar el número de réplicas deseado.

Crear un deployment. Esto crea un Deployment junto con un Replica Set y un Pod asociados.

```bash
kubectl create deployment nombreDeployment --image=imagenDocker
```

Para visualizar la creación del deployment podemos usar el siguiente comando.

```bash
kubectl get deployments
```

Obtener información detallada de un deployment.

```bash
kubectl describe deployment nombreDeployment
```

Muchas de los comandos que se emplean en los Pod se pueden usar también en los Deployment, por lo que no repetiré todos. Uno de los comandos que no se han visto, muy intereante y que también se pueden usar en los Pods, es la de editar la configuración al vuelo.

El siguiente comando abre un editor de consola que nos permmite editar la configuración en yaml del recurso y aplicarla automáticamente.

```bash
kubectl edit deploy nombreDeployment
```

En lugar de modificar toda la configuración, podemos escalar el número de réplicas (de Pods) de un deployment directamente. En el siguiente ejemplo se escala a 5 réplicas el Pod del Deployment nginx-d.

```bash
kubectl scale deploy nginx-d --replicas=5
```

En su lugar, también podemos escalar todos los Deployments que tengan alguna etiqueta.

```bash
kubectl scale deploy -l entorno=prod --replicas=5
```

# Servicios

Desplegar un servicio, donde `--port` hace referencia al puerto interno y `--type=LoadBalancer`lo hace visible desde el exterior.

```bash
kubectl expose pod nombrePod --name=nombrePodSvc --port=80 --type=LoadBalancer
```

Ahora para ver los servicios desplegados.

```bash
kubectl get svc
```

Para consultar la URL para acceder al servicio podemos ejecutar el siguiente comando.

```bash
kubectl service nombreSvc --url
```

Para borrar un servicio.

```bash
kubectl delete svc nombreSvc
```

# Aliases

| comando    | extended    | abbr short |
| :--------- | :---------- | :--------- |
| pod        | pods        | po         |
| deployment | deployments | deploy     |
| replicaset | replicasets | rs         |
| service    | services    | svc        |

# Lanzar configuraciones de recursos desde un yaml

Para crear un nuevo recurso podemos ejecutar `create`.

```bash
kubectl create -f archivo.yaml
```

Sin embargo, si el recurso ya existe puede dat un error. Con el comando `apply` podemos crear o actualizar un recurso si ya existe.

```bash
kubectl apply -f archivo.yaml
```

# Proxy

Es posible lanzar un proxy en kubernetes a través del cual se habilita un puerto en localhost que nos permite preguntar por las características del cluster y de las aplicaciones.

```bash
kubectl proxy
```

Por ejemplo, para acceder a la información del pod de apache de los ejemplos anteriores podemos ir al navegador y escribir una url como esta:

```
http://localhost:8001/api/v1/namespaces/default/pods/apache
```

Y para acceder a la página web desplegada por Apache solo hace falta añadir la ruta /proxy.

```
http://localhost:8001/api/v1/namespaces/default/pods/apache/proxy
```

# Dockerizar

Una vez hemos creado nuestro Dockerfile, podemos crear la imagen con el siguiente comando:

```bash
docker build -t usuario/nombreImagen:etiqueta .
```

Ó

```bash
docker buildx build -t usuario/nombreImagen:etiqueta .
```

Observa el `.` al final que es la ruta donde se encuentra el `Dockerfile` (en este caso en el mismo directorio).

Podemos ver cómo se ha añadido la imagen recien generada a la lista de imágenes con el siguiente comando:

```bash
docker image ls
```

A continuación, para publicar la imagen, primero debemos identificarnos en docker hub y posteriormente pushear la imagen.

```bash
docker login
docker push usuario/nombreImagen:etiqueta
```

Para subir una imagen multiarquitectura podemos usar buildx.

1. En el Docekrfile añadir esta línea en sustirución del FROM.

```Dockerfile
ARG ARCH=
FROM ${ARCH}debian:buster-slim
```

2. Ahora al construir la imagen le pasamos el parámetro ARCH de las distintas plataformas de las que queramos dar soporte.

```bash
docker buildx build --push --tag usuario/nombreImagen:etiqueta --platform linux/amd64,linux/arm/v7,linux/arm64 -f {nombre del Dockerfile} .
```
