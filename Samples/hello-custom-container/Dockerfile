# Descargamos ubuntu
FROM ubuntu

## Actualizamos los repositorios
RUN apt update

## Configuramos el Timezone (necesarios en algunas distribuciones)
ENV TZ=Europe/Madrid

## Creamos un fichero llamado /etc/timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

## Instalamos Nginx
RUN apt install -y nginx

## Creamos un index.html personalizado en el directorio por defecto de Nginx
RUN echo '<h1>Hola mundo!</h1>' > /var/www/html/index.html

## Arrancamos Nginx a través del ENTRYPOINT para que no pueda ser modificado en la creación del contenedor
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]

## Exponemos el puerto 80
EXPOSE 80
