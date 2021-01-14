---
layout: post
title: "Services vs Pure Objects"
tags: [Arquitecturas, Architecture, Clean Code, Código Limpio, DDD, Domain Driven Design]
---

Analizaremos diferentes tipos de objetos y las pautas para crear instancias de ellos. En términos generales, hay dos tipos de objetos y ambos
tienen reglas diferentes. Proximamente analizaremos las características de cada uno respectivamente.

# Dos tipos de objetos

En una aplicación, normalmente habrá dos tipos de objetos:

 - Objetos de servicio que realizan una tarea o devuelven un dato

 - Objetos puros que contendrán algunos datos y, opcionalmente, expondrán algún comportamiento para manipular o recuperar esos datos

Los primeros tipos de objetos se crearán una vez y luego se utilizarán tantas veces como desee, pero no se puede cambiar nada sobre ellos.
Tienen un ciclo de vida muy simple. Una vez que se han creado, pueden funcionar para siempre, como pequeñas máquinas con tareas específicas.
Estos objetos se denominan `servicios`. El segundo tipo de objetos lo utilizan los servicios para completar sus tareas. Estos objetos son los
materiales con los que trabajan los servicios. Por ejemplo, un servicio puede recuperar ese objeto de otro servicio, luego manipularía ese objeto
y lo entregaría a otro servicio para su procesamiento posterior (figura debajo). Por tanto, el ciclo de vida de un objeto puro puede ser más complicado
que el de un servicio: después de creado, se puede manipular opcionalmente, e incluso puede mantener un registro de eventos interno de todo lo que ha sucedido en él.
Los objetos de servicio son do-ers y, a menudo, tienen nombres que indican lo que hacen: controlador, renderizador, calculadora, etc. Los servicios
se pueden construir usando `new` para instanciar su clase, por ejemplo, new FileLogger().

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/104599172-34ec2700-5656-11eb-914e-8a2a123791bf.png">
</p>

Este diagrama UML muestra cómo los servicios llamarán a otros servicios, pasando tipos de objetos puros como argumentos de método o valores de retorno.
Dentro de un método de servicio, dicho objeto puede manipularse o un servicio puede recuperar datos de él.

# Service Objects

Características principales de los servicios

- Inject dependencies and configuration values as constructor arguments
- Inject what you need, not where you can get it from (Not Service Locators)
- All constructor arguments should be required
- Only use constructor injection
- There’s no such thing as an optional dependency
- Make all dependencies explicit
- Task-relevant data should be passed as method arguments instead of constructor arguments
- Don’t allow the behavior of a service to change after it has been instantiated
- Do nothing inside a constructor, only assign properties
- Throw an exception when an argument is invalid
- Define services as an immutable object graph with only a few entry points

# Pure Objects

Características principales de nuestros objectos puros

- Require the minimum amount of data needed to behave consistently
- Require data that is meaningful
- Don’t use custom exception classes for invalid argument exceptions
- Test for specific invalid argument exceptions by analyzing the exception’s message
- Extract new objects to prevent domain invariants from being verified in multiple places (Value Objects)
- Extract new objects to represent composite values (Money: Represented by Amount and Currency Value Objects)
- Use assertions to validate constructor arguments
- Don’t inject dependencies; optionally pass them as method arguments
- Use named constructors
- Don’t use property fillers (fromArray(array $data))
- Don’t put anything more into an object than it needs
- Don’t test constructors
- The exception to the rule: Data transfer objects
  - Use public properties
  - Don’t throw exceptions, collect validation errors
  - Use property fillers when needed
