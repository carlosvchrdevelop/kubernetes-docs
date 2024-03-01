# Listado de comandos de Minikube y Kubernetes

## Minikube

| Comando                                                                                 | Descripción                                                                                                                         |
| :-------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `minikube`                                                                              |                                                                                                                                     |
| `minikube start [-p {nombreCluster}]`                                                   | Inicia un cluster. Si no se especifica -p, se inicia el cluster de minikube por defecto. Si el cluster no exsite se crea uno nuevo. |
| `minikube start [--driver={docker,kvm2}] [-p {nombreCluster}] [--nodes={numeroNodos}] ` | Crea un nuevo cluster.                                                                                                              |
| `minikube ip`                                                                           | Muestra la IP del cluster                                                                                                           |
| `minikube ssh`                                                                          | Se conecta al cluster por SSH                                                                                                       |
| `minikube profile`                                                                      | Muestra el contexto actual                                                                                                          |
| `minikube profile list`                                                                 | Muestra la lista de contextos disponibles                                                                                           |
| `minikube delete -p {nombreCluster}`                                                    | Elimina un cluster de minikube                                                                                                      |
| `minikube config set memory {memoria. Ej: 4G} -p {nombreCluster}`                       | Cambia la configuración de memoria del cluster (necesario recrearlo)                                                                |
| `minikube config get memory -p {nombreCluster}`                                         | Permite consultar la memoria asignada al cluster                                                                                    |

## Kubernetes

### Pods

| Comando                                                            | Descripcón                                                                           |
| :----------------------------------------------------------------- | :----------------------------------------------------------------------------------- |
| `kubectl create -f {nombre}.yaml`                                  | Crear un Pod desde un archivo yaml                                                   |
| `kubectl run {nombrePod} --image={imagenDocker}`                   | Crea un Pod con un contenedor de docker basado en una imagen dada                    |
| `kubectl get pods`                                                 | Obtiene una lista con los Pods creados                                               |
| `kubectl get pods -o wide`                                         | Obtiene una lista más detallada con los Pods creados                                 |
| `kubectl delete pod {nombrePod}`                                   | Elimnina un Pod                                                                      |
| `kubectl delete pod {nombrePod} --now`                             | Elimnina un Pod inmediatamente, sin esperar a finalizar los procesos pendientes      |
| `kubectl delete pod {nombrePod} --grace-period={segundos}`         | Elimnina un Pod dando un tiempo máximo para finalizar los procesos antes de forzarlo |
| `kubectl delete pods --all`                                        | Elimina todos los pods                                                               |
| `kubectl describe pod/{nombrePod}`                                 | Obtiene información muy detallada sobre un Pod                                       |
| `kubectl get pod/{nombrePod} -o yaml > {nombre}.yaml`              | Exporta la configuración de un Pod a un archivo yaml                                 |
| `kubectl get pod/{nombrePod} -o json > {nombre}.json`              | Exporta la configuración de un Pod a un archivo json                                 |
| `kubectl exec {nombrePod} -- {comando}`                            | Ejecuta un comando dentro del contedor de un Pod                                     |
| `kubectl exec {nombrePod} -it -- bash`                             | Entra a la terminal de un Pod en modo interactivo                                    |
| `kubectl port-forward {nombrePod} {puertoExterno}:{puertoInterno}` | Accede al servicio de un Pod a través de localhost sin desplegar un servicio         |
| `kubectl logs {nombrePod}`                                         | Muestra los logs de un Pod. Útil para Pods como Apache o Nginx.                      |
| `kubectl logs -f {nombrePod}`                                      | Muestra los logs de un Pod en tiempo real                                            |
| `kubectl logs {nombrePod} --tail={numeroLineas}`                   | Muestra las últimas n líneas de log de un Pod                                        |

### Servicios

| Comando                                                                                       | Descripción                                                                                      |
| :-------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| `kubectl expose pod {nombrePod} --name={nombreServicio} --port={puertoInterno} --type={Tipo}` | Expone un servicio para hacer público un Pod. el Tipo puede ser `LoadBalancer`, `PortNode`, etc. |
| `kubectl get services`                                                                        | Obtiene la lista de servicios expuestos                                                          |
| `kubectl service {nombreServicio} --url`                                                      | Muestra la url para conectar con el servicio expuesto                                            |
| `kubectl delete service {nombreServicio}`                                                     | Elimina un servicio                                                                              |

## Otros

| Comando                                                 | Descripción                                                                 |
| :------------------------------------------------------ | :-------------------------------------------------------------------------- |
| `kubectl proxy`                                         | Expone una API REST para consultar información del clúster a través de HTTP |
| `docker build -t {usuario}/{nombreImagen}:{etiqueta} .` | Crea una imagen a partir de un Dockerfile                                   |
| `docker login`                                          | Inicia sesión en Dockerhub                                                  |
| `docker push {usuario}/{nombreImagen}:{etiqueta}`       | Sube a Dockerhub una imagen creada previamente                              |
| `docker push {usuario}/{nombreImagen}:{etiqueta}`       | Sube a Dockerhub una imagen creada previamente                              |
