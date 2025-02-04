---
title: "Serverless is useless"
categories:
  - tecnologia
---

😃🚀🔥 El modelo serverless ha ganado popularidad en los últimos años debido a su escalabilidad automática, reducción de costos operativos y facilidad de implementación.

Sin embargo, en mi opinión, es una de las peores decisiones tecnológicas que una compañía puede adoptar, y sin lugar a dudas, un suicidio a medio o largo plazo. 😵‍💫⚠️⏳

Este artículo es, lógicamente, totalmente "opinativo". Es mi visión sobre cómo tomar decisiones sensatas a la hora de desarrollar un producto sin poner en riesgo ni el futuro del producto ni el de la compañía. 🤔💭💡

¿Por qué alguien con mínimos conocimientos de ingeniería de software decidiría hacer un producto usando serverless? 🤷‍♂️🖥️💻


Pues por el mismo motivo que, a principios de siglo, en las grandes organizaciones siempre se compraban productos de IBM, Microsoft u Oracle.
**Siempre se decía que si elegías a IBM, aunque fuera la peor decisión del mundo, no te iban a despedir**. 📈🏢🛑

Ahora ocurre lo mismo con los productos de **Microsoft Azure** o **Amazon AWS**.
Si decides que toda tu infraestructura corra en sus servicios y, además, adoptas toda su arquitectura de aplicaciones,
nadie te va a culpar ni decir que has tomado una mala decisión. 🏗️🔗📊

Como dice Seth Godin:

> Las organizaciones promocionan a los que toman malas decisiones pero tienen suerte con el resultado. Eso se debe  a que miden las cosas que no deben. En vez de promocionar a aquellos que toman las decisiones adecuadas pero que tal vez no tengan suerte con el resultado. 🎯🎲📌

Hoy en día, si planteo seriamente el desarrollo de un producto en el que quiero tener buena trazabilidad del código, poder depurarlo, monitorizarlo y tener control de los recursos, no se me ocurre ninguna circunstancia en la que quisiera hacer que mi código fuera serverless y permanecer esclavo de las dependencias de una plataforma cloud. 🔍🛠️👨‍💻

Con las estupendas herramientas actuales, mi favorita [Kamal-deploy.org](https://Kamal-deploy.org), y los servicios tan low-cost que tenemos a nuestra disposición, no veo cómo justificar meterme en todos estos problemas que enumero a continuación. 💸📉🚀

Lista de problemas al usar Serverless

**1.Latencia en Cold Starts**

Uno de los mayores problemas de las arquitecturas serverless es el "cold start". Cuando una función no ha sido ejecutada recientemente, el proveedor de la nube necesita aprovisionar y arrancar una nueva instancia, lo que genera una latencia adicional en la primera ejecución. Esto puede ser crítico en aplicaciones de baja latencia o en sistemas con requisitos de respuesta inmediata. 🕒⚡❄️

**2.Dependencia del Proveedor (Vendor Lock-in)**

Cada proveedor de servicios en la nube tiene su propia implementación de serverless, lo que puede dificultar la migración entre plataformas. AWS Lambda, Google Cloud Functions y Azure Functions tienen distintas configuraciones, tiempos de ejecución y limitaciones, lo que obliga a los desarrolladores a adaptar su código y configuración a cada plataforma, aumentando la dependencia y reduciendo la flexibilidad. 🔄🔗🚧

**3.Límites en los Recursos y Ejecución**

Las plataformas serverless imponen restricciones en el uso de memoria, tiempo de ejecución y concurrencia. Por ejemplo, AWS Lambda tiene un tiempo máximo de ejecución de 15 minutos y un límite de memoria de 10 GB. Estas limitaciones pueden hacer que serverless no sea adecuado para aplicaciones con procesamiento intensivo o tareas de larga duración. 🚀🔢⏳

**4.Mayor Complejidad en la Depuración y Monitoreo**

Las aplicaciones serverless suelen estar distribuidas en múltiples funciones, lo que complica la depuración y el monitoreo. No se cuenta con acceso directo al servidor para revisar logs en tiempo real, lo que puede dificultar la identificación de errores y cuellos de botella en el rendimiento. Aunque los proveedores ofrecen herramientas de monitoreo como AWS CloudWatch o Google Cloud Logging, estas suelen ser limitadas o requieren configuraciones adicionales. 🔍📊⚠️

**5.Costos Ocultos y Dificultad en la Estimación**

Si bien serverless se presenta como una solución de bajo costo, en algunos casos los costos pueden ser impredecibles. Al estar basado en el número de invocaciones y el tiempo de ejecución, es difícil calcular de antemano el costo total, especialmente en aplicaciones con tráfico variable. Además, otros servicios complementarios como almacenamiento, bases de datos o redes pueden incrementar significativamente el costo final. 💰📉❓

**6.Limitaciones en el Control y la Configuración**

Con serverless, el usuario no tiene control total sobre el entorno de ejecución. No es posible optimizar hardware específico, configurar ciertos aspectos del sistema operativo o instalar dependencias personalizadas sin restricciones. Esto puede ser un problema para empresas que requieren un alto nivel de personalización en sus aplicaciones. 🚀⚙️🔒

Si, tras conocer esta lista de seis problemas que hacen que tu aplicación tenga un serio problema de fragilidad el día que ocurra algo inesperado en tu cloud de elección, aún decides que es la mejor opción posible, estaré encantado de que compartas conmigo tus motivos para debatir sobre ello. 🤝💬🔎
