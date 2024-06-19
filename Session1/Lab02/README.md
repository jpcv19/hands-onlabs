---
Order: 2
Area: languages
TOCTitle: Crear un Contenedor de Nginx
ContentID: 
PageTitle: Session 1 - Lab 02
DateApproved: 16/05/2024
MetaDescription: Crear un Contenedor de Nginx
---

# Crear un Contenedor de Nginx

## Propósito

Este ejercicio tiene como objetivo demostrar de forma práctica los siguientes comando:

* docker pull
* docker image
* docker run
* docker container stats
* docker rm

**Paso 1:** Navegar al Directorio de Trabajo y Descargar la Imagen Oficial de Nginx

1. Abrir la terminal: Abre tu terminal o línea de comandos.
2. Navega hasta el directorio `~\source\repos\lab01\src\`Ejecutar el comando docker pull:

 ```powershell
 docker pull nginx
 ```

 Esto descarga la imagen oficial de Nginx desde Docker Hub.
3. Validar que la imagen se descargó correctamente docker image

 ```powershell
 docker image list nginx
 ```

 Utilice el modificador list y el nombre de la imagen a buscar como parámetro

**Paso 2:** Crear y Ejecutar el Contenedor de Nginx

1. Ejecutar el comando docker run:

 ```powershell
 docker run -d --name app_web -p 8083:80 nginx
 ```

 Esto hará lo siguiente:

* -d: Ejecuta el contenedor en modo desacoplado (detached).
* --name app_web: Asigna el nombre app_web al contenedor.
* -p 8083:80: Mapea el puerto 8083 del host al puerto 80 del contenedor.
* nginx: Utiliza la imagen de Nginx para crear el contenedor.

**Paso 3:** Desplegar nuestra Aplicación Web

1. Copiar los archivos HTML y CSS al contenedor:

 ```powershell
 docker cp index.html app_web:/usr/share/nginx/html/index.html
 docker cp styles.css app_web:/usr/share/nginx/html/styles.css
 ```

**Paso 4:** Verificar la Configuración

1. Abrir un navegador web.
2. Navegar a `http://localhost:8083`.
3. Verificar que se muestre la página HTML personalizada con el contenido y el estilo CSS que has creado.

![Vista Previa](img/preview.png)

**Paso 5:** Eliminar el Contenedor
Eliminar el contenedor:

```powershell
docker rm app_web --force
```

Esto hará lo siguiente:

* --force: fuerza el borrado del contenedor aun que este en uso

Resumen del Ejercicio
Descargar la imagen de Nginx:
docker pull nginx
Crear y ejecutar el contenedor de Nginx:
 `docker run -d --name app_web -p 8083:80 nginx`
Copiar los archivos al contenedor:
 `docker cp index.html app_web:/usr/share/nginx/html/index.html`
 `docker cp styles.css app_web:/usr/share/nginx/html/styles.css`
Verificar el resultado en el navegador:
Ir a `http://localhost:8083` para ver la página personalizada.
Eliminar el contenedor:

```powershell
docker rm app_web --force
```

Este ejercicio te ayuda a familiarizarte con la descarga de imágenes Docker, la creación y ejecución de contenedores, y la manipulación de archivos dentro de un contenedor.