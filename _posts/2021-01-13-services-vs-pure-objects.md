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

Un servicio usualmente utiliza otros servicios para hacer su trabajo. Estos otros servicios son llamados dependencias y deben ser inyectados como argumentos de constructor.
Esto nos asegura que el servicio este listo para usar luego de la instanciación del mismo. Así no requerira una configuración especial ni podemos olvidarnos accidentalmente de proveer la dependencia necesaria para que funcione.
A veces necesitamos algunos valores de configuración (configuration values), como por ejemplo el path adecuado para guardar archivos, o credenciales para conectarse a un servicio externo. Inyectar valores de configuración como argumentos de constructor al igual que las dependencias. Debemos asegurarnos de solo inyectar los valores que el servicio necesita y no enviar un objeto general como por ejemplo una clase: AppConfig (En el caso de Laravel: esta dependencia nos permite traer cualquier configuración necesaria de la aplicación)

Keeping together configuration values that belong together

No se debe inyectar un objeto de configuración global, solo los valores que necesite el servicio. Sin embargo, algunos de estos valores es posible que deban ir juntos, si los separamos partiriaos su cohesión natural, un caso de esto es por ejemplo: Las Credenciales para conectarse a una API. Podríamos pasarle el parametro APIClient::connect(string username, string password) pero tendría más cohesión de la siguiente manera: APIClient::connect(Credentials credentials) siendo el objeto Credentials un DTO (Data Transfer Object)

- Inject what you need, not where you can get it from (Not Service Locators)

Los frameworks suelen ofrecernos objectos especiales que tienen cada servicio disponible y todos los valores de configuración que pudieramos querer utilizar a nivel de nuestra aplicación. Estos suelen llamarser ServiceLocator, Manager, Registry o Container.

1. Para traer nuestros Servicios en Laravel: App::make(CreateUserService::class);

2. Para traer nuestros valores de configuración en Laravel: Config::get('app.env');

¿Qué es un service locator?

Es un servicio que nos permite traer otros servicios, basicamente tiene un mapping de todos los servicios con su nombre disponible para que podamos traerlo en el momento que deseamos.

Un service locator generalmente sabe como instanciar todos los servicios de la aplicación, y tiene especial cuidado dandole a los servicios los argumentos adecuados para su correcta construcción. Además suelen reutilizar los servicios ya instanciados, lo cual nos permite mejorar la performance de la aplicación.

Cuando utilizamos un Service Locator en lugar de dependencias especificas, nos suele oscurecer el código, y puede llevar a que generemos funcionalidades extras dentro de nuestro servicio invocado ya que por tener acceso a todo nos da la tentación de recurrir al llamado de otras tareas dentro del mismo servicio, además esto genera por ejemplo que en un Controller / Action tener conocimiento de como traer y generar X dependencia. En general nos lleva a que traemos clases que poco tienen que ver con nuestra tarea inicial, agregando responsabilidades que no tenian que estar ahí y no empuja al programador a ver una alternativa de diseño buena, ya que por vagancia buscamos siempre el camino facil y en lugar de separar responsabilidades empezamos a crear clases del tipo UserManager / UserService, donde cualquier cosa que se relacione con este concepto de usuario lo metemos en la misma bolsa.

Cuando un servicio necesita de otro para realizar su tarea, debe ser declarado como argumento del constructor, dejando claro que es una dependencia del servicio en cuestión.

```
HomeController -> Service Locator (Tenemos 1 dependencia en teoría con este camino, aunque no sea del todo cierto)

HomeController -> (Cuando declaramos realmente las que necesita nos da como resultado 3 dependencia)
  EntityManager
  TemplateRenderer
  ResponseFactory
```

Aún con el segundo caso en realidad el EntityManager lo utilizamos para traer el UserRepository. Por lo tanto lo más honestos sería que quede de la siguiente manera:

```
HomeController ->
  UserRepository
  TemplateRenderer
  ResponseFactory
```

### ¿Como podemos saber si inyectamos una dependencia correcta u honesta?

Si el servicio no trae una dependencia de él mismo, pero usa directamente el servicio.

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
