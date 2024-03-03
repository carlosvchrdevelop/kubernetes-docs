# Listado de comandos de Minikube y Kubernetes

## Minikube

| Comando                                                                                 | Descripción                                                                                                                         |
| :-------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `minikube`                                                                              | Muestra las opciones disponibles de minikube                                                                                        |
| `minikube start [-p {nombreCluster}]`                                                   | Inicia un cluster. Si no se especifica -p, se inicia el cluster de minikube por defecto. Si el cluster no exsite se crea uno nuevo. |
| `minikube start [--driver={docker,kvm2}] [-p {nombreCluster}] [--nodes={numeroNodos}] ` | Crea un nuevo cluster.                                                                                                              |
| `minikube ip`                                                                           | Muestra la IP del cluster                                                                                                           |
| `minikube ssh`                                                                          | Se conecta al cluster por SSH                                                                                                       |
| `minikube dashboard`                                                                    | Crea un servicio web para visualizar nuestro cluster                                                                                |
| `minikube profile`                                                                      | Muestra el contexto actual                                                                                                          |
| `minikube profile list`                                                                 | Muestra la lista de contextos disponibles                                                                                           |
| `minikube delete -p {nombreCluster}`                                                    | Elimina un cluster de minikube                                                                                                      |
| `minikube config set memory {memoria. Ej: 4G} -p {nombreCluster}`                       | Cambia la configuración de memoria del cluster (necesario recrearlo)                                                                |
| `minikube config get memory -p {nombreCluster}`                                         | Permite consultar la memoria asignada al cluster                                                                                    |

## Kubernetes

Comandos generales.

| Comando                           | Descripción                                                                 |
| :-------------------------------- | :-------------------------------------------------------------------------- |
| `kubectl create -f {nombre}.yaml` | Crear un Pod desde un archivo yaml, si ya existe puede dat error            |
| `kubectl apply -f {nombre}.yaml`  | Crear un Pod desde un archivo yaml o lo actualiza si ya existe              |
| `kubectl proxy`                   | Expone una API REST para consultar información del clúster a través de HTTP |
| `kubectl delete all --all`        | Borra todo el cluster (servicios, pods, etc.)                               |
| `kubectl get po,rs,deploy`        | Obtiene información de varios recursos a la vez                             |

### Pods

| Comando                                                                       | Descripcón                                                                                                   |
| :---------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------- |
| `kubectl run {nombrePod} --image={imagenDocker}`                              | Crea un Pod con un contenedor de docker basado en una imagen dada                                            |
| `kubectl describe po {nombrePod}`                                             | Obtiene información muy detallada sobre un Pod                                                               |
| `kubectl get po`                                                              | Obtiene una lista con los Pods creados                                                                       |
| `kubectl get po {nombrePod}`                                                  | Obtiene información básica de un Pod en particular.                                                          |
| `kubectl get po -o wide`                                                      | Obtiene una lista más detallada con los Pods creados                                                         |
| `kubectl get pod {nombrePod} -o wide`                                         | Muestra información básica de un Pod, pero con algunos campos adicionales                                    |
| `kubectl get po {nombrePod} --show-labels`                                    | Como el anterior, pero muestra las labels del Pod                                                            |
| `kubectl get po --show-labels -l {etiqueta}={valor}`                          | Obtiene información básica de los pods que tengan una etiqueta {etiqueta} con valor {valor}                  |
| `kubectl get po --show-labels -l {etiqueta}!={valor}`                         | Como el anterior, pero realiza la búsqueda inversa                                                           |
| `kubectl get po --show-labels -l '({etiqueta} in ({valor1},{valor2},...)'`    | Como el anterior, pero en una lista de valores                                                               |
| `kubectl get po --show-labels -l '({etiqueta} notin ({valor1},{valor2},...)'` | Como el anterior, pero realiza la búsqueda inversa                                                           |
| `kubectl get po {nombrePod} -L {etiqueta,etiqeta2...}`                        | Como el anterior, pero añade una columna con la etiqueta seleccionada donde aparecerán los valores asignados |
| `kubectl get po {nombrePod} -o yaml > {nombre}.yaml`                          | Exporta la configuración de un Pod a un archivo yaml                                                         |
| `kubectl get po {nombrePod} -o json > {nombre}.json`                          | Exporta la configuración de un Pod a un archivo json                                                         |
| `kubectl delete po {nombrePod}`                                               | Elimnina un Pod                                                                                              |
| `kubectl delete po {nombrePod} --now`                                         | Elimnina un Pod inmediatamente, sin esperar a finalizar los procesos pendientes                              |
| `kubectl delete po {nombrePod} --grace-period={segundos}`                     | Elimnina un Pod dando un tiempo máximo para finalizar los procesos antes de forzarlo                         |
| `kubectl delete po --all`                                                     | Elimina todos los pods                                                                                       |
| `kubectl delete po -l '({etiqueta} in ({valor1},{valor2},...)'`               | Otro ejemplo de selectores, pero en este caso usado para borrar pods                                         |
| `kubectl label po {nombrePod} {nombreEtiqueta}={valor}`                       | Añade una etiqueta a un Pod                                                                                  |
| `kubectl exec {nombrePod} -- {comando}`                                       | Ejecuta un comando dentro del contedor de un Pod                                                             |
| `kubectl exec {nombrePod} -it -- bash`                                        | Entra a la terminal de un Pod en modo interactivo                                                            |
| `kubectl port-forward {nombrePod} {puertoExterno}:{puertoInterno}`            | Accede al servicio de un Pod a través de localhost sin desplegar un servicio                                 |
| `kubectl logs {nombrePod}`                                                    | Muestra los logs de un Pod. Útil para Pods como Apache o Nginx                                               |
| `kubectl logs -f {nombrePod}`                                                 | Muestra los logs de un Pod en tiempo real                                                                    |
| `kubectl logs {nombrePod} --tail={numeroLineas}`                              | Muestra las últimas n líneas de log de un Pod                                                                |
| `kubectl logs {nombrePod} -c {nombreContenedor}`                              | Muestra las últimas los logs de un contenedor de un Pod en Pods multicontenedor                              |

### Deployments y ReplicaSet

| Comando                                                          | Descripción                                                                                                     |
| :--------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| `kubectl create deployment {nombreDeploy} --image={dockerImage}` | Crea un nuevo deployment                                                                                        |
| `kubectl edit deploy {nombreDeploy}`                             | Permite editar la configuración del Deploy en yaml directamente desde consola de comandos y aplicar los cambios |
| `kubectl scale deploy {nombreDeploy} --replicas={nReplicas}`     | Escala un deploy estableciendo el número de réplicas de los Pods                                                |
| `kubectl get deploy`                                             | Muestra la lista de deployments                                                                                 |
| `kubectl describe deploy {nombreDeploy}`                         | Muestra información muy detallada de un deployment                                                              |
| `kubectl get rs`                                                 | Muestra la lista de replicasets                                                                                 |

### Servicios

| Comando                                                                                       | Descripción                                                                                      |
| :-------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| `kubectl expose pod {nombrePod} --name={nombreServicio} --port={puertoInterno} --type={Tipo}` | Expone un servicio para hacer público un Pod. el Tipo puede ser `LoadBalancer`, `PortNode`, etc. |
| `kubectl get svc`                                                                             | Obtiene la lista de servicios expuestos                                                          |
| `kubectl service {nombreServicio} --url`                                                      | Muestra la url para conectar con el servicio expuesto                                            |
| `kubectl delete service {nombreServicio}`                                                     | Elimina un servicio                                                                              |

# Aliases

| comando    | extended    | abbr short |
| :--------- | :---------- | :--------- |
| pod        | pods        | po         |
| deployment | deployments | deploy     |
| replicaset | replicasets | rs         |
| service    | services    | svc        |

# Docker

| Comando                                                 | Descripción                                    |
| :------------------------------------------------------ | :--------------------------------------------- |
| `docker build -t {usuario}/{nombreImagen}:{etiqueta} .` | Crea una imagen a partir de un Dockerfile      |
| `docker login`                                          | Inicia sesión en Dockerhub                     |
| `docker push {usuario}/{nombreImagen}:{etiqueta}`       | Sube a Dockerhub una imagen creada previamente |
| `docker push {usuario}/{nombreImagen}:{etiqueta}`       | Sube a Dockerhub una imagen creada previamente |
