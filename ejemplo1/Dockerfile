# Descargamos ubuntu
FROM ubuntu

## Actualizamos los repositorios
RUN apt update

## En algunas versiones de Linux es necesario configurar el Timezone
ENV TZ=Europe/Madrid

## Luego creamos un fichero llamado /etc/timezone para configurar
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

## Instalamos Nginx
RUN apt install -y nginx

## Creamos un index.html personalizado en el directorio por defecto de Nginx
RUN echo 'Ejemplo de POD con Kubernetes y .yaml' > /var/www/html/index.html

## Arrancamos Nginx a través del ENTRYPOINT para que no pueda ser modificado en la creación del contenedor
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]

## Exponemos el puerto 80
EXPOSE 80
