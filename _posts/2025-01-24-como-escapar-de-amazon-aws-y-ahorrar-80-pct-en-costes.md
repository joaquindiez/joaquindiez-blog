Aún tengo en la memoria aquella tarde de otoño en el Berlín de 2007, en el edificio de convenciones donde se celebraba la conferencia Web 2.0. Mike Culver (que en paz descanse) era un ingeniero de Amazon sentado en una silla de colegio, con un pequeño cartel que ponía Amazon (y no sé si algo de AWS) y que estaba enseñando a quien quisiera verlo cómo arrancar instancias de EC2 desde una terminal de un portátil. Esas instancias apenas tenían almacenamiento efímero y disponían de pocos tipos de instancia que podías levantar. Era el segundo servicio que ofrecían tras S3. Pero fue suficiente para que apostáramos todo como fundadores de Unience (ahora Finect) a que esa fuera la base de nuestra plataforma tecnológica para poner en producción nuestro producto. Habíamos entrado de lleno en el Cloud.

Y desde ese momento, me enamoré de los servicios de Amazon AWS. Cada cosa que sacaban estaba más y más alineada a nuestras necesidades: las bases de datos (RDS), las colas (SQS), el envío de email (SES), el DNS (Route 53), balanceadores (ELB), servicios sencillos, claros de utilizar y que a los desarrolladores y fundadores de startups nos daban la oportunidad de crear rápido y barato para validar nuestras ideas. Nos habían dado algo muy bueno.

Luego surgieron Microsoft Azure, Google con GCP y hemos terminado en un infierno de soluciones cloud que poca gente entiende, mucha menos gente las entiende y que terminan atándote a un proveedor de servicios.

Y lo peor es que estos proveedores son como los traficantes de droga: te dan tu dosis a bajo coste durante 12 meses y, cuando ya estás enganchado y no puedes dejarlo, los costes empiezan a escalar a tal punto que puede hacer tu modelo de negocio inviable.

Y no es una opinión, es un hecho. He visto esa escalada de costes en todas y cada una de las startups en las que he estado y cómo hemos tenido que hilar de fino para limitar los costes y no hacer tanto daño a las cuentas de resultados de la compañía.

Los servicios básicos de estos proveedores en general están muy bien y usarlos de forma pura reduce dependencias a los proveedores cloud. El stack Linux, instancia, almacenamiento, BBDD y acaso un balanceador, es en general poco más de lo que se necesita.

Pero luego vino, desde mi punto de vista, el caos. El 95% de los ingenieros se creían que estaban construyendo el siguiente Facebook o el siguiente Netflix, y que desde el primer día se necesitaba escalar porque iban a tener tanto éxito que millones de usuarios iban a usar sus productos. Por supuesto, era necesario hacer decenas de microservicios para su aplicación de compra de juguetes para perros, decenas de microservicios para un equipo de menos de 5 personas, había que dockerizarlos y desplegarlos en una plataforma que pudiera escalar, por supuesto Kubernetes, y ahí ya perdimos el sentido de la razón.

Ahora necesitábamos gente dedicada a DevOps, porque lo habíamos complicado todo tanto que necesitamos perfiles específicos que gestionaran este lío. Estos perfiles no eran baratos, resulta que cuando fuimos al cloud nos convencieron de que esos perfiles no eran necesarios, y ahora resulta que han tenido que regresar encima con certificaciones específicas de cada uno de los cloud, porque lo hemos complicado tanto que la mayoría o saben uno o saben otro. Y, por supuesto, han de saber complicadas herramientas para poder gestionar toda esa infraestructura.

Porque a ver, quién era el guapo que montaba 3 nodos (mínimo) de Kubernetes, lo mantenía razonablemente actualizado y lo montaba para que se fueran añadiendo nuevos nodos por si hacía falta. Y todo eso para aplicaciones que apenas tenían 10 usuarios a la hora. Un sinsentido.

Consecuencia: lo que pretendía ser un acercamiento de los desarrolladores a la infraestructura ha derivado en equipos dedicados que solo ellos pueden tocar o desplegar, alejando a los desarrolladores de cómo funciona su código en los servidores. Muchos ya ni saben usar la terminal de comandos, y si les tienes que pedir que hagan algo sin usar los servicios de AWS, ni saben.

Estamos locos.

Alguien me dirá que para gestionar Kubernetes está ECS y sus equivalentes en Azure o GCP, lo que quieran. Pocas empresas pueden justificar la necesidad de su uso, y las que lo justifican es más porque a sus ingenieros les mola cacharrear y manejar lo último que ha salido, pero si fueran totalmente sinceros y en muchos casos buenos profesionales, recomendarían no hacer uso de este tipo de arquitectura. Es muy cara y no está justificada.

Ojo, no digo que no haya empresas que necesiten escalar de esta forma, pero insisto, la mayoría no.

Y eso que trato de expresar no lo pienso yo solo, cada vez más desarrolladores están dándose cuenta de esta situación y empieza a haber una mayor concienciación de que hemos complicado demasiado las cosas, que hay que volver a lo básico, desarrollar de forma más sencilla con recursos que podamos controlar y volviendo a conocer dónde corre nuestro código.

Gente como la de 37Signal inició hace dos años un movimiento de salir de Amazon AWS y controlar sus servidores. Con los costes que tenían en AWS en un mes, compraron servidores tope de gama, cargados de cores y memoria, y los instalaron en 2 CPD en Chicago, simplemente alquilando el espacio donde los alojaba y pagando un alquiler al gestor de CPD para mantener los servidores.

Y volvieron a tener el control de sus entornos en producción, para simplificar el proceso de despliegue, por supuesto eliminaron Kubernetes y desarrollaron su propia herramienta de despliegue Kamal, que permite desplegar imágenes de Docker en un entorno Linux de forma sencilla, simplemente teniendo un Ubuntu básico instalado y una clave SSH. Kamal se encarga de todo, instala Docker, monta un proxy reverso, gestiona los certificados SSL, todo sencillo, documentado y devolviendo el control a los desarrolladores.

Empecé a usar Kamal el año pasado, primero para mis proyectos personales, desplegando en sencillas máquinas de 4 euros al mes que me permitían dar servicio a cientos de usuarios.

Ahora migrando una plataforma de mi actual empresa que está constando entre balanceador, 2 instancias y RDS, más de 140 euros al mes, migrando a Hetzner por un coste inferior a 30 euros al mes y dando servicio sin incidencias a decenas de clientes.

Si necesitaras escalar a miles o cientos de miles de usuarios, no dudaría en pagar por un par de máquinas de 300 euros con 16 cores, 128 GB de RAM y al menos 3 TB de almacenamiento, en vez de gastarme 2400 euros al mes en AWS o Azure.

¿Necesito colas? Uso Rabbit (y lo despliego con Kamal).

¿Necesito cache? Uso Redis.

¿Base de datos? Postgres.

Volver a lo básico devuelve el control y la diversión. Y te permite escapar de una esclavitud y de un robo a nuestro bolsillo.

Y estoy convencido de que muchas, muchas empresas van a seguir este camino, porque tomar decisiones que te atan a un proveedor en general son decisiones de técnicos mediocres que se han olvidado a lo que nos dedicamos:

Solucionar problemas con código, de la forma más eficientemente posible.
