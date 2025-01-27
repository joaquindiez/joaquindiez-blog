---
title: "Como escapar de Amazon AWS y ahorrar un 80% en costes"
categories:
  - tecnologia
---


Aun tengo en la memoria aquella tarde de otoño en el **Berlín** de 2007, en el edificio de convenciones donde se celebraba
la conferencia **Web 2.0**. Mike Culver( que en paz descanse) era un ingeniero de Amazon sentado en una silla de colegio,
con pequeño cartel que ponia Amazon ( y no se si algo e AWS) y que estaba enseñando a quien quiera verlo como arrancar
instancias de Ec2 desde una terminal de un portatil. Esas instancias, apenas tenias almacenamiento efimero, y apenas disponian
de una pocos tipos de instancia que podias levantar. Era el segundo servicio que ofrecian tras S3. Pero fue suficiente para
apostaramos todo el fundador de Unience ( ahora [Finect](https://www.finect.com)) a que esa fuera la base de nuestra plataforma
tecnológica para poner en producción nuestro producto. Habiamos entrado de lleno en el Cloud.

Y desde ese momento, me enamore de los Servicios de **Amazon AWS**, cada cosa que sacaban estaba mas y mas alineada a nuestras necesidade.
Las bases de Datos(RDS), las colas(SQS), el envio de email (SES), el DNS ( Route 53), Balanceadores (ELB) servicios
sencillos claros de utilizar y que a los desarrolladores y fundadores de StartUps nos daban la oportunida
de crear rápido y barato para validad nuestras ideas. Nos habian dado algo muy bueno.

Luego surgió **Microsoft Azure**, **Google con GCP** y hemos terminado en un infierno de soluciones cloud que poca gente entiende,
mucha menos gente entiende y que termina atándote a un proveedor de servicios.

Y lo peor es que estos proveedores son como los traficantes de droga, te dan tu dosis a bajo coste durante 12 meses y cuando ya estas enganchado
y no puedes dejarlo, los costes empiezan a escalar a tal punto que puede hacer tu modelo de negocio inviable.

Y no es una opinión, es un hecho, he visto esa escalada de costes en todas y cada una de las Startups en las que he estado y
como hemos tenido que hilar de fino para limitar los costes y no hacer tanto daño a las cuentas de resultados de la compañía.

Los servicios básicos de estos proveedores en general están muy bien y usarlos de forma pura, reduce dependencias a
los proveedores Cloud, el Stack Linux, Instancia, Almacenamiento, BBDD y acaso un Balanceador, es en general poco más de lo
que se necesita.

Pero luego vino, desde mi punto de vista el caos. El 95% de los ingenieros se creían que estaban construyendo el siguiente Facebook
o siguiente Netflix, y que desde el primer dia se necesitaban escalar por que iban a tener tanto éxito que millones de
usuarios iban a usar sus productos. Por supuesto era necesario hacer decenas de microservicios para su aplicacion de compra de
jugues para perros, decenas de micronservicios para un equipo de menos de 5 personas, habia que dockerizarlos y desplegarlos en
una plataforma que pudiera escalar, por supuesto **Kubernetes**, y ahi ya perdimos el sentido de la razón.

Ahora necesitabamos gente dedicada a DevOps, por que lo habiamos complicado todo tanto que necesitamos perfiles especificos
que gestionaran este lio. Esto perfiles no era baratos, resulta que cuando fuimos al cloud nos convencieron que esos perfiles
no eran necesarios, y ahora resulta que han tenido que regresar  encima con certificaciones específicas de cada una de Cloud,
por que lo hemos complicado tanto que la mayoria o saben uno o saben otro. Y por supuesto han de saber, complicadas herramientas
para poder gestionar toda esa infraestructura.

Por que a ver quien era el guapo que montaba 3 nodos ( minimo) de Kubernetes,
lo mantenia razonablemente actualizado y lo montaba para que se fueran añadiendo nuevos nodos, por si acaso hacia falta.
Y todo eso para aplicaciones que apenas tenian 10 usuario a la hora. Un sin sentido.

Consecuencia, lo que pretendia ser una acercamiento de los desarrolladores al infraestructura, ha derivado en equipos dedicados
que solo ellos pueden tocar o desplegar, alejando a los desarrolladores de como funciona su código en los servidores. Muchos ya ni
saben usar la terminal de comandos, y si les tienes que pedir que hagan algo si usar los servicios de AWS, ni saben.

Estamos locos.


Alguien me dirá que para gestionar Kubenetes esta  ECS y sus equivalente en Azure o GCP. Lo que quieran. Pocas empresas pueden justicar
la necesidad de su uso, y las que lo justifican es más por que a sus ingenieros les mola cacharrear y manejar lo último que
ha salido, pero si fueran totalemente sinceros y en muchos casos buenos profesionales, recomendarian no hacer uso de este
tipo de arquitectura. Es muy cara y no esta justificada.


Ojo, no digo que no haya empresas que necesiten escalar de esta forma, pero insisto, la mayoría no.

Y eso que trato de expresar no lo pienso yo solo, cada vez mas desarrolladores están dandose se cuenta de esta situación y
empieza a haber una mayor concienciación de que hemos complicado demasiado las cosas, que hay que volver a lo básico, desarrollar
de forma más sencilla con recursos que podamos controlar y volviendo a conocer donde corre nuestro código.

Gente como la de 37Signal, iniciaron hace dos años un movimiento de salir de Amazon AWS y controlar su servidores,
con los costes que tenian en AWS en 1 mes, compraron servidores tope de gama, cargados de cores y memoria y los instaron en
2 CPD en Chicago, simplemente alquilando el espacio done los alojaba y pagando un alquiler al gestor de CPD para mantener los servidores.

Y volvieron a tener el control de sus entornos en producción, para simplificar el proceso de despliegue, por su puesto eliminaron
Kubernetes, y desarrollaron su propia herramienta de despliegue [Kamal](https://kamal-deploy.org), que permite desplegar Imagenes
de Docker, en un entorno linux de forma sencilla, simplemente teniendo un Ubuntu básico instalado y una clave SSH. Kamal se encarga
de todo, instala Docker, monta un Proxy reverso, gestiona los certificados SSL, todo sencillo, documentado, y devolviendo el
control a los desarrolladores.

Empeze a usar Kamal el año pasado, primero para mis proyectos personales, desplegando en sencillas máquinas de 4 euros al mes
que me permitian dar servicio a cientos de usuarios!

Ahora migrando una plataforma de mi actual empresa que esta constando entre, Balanceador, 2 instancias y RDS, más de 140 euros al mes.
Migrando a (Hetzner)(https://hetzner.com) por un coste inferior a 30 euros al més y dando servicio sin incidencias a decenas de clientes.

Si necesitara escalar a miles o ciento de miles de usuarios, no dudaria en pagar por un par de maquinas de 300 euros con 16 cores,
128 Gb de RAM y al menos 3TB de almacenamiendo, en vez de gastarme 2400 euros al mes en AWS o Azure.

¿Necesito colas? Uso Rabbit ( y lo despliego con Kamal)
¿Necesito cache? Uso Redis
¿Base de datos? Postgres

Volver a lo básico de devuelve el control y la diversión. Y te permite escapar de una exclavitud y de un robo a nuestro bolsillo.

Y estoy convencido que muchas muchas empresas van a seguir este camino, por que tomar decisiones que te atan ca un proveedor
en general son decisiones de técnicos mediocres que se han olvidado a lo que nos dedicamos:

Solucionar problemas con código, de la forma más eficientemente posible
