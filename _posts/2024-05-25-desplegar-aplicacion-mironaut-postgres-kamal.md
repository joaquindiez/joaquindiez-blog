---
title: "Desplegar Micronaut con Postgresql, kamal en cualquier VPS"

categories:
  - tecnologia
---

En el siguiente artículo vamos a probar a desplegar una aplicación desarrollada con Micronaut 4.4.1 que se conecta a Postgresql y que se despliega en Kamal todo en el mismo servidor.

Una forma rápida de desplegar un producto y empezar a validar, usando servicios de bajo coste como los de Hetzner, los cuales uso habitualmente.

La aplicación crea una tabla de usuarios usando Liquibase, y ofrece dos endpoints, 1 para crear el usuario y otro para obtener la lista de los mismos.

El código de la aplicación Micronaut versión 4.4.1, con los ficheros de configuración de Kamal se encuentra en el siguiente repositorio de [Github](https://github.com/joaquindiez/micronaut-postgres-kamal)

Cualquier duda sobre como instalar Kamal, podéis referiros al primer articulo de las serie:

- [Como desplegar tu aplicación Micronaut con SSL en cualquier VPN usando Kamal](https://blog.joaquindiez.com/tecnologia/2024/05/24/desplegar-aplicacion-micronaut-kamal-ssl.html)

Si quieres jugar simplemente con la aplicación del repositorio, este ya viene con el código completo para que puedas probar el despliegue de la aplicación por lo que puedes saltar a la sección de **Ficheros Kamal.**

Si quieres crear la aplicacion de cero. estas son las instrucciones

## Creación aplicación Micronaut

Usando el cliente de Micronaut instalado con SDKMan, ejecutamos el siguiente comando

```jsx

mn create-app --lang kotlin \
	micronaut-kamal-postgresql \
	--build=gradle \
	--test=junit \
	--features data-jpa,liquibase,postgres,jdbc-hikari,management,graalvm,yaml,testcontainers
```

Configuración inicial de Kamal

```jsx
kamal init
```

## Ficheros Kamal

Contenido fichero config/deploy.yml

```jsx
# Name of your application. Used to uniquely configure containers.
service: my_app

# Name of the container image.
image: container_image

# Deploy to these servers.
servers:
   web:
     hosts:
      - XXX.XXX.XXX.XXX
     options:
        network: "private" # This is important!

# Credentials for your image host.
registry:
  # Specify the registry server, if you're not using Docker Hub
  # server: registry.digitalocean.com / ghcr.io / ...
  username: user

  # Always use an access token rather than real password when possible.
  password:
    - KAMAL_REGISTRY_PASSWORD

env:
  clear:
    DATASOURCES_DEFAULT_USERNAME: "postgres"
    DATASOURCES_DEFAULT_URL: "jdbc:postgresql://my_app-db:5432/my_awesome_app_production"
  secret:
    - DATASOURCES_DEFAULT_PASSWORD

# Configure where the Dockerfile is in Micronaut
builder:
  dockerfile: build/docker/main/Dockerfile
  context: "./build/docker/main"

accessories:
  db:
    image: postgres:16
    host: XXX.XXX.XXX.XXX
    # port: 5432 # Remove this line!
    env:
      clear:
        POSTGRES_USER: "postgres"
        POSTGRES_DB: "my_awesome_app_production" # The database will be created automatically on first boot.
      secret:
        - POSTGRES_PASSWORD
    directories:
      - data:/var/lib/postgresql/data
    options:
      network: "private" # This is important!

# Configure custom arguments for Traefik
traefik:
  options:
    network: "private"

# Configure a custom healthcheck (default is /up on port 3000)
healthcheck:
  path: /health
  port: 8080
  interval: 5s

```

1. Personaliza el valor de **image y registry.username** con los que correspondan. En este escenario se asume que estamos usando el registro de contenedores de docker.com
2. Crea tu Servidor en tu proveedor favorito ( Digital Ocen, Hetzner…) y modifica el valor la Ip en las propiedades Host.
3. En el fichero .env , modifica el valor de la propiedad

`KAMAL_REGISTRY_PASSWORD`

Fijate en el dichero .kamal/hooks/docker-setup

```jsx
#!/bin/sh

# A sample docker-setup hook
#
# Sets up a Docker network which can then be used by the application’s containers

ssh root@$KAMAL_HOSTS 'docker network create --driver bridge private'

```

En el momento que ejecutemos kamal setup, configuraremos nuestro Docker, creando un red llamada private, de esta forma nuestros servicios como el de base de datos, no estarán expuestos al exterior, por lo que ganamos en seguridad.

Ahora puedes desplegar tu aplicación, ejecuta

```jsx
kamal setup
```

Básicamente, este comando hará:

- Instalar Docker si aún no está instalado.
- Iniciar y configurar el contenedor Traefik.
- Cargar todas tus variables de entorno presentes en tu archivo **`.env`**.
- Construir y subir tu imagen Docker al registro.
- Iniciar un nuevo contenedor con la versión de la aplicación.

ahora todo debería estar arrancado y funcionado puedes comprobarlo con

Crear un nuevo Usuario

```jsx
 curl -X POST -H "Content-Type: application/json" -d '{"email": "your@email.com"}' http://to_ip_aqui/users

```

Obtener todos los usuarios

```jsx
curl http://to_ip_aqui/users

```
