---
title: "Como despleglar tu aplicación Micronaut usando Kamal, con certificado SSL en cualquier VPS"

categories:
  - tecnologia
---

[Kamal](https://kamal-deploy.org/) es una herramienta que te ayuda a desplegar tus aplicaciones web en cualquier lugar de la nube o en tus propias máquinas con Docker.

Citando su web:

Kamal ofrece despliegues sin tiempo de inactividad, reinicios continuos, puenteo de activos, construcciones remotas, gestión de servicios accesorios y todo lo que necesitas para desplegar y gestionar tu aplicación web en producción con Docker. Originalmente construido para aplicaciones Rails, Kamal trabajará con cualquier tipo de aplicación web que puede ser en contenedores.


![image-center]({{ '/images/kamal.png' | absolute_url }}){: .align-center}

Llevo varios años trabajando para empresas que utilizan Kubernetes, principalmente a través de los servicios gestionados de Amazon AWS y Microsoft Azure. En ambos casos, he observado un aumento considerable en los costos y en la complejidad de la gestión de aplicaciones.

Esta situación ha llevado a la necesidad de contratar ingenieros especializados en la administración de Kubernetes, quienes también se esfuerzan por simplificar su uso para los demás desarrolladores y así evitar aumentar la carga cognitiva que enfrentan al resolver problemas.

A este aumento de costos en los servicios de Cloud, se añade también el incremento en gastos de personal, desmintiendo el mito de que al usar servicios en la nube ya no es necesario contar con personal de sistemas, quienes ahora son conocidos como DevOps.

Además, se observan errores en la toma de decisiones por parte de los CTOs y líderes técnicos. A menudo, desde las primeras etapas de desarrollo de un producto, sin tener claridad sobre las necesidades futuras de uso o escalabilidad, optan por implementar Kubernetes y asumen todas las complicaciones asociadas a esta elección.

La realidad, es que cuando empezamos un desarrollo de producto, lo que necesitamos:

- velocidad, para desarrollar, desplegar y poner nuestro código en manos de los usuarios y clientes lo antes posible.
- costes bajos de infraestructura por que queremos optimizar lo más posible nuestros recursos
- tener a la gente enfocada en los importante, y tener a la gente necesaria haciendo lo importante sin perder el tiempo en otras cosas.

Todas estas cosas son las que la gente de BaseCamp detectaron hace año y pico y decidieron migrar sus aplicaciones de Amazon AWS  y volver a hacer las cosas de formas más simples, sin complejidades no necesarias. Y de paso nos regalaron a la comunidad [Kamal](https://kamal-deploy.org/),  su herramienta para desplegar servicios en cualquier máquina o VPS, solo usando Docker.

Ellos desarrollan todo usando Ruby on Rail, y Kamal esta desarrollado en Ruby, pero es perfecta para desplegar cualquier servicio que este contenerizado y aqui muestro los pasos para desplegar servicios desarrollados con [Micronaut](https://micronaut.io/), mi framework de referencia

Es muy interesante  que leas ( y te animo a ello) su sección de visión en su página de inicio porque explica muy bien la filosofía que hay detrás.

Esto es lo que he aprendido y cómo hacerlo.

En esta entrada del blog, vamos a ver cómo:

- Desplegar tu aplicación Micronaut en Docker con Kamal
- Configurar tus DNS
- Configurar Traefik para generar un certificado SSL automáticamente
- Configurar y asegurar tu servidor

## Instalar Kamal

Kamal viene como un Gema de Ruby

```jsx
gem install kamal
```

Comprobamos que se ha instalado correctamente

```jsx
kamal version

```

## Crear Proyecto Micronaut

```jsx
mn create-app example.micronaut.micronaut-kamal \
    --features=management,graalvm \
    --build=gradle \
    --lang=kotlin \
    --test=junit
```

Compilamos y ejecutamos para comprobar que todo esta bien

```jsx
cd micronaut-kamal
./gradlew build
./gradlew run
```

el servicio esta arrancado y podemos ejecutar la siguiente linea para comprobar que el endpoint de /health esta sirviendo correctamente

```jsx
$ curl http://localhost:8080/health

{"status":"UP"}%
```

Tenemos nuestro servicio funcionando podemos salir de la ejecucion ctrl+C y seguir

## Git init  ( inicia el proyecto en git)

en el directorio del servicio micronaut-kamal

```jsx
git init
git add .
git commit -m "first commit"
```

## kamal init

en el mismo directorio para a iniciar Kamal en el

```jsx
$ kamal init
```

Este comando va a crear los siguientes archivos

```jsx
Created configuration file in config/deploy.yml
Created .env file
Created sample hooks in .kamal/hooks
```

Este comando creará al menos dos archivos importantes, **`.env`** y **`config/deploy.yml`**.

El primero es bastante sencillo. Aquí es donde establecerás la mayoría de tus variables de entorno que serán enviadas a tus servidores.

El segundo es donde vive tu configuración de Kamal.

Si usas el Docker Hub predeterminado, el **`KAMAL_REGISTRY_PASSWORD`** en tu **`.env`** debe ser un token, no tu contraseña real.

Ver:  [https://docs.docker.com/security/for-developers/access-tokens/](https://docs.docker.com/security/for-developers/access-tokens/**) para más información.

El archivo más importante, **`config/deploy.yml`**:

- Configuramos la imagen del contenedor  end [docker.com](http://docker.com)
- Cambiamos al builder para que coja los ficheros de Dockerfile que genera Micronaut/

```jsx
# Name of your application. Used to uniquely configure containers.
service: micronaut-kamal

# Name of the container image.
image: my-user/micronaut-kamal

# Deploy to these servers.
servers:
  - 192.168.0.1

# Credentials for your image host.
registry:
  # Specify the registry server, if you're not using Docker Hub
  # server: registry.digitalocean.com / ghcr.io / ...
  username: my-user

  # Always use an access token rather than real password when possible.
  password:
    - KAMAL_REGISTRY_PASSWORD

# Configure where the Dockerfile is in Micronaut
builder:
  dockerfile: build/docker/main/Dockerfile
  context: "./build/docker/main"


# Configure a custom healthcheck (default is /up on port 3000)
healthcheck:
  path: /health
  port: 8080
  interval: 5s

```

Como ya he comentado, Kamal esta especialmente alineado con las aplicaciones Ruby On Rails para que todo funcione por defecto, pero sin embargo es tremendamente flexible para configurarlo para nuestras aplicaciones.

Micronaut, por defecto, no genera un Dockerfile hasta que no ejecutas el comando dockerBuild, y donde genera el fichero no es en la raiz del código, por lo que hay que realizar los siguientes cambios

```jsx
# Configure where the Dockerfile is in Micronaut
builder:
  dockerfile: build/docker/main/Dockerfile
  context: "./build/docker/main"
```

y queda configurar el healthcheck, es Rails, por defecto, es /up y se sirven las aplicaciones en el puerto 3000, para Micronaut lo cambiamos al puerto 8080 y /health

```jsx
# Configure a custom healthcheck (default is /up on port 3000)
healthcheck:
  path: /health
  port: 8080
  interval: 5s
```

## Construir proyecto Micronaut

```jsx
./gradlew dockerBuild
```

on este comando ya construimos el proyecto y generamos el contenedor y el fichero Docker file

## Crear Clave SSH y añadirla a ssh-add

Kamal ejecuta comandos en nuestros servidores usando ssh, si no lo tienes previamente creado, estos son los comando para crear la clave ssh

```jsx
ssh-keygen -t ed25519 -C "your_email@example.com"

```

### Añadirla con ssh-add

```jsx
 ssh-add ~/.ssh/your-key-generated
```

## Primer Deploy

Ahora estamos listos para desplegar nuestra aplicación por primera vez, pero antes necesitamos configurar Kamal:

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

```jsx
$ curl http://your_ip_address/health

{"status":"UP"}%
```

Ver detalle contenedores

```jsx
kamal details
```

Ver logs

```jsx
 kamal app logs
```

## Configurando SSL

Volvemos a nuestro fichero deploy.xml y hacemos los siguientes cambios, para configurar Traefik

```jsx
# Name of your application. Used to uniquely configure containers.
service: micronaut-kamal

# Name of the container image.
image: my-user/micronaut-kamal

servers:
  web: # Use a named role, so it can be used as entrypoint by Traefik
    hosts:
      - 192.168.0.1
    labels:
      traefik.http.routers.web.entrypoints: websecure
      traefik.http.routers.web.rule: Host(`your_web.com`)
      traefik.http.routers.web.tls.certresolver: letsencrypt

# Credentials for your image host.
registry:
  # Specify the registry server, if you're not using Docker Hub
  # server: registry.digitalocean.com / ghcr.io / ...
  username: joaquindiez75

  # Always use an access token rather than real password when possible.
  password:
    - KAMAL_REGISTRY_PASSWORD

# Configure where the Dockerfile is in Micronaut
builder:
  dockerfile: build/docker/main/Dockerfile
  context: "./build/docker/main"

# Configure a custom healthcheck (default is /up on port 3000)
healthcheck:
  path: /health
  port: 8080
  interval: 5s

# Configure custom arguments for Traefik
traefik:
  options:
    publish:
      - "443:443"
    volume:
      - "/letsencrypt/acme.json:/letsencrypt/acme.json" # To save the configuration file.
  args:
    entryPoints.web.address: ":80"
    entryPoints.websecure.address: ":443"
    entryPoints.web.http.redirections.entryPoint.to: websecure # We want to force https
    entryPoints.web.http.redirections.entryPoint.scheme: https
    entryPoints.web.http.redirections.entrypoint.permanent: true
    certificatesResolvers.letsencrypt.acme.email: "your@email_here.com"
    certificatesResolvers.letsencrypt.acme.storage: "/letsencrypt/acme.json" # Must match the path in `volume`
    certificatesResolvers.letsencrypt.acme.httpchallenge: true
    certificatesResolvers.letsencrypt.acme.httpchallenge.entrypoint: web # Must match the role in `servers`

```

Nuestros servidores necesitan tener el directorio /letscrypt y el archivo /letsencrypt/acme.json creados. Para esto, hay varias soluciones.

Algunas personas utilizan Ansible para automatizar este proceso, pero yo prefiero aprovechar todas las capacidades de Kamal.

Nos dirigimos al directorio .kamal/hooks y creamos un archivo llamado docker-setup en el que insertamos las siguientes líneas.

```jsx
#!/bin/sh

# A sample docker-setup hook
#
# Sets up a Docker network which can then be used by the application’s containers

ssh root@$KAMAL_HOSTS 'mkdir -p /letsencrypt && touch /letsencrypt/acme.json && chmod 600 /letsencrypt/acme.json'

```

y damos permisos de ejecución a ese fichero

```jsx
chmod +x .kamal/hooks/docker-setup
```

ya estamos listos podemos volver a configurar nuestra aplicación y desplegarla de nuevo, ejecutamos de nuevo

```jsx
kamal setup
kamal traefik reboot // pare reiniciar el servidor traefik con la configuracion segura
```

```jsx
curl https://your_web.com/health

{"status":"UP"}%

```

Con este comando, puedes verificar que tu servicio está correctamente desplegado y operando de manera segura.

SI quieres desplegar una nueva versión de tu aplicación tan solo tienes que hacer

```jsx
kamal deploy
```

Si necesitas actualizar las variables de entorno

```jsx
kamal env push
```

y si necesitas ver los logs de la aplicación

```jsx
kamal logs
```

El código de este proyecto esta publicado en [github](https://github.com/joaquindiez/micronaut-kamal)

## Ultimo comentario

Si despliegas tu aplicación en multiples servidores automáticamente con Kamal, esta configuración de  Traefik que he presentado es irrelevante, ya que necesitarás un balanceador de carga, el resto permanece de forma equivalente.

Para la configuración del balanceador de carga con SSL es mejor referirse al proveedor que utilices habitualmente.

Pero para desplegar tus pilotos, de forma rápida y empezar a validar, con esto ya tienes toda la magia montada.

A programar y a disfrutar!!
