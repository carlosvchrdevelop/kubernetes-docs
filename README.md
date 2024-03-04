# Guía práctica de Kubernetes

Te doy la bienvenida a la guía práctica de Kubernetes. Esta guía se encuentra estructurade de la siguiente manera:

-   [README](/) (este documento): Conceptos teóricos, configuraciones, comandos y ejemplos básicos.
-   [Pod](./Pod.yaml): Configuración declarativa de un Pod.
-   [Deployment](./Deployment.yaml): Configuración declarativa de un Deployment.
-   [Service](./Service.yaml): Configuración declarativa de un Service.
-   [Namespace](./Namespace.yaml): Configuración declarativa de un Namespace.
-   [LimitRange](./LimitRange.yaml): Configuración declarativa de un LimitRange.

Mi recomendación es empezar por leer los conceptos básicos de esta página y realizar los ejemplos de las secciones `Quick start`, probar algunos ejemplos por vuestra cuenta teniendo como referencia la lista de comandos y, a continuación, pasar a las secciones específicas de Deployments y Servicios.

# Kubernetes

Kubernetes es una plataforma de código abierto diseñada para la automatización, implementación, escala y gestión de aplicaciones contenerizadas. Kubernetes proporciona un entorno robusto para desplegar y orquestar contenedores, facilitando la gestión eficiente de aplicaciones en entornos de nube y en clústeres de servidores. Con características como el escalado automático, la auto curación y la declaratividad en la definición de recursos, Kubernetes se ha convertido en la herramienta líder para la administración de contenedores en entornos de producción, permitiendo a los desarrolladores y administradores de sistemas gestionar aplicaciones de manera eficiente y escalable.

## Recursos

En Kubernetes, los recursos son unidades de cómputo que representan los componentes y objetos gestionados por el sistema.

Hay dos formas de crear recursos: la forma imperativa, que consiste en ejecutar comandos y la forma declarativa, que consiste en utilizar archivos `yaml`. Lo ideal es usar los archivos `yaml` ya que garantizará que kubernetes mantenga el estado deseado en todo momento. En la forma imperativa kubernetes no se preocupa de recuperar el estado en caso de que algo haya cambiado.

Estos son los tipos de recurso más habituales:

## Pods

Representa la unidad mínima en Kubernetes y puede contener uno (recomendado) o varios contenedores. Proveen al contenedor de un `endpoint` (dirección IP y puerto).

## ReplicaSet

Garantiza que un número especificado de réplicas de un pod se estén ejecutando en todo momento, de modo que si una réplica se cae, crea otra automáticamente. Proporciona escalado automático y gestión de la disponibilidad. Sin embargo, normalmente no trabajamos directamente con este recurso, sino que lo gestiona el Deployment de forma transparente y nosotros trabajamos con el Deployemnt.

## Deployments

Permiten gestionar la implementación de aplicaciones. Suponen una capa que engloba a uno o varios pods e integra de forma transparente un ReplicaSet que nos permite escalar fácilmente los pods que contiene.

## Services

Permite crear políticas de acceso a los pods de un deployment. Ofrece una forma estable de acceder a aplicaciones a través de una dirección IP y un nombre de servicio. Los tipos de servicios que podemos encontrar son los siguientes:

1. **ClusterIP (por defecto)**: Servicio interno del clúster. Estos servicios posibilitan conectar Deployments dentro del clúster, por ejemplo un Deployment de un backend con otro Deployment de una base de datos. En este caso la base de datos debe exponer un servicio ClusterIP, ya que no debe ser accedido desde fuera, en cambio, es posible que el backen exponga una API Rest que sí deseemos hacer accesible desde fuera para que los usuarios la consuman. En este último caso usaremos un servicio de tipo NodePort o LoadBalancer.
2. **NodePort**: Permite al servicio ser accesible desde fuera del clúster. Estos servicios son los que deben desplegarse cuando queramos que los usuarios puedan acceder al servicio.
3. **LoadBalancer**: Igual que el NodePort, pero pensado para integrarse con un servidor Cloud. Este servicio agrega un puerto externo que es el que provee la nube externa para conectarse al servicio. Si no estamos en un entorno cloud se debe usar el NodePort, ya que el LoadBalancer malgastará recursos tratando de adquirir esa IP externa, aunque también debería funcionar.

## ConfigMaps

Almacena la configuración no confidencial en forma de pares clave-valor, que luego se montan como volúmenes o se utilizan como variables de entorno en los pods.

## Secrets

Similar a ConfigMaps, pero diseñado para almacenar información confidencial, como contraseñas y tokens de acceso.

## Persistent Volumes (PV) y Persistent Volume Claims (PVC):

Permiten el almacenamiento persistente para los pods, independientemente de su ciclo de vida.
PV representa el almacenamiento físico, mientras que PVC solicita y utiliza ese almacenamiento.

## Namespaces

Proporciona múltiples clústeres virtuales dentro de un clúster de Kubernetes.
Se utiliza para aislar y organizar recursos entre diferentes equipos o aplicaciones.

## ServiceAccounts

Proporciona identidades a los pods y controla los permisos de acceso a los recursos del clúster.

## Ingress

Define las reglas de enrutamiento externo al clúster para el tráfico entrante.
Permite exponer servicios HTTP y HTTPS de manera más avanzada que los servicios normales.

## DaemonSet

Garantiza que todos los nodos del clúster ejecuten una instancia específica de un pod. Útil para tareas que deben ejecutarse en cada nodo, como la recolección de registros.

## LimitRange

Es un objeto que se utiliza para establecer límites en los recursos de los contenedores en un clúster. Los límites se pueden aplicar a varios recursos, como CPU, memoria y el número de contenedores.

# Minikube

Minikube es una utilidad de línea de comandos que permite crear un clúster de kubernetes de un único nodo (se puede ampliar a varios nodos) con el fin de realizar pruebas **en entornos de desarrollo** de manera sencilla, sin la complejidad que conlleva desplegar un clúster real con herramientas coomo `kubeadm`.

Minikikube permite la posibilidad de desplegar tantos klústeres como sea necesario, por defeceto, cuando ejecutamos el comando para crear un clúster nuevo sin parámetros adicionales, Minikube creará un nuevo clúster con el nombre minikube y lo asignará al contexto actual.

El contexto en kubernetes hace referencia al clúster con el que estamos trabajando y sobre el que toman efecto los comandos ejecutados con `kubectl`. Kubectl es una utilidad de línea de comandos que nos permitirá controlar nuestro clúster, ya sea Minikube, Kubeadm o cualquier otro.

## Quick start

La documentación oficial se encuentra [aquí](https://minikube.sigs.k8s.io/docs/start/), donde también se pueden encontrar las guías de instalación en otras plataformas. En este ejemplo se realizará en un sistema MacOS ARM64.

```bash
# instalación
brew install minikube

# iniciar clúster
minikube start

# parar clúster
minikube stop -p minikube

# eliminar clúster
minikube delete -p minikube
```

## Comandos

Los comandos más útiles que podemos utilizar para gestionar el clúster de minikube son los siguientes.

```bash
# Mostrar opciones disponibles de minikube (similar al típico comando --help)
minikube

# Crear un nuevo clúster (sin parámetros, se crea uno por defecto llamado minikube)
# Params:
#   --driver={docker, kvm2}: docker para contenedores y kvm2 para máquinas
#                            virtuales. Hay más, pero estos son los recomendados.
#   -p {name}: indica el nombre que queremos dar al clúster
#   --nodes={#}: indica el número de nodos que debe tener el clúster.
#   --memory {#}: establece la RAM disponible para el clúster en MiB.
#   --cpus {#}: establece la cantidad de cpus disponibles para el clúster
minikube start --driver=docker -p miCluster --nodes=4 --memory 8192 --cpus 2

# Reanudar un clúster parado
# Params:
#   -p {name}: indica el nombre del clúster
minikube start -p miCluster

# Detener un clúster
# Params:
#   -p {name}: indica el nombre del clúster
minikube stop -p miCluster

# Eliminar un clúster
# Params:
#   -p {name}: indica el nombre del clúster
minikube delete -p miCluster

# Ver la lista de clústers
minikube profile list

# Ver el clúster seleccionado
minikube profile

# Cambiar de clúster
minikube profile otroCluster

# Comprobar la IP del clúster (para conectar a los servicios internos)
minikube ip

# Acceder al clúster por ssh
minikube ssh

# Abrir un dashboard para visualizar el clúster
minikube dashboard

# Mostrar la lista de servicios expuestos en el cúster
minikube service list

# Obtener la cadena de conexión de un servicio (necesario en Mac y a veces Windows)
minikube service miServicio --url

# Establecer el driver por defecto para no especificarlo cada vez {docker, kvm2}
minikube config set driver kvm2

# Cambiar la configuración del clúster. Es necesario detener el clúster primero
# Params:
#   memory {G,M}: establece la cantidad de RAM disponible para el clúster
#   cpu {#}: establece la cantidad de cpus
#   -p: especifica sobre qué clúster se aplica la configuración
minikube config set memory 4G -p miCluser
minikube config set cpu 4 -p miCluster

```

# Kubectl

Kubernetes, como tal, es la tecnología que nos permite crear la infraestructura, pero para montar esta infraestructura dependemos de las herramientas que provee su ecosistema. La herramienta más importante es `kubectl`. Kubectl es la interfaz de línea de comandos de Kubernetes, que permite a los usuarios interactuar y gestionar clústeres de Kubernetes (Minikube, Kubeadm, etc.) mediante comandos. Se utiliza para desplegar, gestionar y monitorizar aplicaciones y recursos en entornos basados en Kubernetes.

La instlación de kubectl se va a ejemplificar para un sistema MacOS ARM64. Sin embargo,la instalación depende del sistema operativo que estemos ejecutando, de modo que debemos revisar la guía adecuada para cada uno de ellos:

-   [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
-   [MacOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
-   [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

```bash
brew install kubectl

# Podemos comprobar que se haya instalado correctamente con:
kubectl version --client
```

## Quick start

```bash
# Creamos un Deployment basado en un contenedor nginx
kubectl create deploy nginx-d --image=nginx

# Creamos un Servicio para exponer el Deployment al exterior
kubectl expose deploy nginx-d --name=nginx-svc --port=80 --type=NodePort

# Opcionalmente, podemos comprobar que se hayan creado los recursos
kubectl get all -o wide

# Deberíamos poder acceder al servicio con la IP del clúster y el puerto
# asignado por kubernetes. En minikube podemos obtenerlo directamente:
minikube get service nginx-svc --url

# Eliminamos todos los recursos desplegados en el kúster
kubectl delete all --all
```

## Aliases

| Recurso    | Abreviatura |
| :--------- | :---------- |
| pod        | po          |
| deployment | deploy      |
| replicaset | rs          |
| service    | svc         |
| namespace  | ns          |
| endpoints  | -           |

## Comandos generales

Los comandos en los que se indica el campo `{resource}` significa que son válidos para todos, o la mayoría, de los recursos identificados en la tabla de `Aliases` anterior. Podemos usar tanto el nombre del reecurso como la abreviatura, como sea más cómodo.

```bash
# Obtener información de clúster
kubectl config view

# Crear un recurso de forma declarativa
kubectl create -f {name}.yaml

# Crear un recurso de forma declarativa
# Si el recurso ya existe lo actualiza.
kubectl apply -f {name}.yaml

# Exponer un API para consultar información del clúster por HTTP
kubectl proxy

# Ejecutar comandos en otro namespace distnto de default
kubectl [...] -n {namespace}

# Cambiar el namespace por defecto
kubectl config set-context --current --namespace={namespace}

# Obtener la lista de recursos creados de un tipo
# params:
#   -o wide: amplia la información
kubectl get {resource}
kubectl get {resource} -o wide

# Consultar información básica de un recurso
# params:
#   -o wide: amplia la información
#   --show-labels: muestra las etiquetas del recurso
#   -L {tagName}: agrega al resultado una columna con la etiqueta
#                 {tagName} donde aparecerá el valor de cada recurso.
#   -l {condición}: filtra por etiquetas basado en una condición
kubectl get {resource}/{name}
kubectl get {resource}/{name} -o wide
kubectl get {resource}/{name} --show-labels
kubectl get {resource}/{name} -L {tagName}
kubectl get {resource}/{name} -l {tagName}={tagValue}
kubectl get {resource}/{name} -l {tagName}!={tagValue}
kubectl get {resource}/{name} -l {tagName} in ({tagValue},{tagValue},...)
kubectl get {resource}/{name} -l {tagName} notin ({tagValue},{tagValue},...)


# Consultar información detallada de un recurso
kubectl describe {resource} {name}

# Consultar configuración de un recurso
# params:
#   -o yaml: mostrar en formato yaml (yaml,json)
kubectl get {resource}/{name} -o json
kubectl get {resource}/{name} -o yaml
kubectl get {resource}/{name} -o yaml > miconfig.yaml

# Editar un recurso en caliente y aplicar los cambios
kubectl edit {resource} {name}

# Eliminar todos los recursos de un clúster
kubectl delete all --all

# ELimina todos los recursos de un tipo
kubectl delete {resource} -all

# Elimina un recurso de forma normal, dejando terminar los procesos pendientes
# params:
#   --now: elimina el recurso inmediatamente, sin dejar terminar los procesos
#          de forma regular
#   --grace-period={seconds}: similar al anterior pero, en lugar de eliminarlos
#          inmediatamente, concede un tiempo para que intenten finalizar
#   -l {condición}: filtra por etiquetas basado en una condición
kubectl delete {resource} {name}
kubectl delete {resource} {name} --now
kubectl delete {resource} {name} --grace-period={seconds}
kubectl delete {resource}/{name} -l {tagName}={tagValue}
kubectl delete {resource}/{name} -l {tagName}!={tagValue}
kubectl delete {resource}/{name} -l {tagName} in ({tagValue},{tagValue},...)
kubectl delete {resource}/{name} -l {tagName} notin ({tagValue},{tagValue},...)

# Obtener historial del despliegue de un recurso
# params:
#   --revision: historial de una versión específica
kubectl rollout history {resource} {name}
kubectl rollout history {resource} {name} --revision={num}

# Obtener información de lo que se está realizando sobre un recurso
kubectl rollout status {resource} {name}

# Deshacer una actualización
kubectl rollout undo {resource} {name}
kubectl rollout undo {resource} {name} --to-revision={num}
```

## Comandos específicos de los Pods

```bash
# Crear un Pod
kubectl run {name} --image={dockerImage}

# Ejecutar un comando dentro de Pod
kubectl exec {name} -- {comando}

# Lanzar una terminal dentro deun Pod
kubectl exec {name} -id -- bash

# Mostrar logs de un Pod
# params:
#   -f: mostrar los logs en tiempo real
#   --tail={num}: muestras las últimas {num} del log
#   -c {containerName}: para cuando un Pod tiene varios contenedores
kubectl logs {name}
kubectl logs {name} -f
kubectl logs {name} --tail={num}
kubectl logs {name} -c {containerName}

# Port-forward para acceder al servicio del Pod localmente
kubectl port-forward {name} {externalPort}:{internalPort}
```

## Comandos específicos de los Deployments

```bash
# Crear un deployment
kubectl create deploy {name} --image={dockerImage}

# Escala el número de réplicas de los Pods del Deployment
kubectl scale deploy {name} --replicas={num}
```

## Comandos específicos de los Services

```bash
# Desplegar un servicio
# params:
#   --name: nombre que daremos al servicio
#   --type: tipo de servicio (ClusterIP, NodePort, LoadBalancer)
#   --port: puerto que expone el servicio al mundo para que se conecten
kubectl expose pod {podName} --name={name}
kubectl expose pod {podName} --name={name} --type={svcType}
kubectl expose pod {podName} --name={name} --port={port}
```

## Comandos específicos de los Namespaces

```bash
# Crear un nuevo namespace
kubectl create ns {name}

# Obtener todos los eventos ocurridos en el namespace.
# Esta es la información que aparece al final al realizar
# un kubectl describe pod, pero de todos los pods.
# parms:
#   -n: especifica el namespace sobre el que operar
#   --field-selector: permite filtrar por los valores de una columna
kubectl get events
kubectl get events -n {namespace}
kubectl get events --field-selector {col}={value}
kubectl get events --field-selector type="Error"
```

# Dockerizar aplicaciones

El primer paso será crear nuestro Dockerfile donde se establecerá la imagen base, copiarán los ficheros necesarios, ejecutarán los comandos pertinentes y definirá el EntryPoint del contenedor.

Un vez configurado el Dockerfile en base a nuestras necesidades procedemos a generar la imagen y a publicarla en un repositorio público (Dockerhub). No olvides el `.` al final que es la ruta donde se encuentra el `Dockerfile` (en este ejemplo en el directorio donde nos encontramos).

```bash
# para la arquitectura actual
docker build -t user/name:tag .

# comprobamos que se ha generado correctamente
docker image ls

# login y pusheo al repositorio
docker login
docker push user/name:tag
```

## Versión multiarquitectura (opcional)

Para subir una imagen multiarquitectura disponemos de la utilidad buildx.

1. En el Docekrfile tenemos que añadir al comienzo la variable de entorno `ARG` y concatenarla al nombre de la imagen, como se aprecia a continuación.

```Dockerfile
ARG ARCH=
FROM ${ARCH}image:tag
```

2. Ahora, al construir la imagen le pasamos el parámetro ARCH de las distintas plataformas de las que queramos dar soporte (consultar tags de la imagen base). Si tu fichero Dockerfile no se llama con el nombre habitual (Dockerfile), debes especifica el nombre del mismo con el parámetro `-f`. De nuevo, no olvides que el `.` al final del comando indica la ruta donde se encuentra el Dockerfile.

```bash
docker buildx build --push --tag user/name:tag --platform linux/amd64,linux/arm/v7,linux/arm64 -f miDockerFile .
```
