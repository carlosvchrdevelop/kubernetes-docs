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

Para **ver los Pods creados y en ejecución** podemos ejecutar el comando `get pods`. Si al comnado le pasamos el parámetro `-o wide` podemos obtener una visión mucho más detallada.

```bash
kubectl get pods -o wide
```

Para **obtener información detallada de un pod** en particular (por ejemplo el de nginx1 creado anteriormente) podemos ejecutar el comando `describe`.

```bash
kubectl describe pod/nginx1
```

Para **ejecutar un comando dentro de un pod** ejecutamos el siguiente comando, similar a cómo se hace en Docker.

```bash
kubectl exec nginx1 -- ls
```

Para **entrar en modo interactivo**:

```bash
kubectl exec nginx1 -it -- bash
```

Por ejemplo para apache podríamos lanzar el contenedor y consultar los logs de este modo:

```bash
kubectl run apache --image=apache --port=8080
kubectl logs apache
```

También podemos dejarlo a la escucha para ir mostrando los logs en tiempo real con el parámetro `-f` o mostrar solo las 20 útimas líneas del log con `--tail=20`.

```bash
kubectl logs -f apache
kubectl logs apache --tail=20
```
