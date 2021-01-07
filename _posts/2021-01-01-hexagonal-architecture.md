---
layout: post
title: "Arquitectura Hexagonal"
tags: [Arquitecturas, Architecture, Clean Code, Código Limpio]
---


El primer concepto clave de lo que creemos que es una arquitectura limpia a nuestro criterio, es el concepto de una capa. Una capa en sí misma no es nada, si lo piensas.
Simplemente está determinado por cómo se usa, por lo que vamos a dejar cierto resumenes de lo que significa cada capa en nuestra arquitectura hexagonal planteada.

<p align="center">
  <img width="50%" src="https://gblobscdn.gitbook.com/assets%2F-MAffO8xa1ZWmgZvfeK2%2F-MBm5SHAQNSPhXT1tUUk%2F-MBmGI3OreGzXugbMmtM%2Fimage.png?alt=media&token=58e3d2c8-549c-4e14-a288-07eb9f008e8d">
</p>

### Capa 1 (núcleo): Dominio
La capa de dominio contiene clases que son nombradas como patrones tácticos de DDD familiares:

- Entities
- Value objects
- Domain events
- Repositories (Contracts, NOT IMPLEMENTATIONS)
- Domain services
- Factories
- ...

El código del modelo de dominio es etéreo, se busca que sea lo más puro posible. No tiene puntos de contacto con el mundo real. Y si no fuera por las pruebas, nadie llamaría a este código todavía (sucede en las capas superiores). Las pruebas para el código del modelo de dominio pueden ser pruebas puramente unitarias, ya que todo lo que hacen es ejecutar código en memoria. No es necesario que el código del modelo de dominio llegue al mundo exterior (como acercarse al sistema de archivos, hacer una llamada de red, generar un número aleatorio u obtener la hora actual). Esto hace que sus pruebas sean muy estables y rápidas.

### Capa 2 (Envoltura del dominio): Aplicación

La capa de aplicación contiene clases llamadas commands y command handlers (Application Services). Un comando representa algo que debe hacerse, es decir, un caso de uso del usuario.
Es un objeto de transferencia de datos (DTO), que contiene solo valores de tipo primitivos, listas u otros DTOs en caso de necesitar algunas validaciones extras.
Siempre hay un command handler / application service que sabe cómo procesar un comando en particular. Por lo general, el command handler (que también se conoce como un servicio de aplicación) realiza cualquier orquestación necesaria para resolver el caso de uso en cuestión. Utiliza los datos del comando para crear un agregado (o entidad), o buscar uno del repositorio, y realizar alguna acción sobre él. Generalmente persiste el agregado con sus cambios de estados generados en el caso de uso.

El código en esta capa podría probarse de forma unitaria, pero tener una capa de aplicación también es un buen punto de partida para escribir pruebas de aceptación, como los escenarios de Gherkin.

### Capa 3 (Implementaciones reales de la Aplicación): Infraestructura

Nuevamente, si no fuera por las pruebas, el código en la capa de aplicación no sería ejecutado por nadie. Solo cuando se agrega la capa de infraestructura, la aplicación se volverá realmente utilizable.

La capa de infraestructura contiene cualquier código necesario para exponer los casos de uso al mundo y hacer que la aplicación se comunique con usuarios reales y servicios externos.
Piense en cualquier cosa que le brinde a su modelo de dominio y a sus servicios de aplicaciones "manos y pies" y que haga que los casos de uso de su aplicación sean "utilizables". 

Esta capa contiene el código para:

 - Procesar HTTP requests
 - Hacer (HTTP) requests a otros servidores
 - Guardado de cosas en la base de datos
 - Publicación de mensajes a un sistema como RabbitMQ
 - Enviado de Emails
 - Etc

Este tipo de código requiere pruebas de integración. En este punto prueba todas las "cosas reales": la base de datos, el código del proveedor, los servicios externos reales involucrados, etc. Esto le permite verificar todas las suposiciones que hace su código de infraestructura sobre cosas que están fuera de su control.
