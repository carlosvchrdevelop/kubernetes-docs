# Quick Start

```bash
# Inicia minikube
minikube start

# Comprueba que minikube está corriendo
minikube status

# Aplica la configuración de todos los archivos yaml de la carpeta
kubectl appy -f .

# Consulta el nombre del pod creado
kubectl get po

# Accede dentro del pod
kubectl exec [nombrePod] -it -- bash

# Intenta acceder a la base de datos mysql con las credenciales de configmap-mysql.yaml y secret-mysql.yaml
# -u: usuario, -p: nombre de base de datos. Al acceder, nos pedirá la base de datos.
mysql -u kubernetes -p kubernetes
> kubernetes
```
