---
layout: post
title: "Services vs Pure Objects"
tags: [Arquitecturas, Architecture, Clean Code, Código Limpio, DDD, Domain Driven Design]
---

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

A veces podemos sentir que una dependencia es opcional y que el servicio funciona bien sin el. Por ejemplo: Logger, solemos considerarlo como dependencia secundaria en la tarea que realiza un servicio.
Sin embargo esto innecesariamente complica el código, porque siempre necesitas un check dentro de la clase para saber si existe la instancia de esa clase opcional.
Lo mismo para los configuration values. Podemos pensar que un usuario de una clase FileLogger no necesita darle un path para ecribir los mensajes de error si existe un valor por defecto para guardarlos.
Sin embargo esto hace que cuando alguien usa la clase, no esta claro inmediatamente en que archivo se escribirá el log respectivo. Podría ser peor aún si el vaor por defecto se establece más adentro del código por ejemplo dentro de un método de la clase.
Ya que esto nos lleva a que debemos indagar en la clase hasta encontrar el valor por defecto establecido. Ahora además se convierte en un detalle de implementación que puede facilmente cambiar sin que el cliente de la clase se entere más que inspeccionando el código de la clase.
Por eso siempre debemos dejar al cliente de la clase proveer a cualquier configuration value que necesite el objeto. Si hacemos esto para todas las clases, podemos ver como un objeto es configurado solo mirando como se instancia.
En resumen, los argumentos del constructor son usados para inyectar dependencias o para proveerlos de configuration values, y recordar que estos siempre deben ser requeridos, sin valores por defecto.

- Only use constructor injection

Otro truco para dependencias opcionales es utilizar métodos setters que puede ser llamadas si el usuario decide utilizar esa dependencia. Esto permite al cliente inyectar Logger por ejemplo después de su construcción o instanciación. Esto sin embargo complica el código dentro de la clase y ademas viola dos reglas de objetos:

1. No debe ser posible crear un objeto en un estado incompleto.
2. Servicios deben ser inmutables eso es, que sea imposible de cambiar después de que fueron instanciados.

Don't use setter injection, only use constructor injection.

- There’s no such thing as an optional dependency

Hay ocasiones en las que aún con todo lo que vimos de que no es recomendable utilizar dependencia opcionales, queremos que de todas maneras una sea opcional (¿Porfiados un poco no?) para eso podemos usar un objecto dummy como implementacion al que llamamos en general patrón null object (no hace nada en general), ¿como es esto? Creamos por ejemplo una interfaz: LoggerInterface y la implementamos con un objeto vacio NullLoggerImplementation. Si el opcional no es un servicio, si no que es un configuration value, podemos utilizar el mismo approach o similar. Podemos crear un objeto con un método con valores por defecto, en lugar de agregar un argumento que sea opcional, por ejemplo: Credentials.default();

- Make all dependencies explicit

Aún con todo lo visto anteriormente pueden existir dependencias ocultas. Esto es porque no se pueden reconocer simplemente mirando de manera rápida su constructor.

### Turn static dependencies into object dependencies

ServiceRegistry.get()

Cache.get()

### Turn complicated functions into object dependencies

Generalmente estas funciones complejas son parte de la librería estandar del lenguaje por ejemplo: json_decode(), json_encode(), simple_xml_load_file(), suelen ocultar tareas complejas que resuelven y parecen simples ya que llamamos a la función determinada y nos resuelve el problema.
Podemos y deberíamos promoverlas a objectos, introduciendo alguna clase que funcione como wrapper de la función. Lo cuál nos permite agregar lógica personaliada, además de valores de argumento por defecto, o un manejo de errores personalizado.

```
final class JsonEncoder
{
    /**
    * @throws RuntimeException
    */
    public function encode(array data): string
    {
       try {
         return json_encode(
           data,
           JSON_THROW_ON_ERROR | JSON_FORCE_OBJECT
         );
       } catch (RuntimeException previous) {
         throw new RuntimeException(
            'Failed to encode data: ' . var_export(data, true),
            0,
            previous
         );
    }
    }
}
```

Introduciendo una dependencia de objeto es el primer paso para hacer el comportamiento del servicio reconfigurable sin tocar el código en cuestión.

### Make system calls explicit (DateTimes, times(), file_get_content())

```
final class MeetupRepository
{
    private Connection connection;
    
    public function __construct(Connection connection)
    {
       this.connection = connection;
    }

    public function findUpcomingMeetups(string area): array
    {
        now = new DateTime();

        return this.findMeetupsScheduledAfter(now, area);
    }

    public function findMeetupsScheduledAfter(
       DateTime time,
       string area
    ): array {
    // ...
    }
}
```

La hora actual no es algo que el servicio pueda concluir desde los argumentos del método, ni de sus dependencias, es una dependencia oculta.

Refactor:

```

interface Clock
{
   public function currentTime(): DateTime
}

final class SystemClock implements Clock
{
   public function currentTime(): DateTime
   {
      return new DateTime();
   }
}

final class MeetupRepository
{
     // ...
    private Clock clock;
    
    public function __construct(
       Clock clock,
       /* ... */
    ) {
       this.clock = clock;
    }

    public function findUpcomingMeetups(string area): array
    {
        return this.findMeetupsScheduledAfter(this.clock.currentTime(), area);
    }

    public function findMeetupsScheduledAfter(
       DateTime time,
       string area
    ): array {
    // ...
    }
}

meetupRepository = new MeetupRepository(new SystemClock());
meetupRepository.findUpcomingMeetups('NL');

```

Al hacer una interfaz cuando queremos realizar tests podemos utilizar un `fixed-time` que esta completamente bajo nuestro control. Lo convertirá en un caso deterministico, que es justo lo que necesitamos para nuestra suite de tests.

Otra manera podría ser pasar el tiempo como argumento del método, lo cual lo convierte en `contextual information` que se necesita para realizar la tarea determinada.


- Task-relevant data should be passed as method arguments instead of constructor arguments

Como sabe, un servicio debe obtener todas sus dependencias y valores de configuración inyectados como argumentos de constructor. Pero la información sobre la tarea en sí, incluida cualquier información contextual relevante, debe proporcionarse como argumentos del método.
Como contraejemplo, considere un EntityManager que solo se puede usar para guardar una entidad en la base de datos


```
final class EntityManager
{
   private object entity;
   
   public function __construct(object entity)
   {
      this.entity = entity;
   }
   
   public function save(): void
   {
      // ...
   }
}

user = new User(/* ... */);
comment = new Comment(/* ... */);

entityManager = new EntityManager(user);
entityManager.save();

entityManager = new EntityManager(comment);
entityManager.save();
```

Esta no sería una clase muy útil, porque tendría que crear una instancia nueva para cada trabajo que tenga.
Tener una entidad como argumento de constructor puede parecer una mala elección de diseño obviamente. Un escenario más sutil y común sería un servicio que obtiene la Request actual o el objeto Session actual inyectado como un argumento de constructor.


```
final class ContactRepository
{
   private Session session;
   
   public function __construct(Session session)
   {
      this.session = session;
   }
   
   public function getAllContacts(): array
   {
      return this.select()
        .where([
        'userId' => this.session.getUserId(),
        'companyId' => this.session.get('companyId')
      ])
      .getResult();
   }
}
```

Este servicio de ContactRepository no se puede utilizar para obtener los contactos de un usuario o empresa diferente al conocido por el objeto de sesión actual. Es decir, solo se puede ejecutar en un contexto.
La inyección de parte de los detalles del trabajo como argumentos del constructor impide que el servicio sea reutilizable, y lo mismo ocurre con los datos contextuales. Toda esta información debe pasarse como argumentos de método, para que el servicio sea reutilizable para diferentes trabajos.
Una pregunta de orientación para ayudarlo a decidir si algo debe pasarse como un argumento de constructor o como un argumento de método es: "¿Podría ejecutar este servicio en un lote, sin tener que crear una instancia una y otra vez?" Dependiendo de su lenguaje de programación, es posible que ya esté acostumbrado a la idea de que su servicio será instanciado una vez y debería estar preparado para su reutilización. Sin embargo, si usa PHP, cualquier
objeto del que se crea una instancia suele durar solo el tiempo que sea necesario para procesar una solicitud HTTP y devolver una respuesta. En ese caso, al diseñar sus servicios, siempre debe preguntarse: "Si la memoria no se borró después de cada solicitud web, ¿podría usarse este servicio para solicitudes posteriores o tendría que reinstalarse?"
Eche otro vistazo al servicio EntityManager que vimos anteriormente. Sería imposible guardar varias entidades en un lote sin crear una instancia del servicio nuevamente, por lo que la entidad debería convertirse en un parámetro del método save(), en lugar de ser un argumento de constructor.


```
final class EntityManager
{
   public function save(object entity): void
   {
      // ...
   }
}
```

Lo mismo ocurre con ContactRepository. No se puede usar en un lote para obtener los contactos de diferentes usuarios y diferentes empresas. getAllContacts () debería tener argumentos adicionales para la empresa actual y el ID de usuario, como se indica a continuación.

```
final class ContactRepository
{
   public function getAllContacts(
      UserId userId,
      CompanyId companyId
   ): array {
      return this.select()
      .where([
         'userId' => userId,
         'companyId' => companyId
      ])
      .getResult();
   }
}
```

De hecho, la palabra "actual" es una señal útil de que esta información es información contextual que debe pasarse como argumentos del método: "la hora actual", "el ID de usuario actualmente conectado", "la solicitud web actual", etc. .

- Don’t allow the behavior of a service to change after it has been instantiated

Como vimos anteriormente, cuando inyecta dependencias opcionales en un servicio después de la instanciación, cambiará el comportamiento de un servicio. Esto hace que el servicio sea impredecible. Lo mismo ocurre con los métodos que no inyectan dependencias pero que le permiten influir en el comportamiento del servicio desde fuera. Un ejemplo sería el método ignoreErrors() de la clase Importer:

```
final class Importer
{
   private bool ignoreErrors = true;
   
   public function ignoreErrors(bool ignoreErrors): void
   {
      this.ignoreErrors = ignoreErrors;
   }
   // ...
}

importer = new Importer();
importer.ignoreErrors(false);
```

Asegúrese de que esto no pueda suceder. Todas las dependencias y los valores de configuración deben estar ahí desde el principio, y no debería ser posible volver a configurar el servicio después de que se haya creado una instancia.
Otro ejemplo es un EventDispatcher en el siguiente ejemplo. Permite reconfigurar la lista de listerers activos después de crear una instancia.

```
final class EventDispatcher
{
    private array listeners = [];
    
    public function addListener(
       string event,
       callable listener
    ): void {
       this.listeners[event][] = listener;
    }
    
    public function removeListener(
       string event,
       callable listener
    ): void {
       foreach (this.listenersFor(event) as key => callable) {
         if (callable == listener) {
            unset(this.listeners[event][key]);
         }
       }
    }
    
    public function dispatch(object event): void
    {
       foreach (this.listenersFor(event.className) as callable) {
          callable(event);
       }
    }
    
    private function listenersFor(string event): array
    {
        if (isset(this.listeners[event])) {
           return this.listeners[event];
        }
        
        return [];
    }
}

```

Permitir que los detectores de eventos se agreguen y eliminen sobre la marcha hace que el comportamiento de EventDispatcher sea impredecible porque puede cambiar con el tiempo. En este caso, deberíamos convertir la matriz de detectores de eventos en un argumento de constructor y eliminar los métodos addListener() y removeListener(), como se hace de la siguiente manera:

```
final class EventDispatcher
{
   private array listeners;
   
   public function __construct(array listenersByEventName)
   {
     this.listeners = listenersByEventName;
   }
   // ...
}
```

Debido a que la matriz no es un tipo muy específico y podría contener cualquier cosa (si usa un lenguaje de programación escrito dinámicamente), debe validar el argumento listenersByEventName antes de asignarlo.

Si no permite que se modifique un servicio después de la creación de instancia, y tampoco permite que tenga dependencias opcionales, el servicio resultante se comportará de manera predecible con el tiempo y no comenzará repentinamente a seguir diferentes rutas de ejecución en función de quién a llamado un método en él (ver imagen debajo).

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/106136738-77c6f800-6148-11eb-9f5d-27f8f4f2e70e.png">
</p>


"En el tipo de aplicaciones que creo, en realidad necesito servicios mutables".

Las aplicaciones web realmente no necesitan servicios mutables; el conjunto completo de comportamientos de un servicio siempre se puede definir en el momento de la construcción.
Es posible que esté trabajando en otros tipos de aplicaciones, donde necesita un servicio como un despachador de eventos que le permite agregar y eliminar oyentes o suscriptores después del tiempo de construcción. Por ejemplo, si está creando un juego o algún otro tipo de aplicación interactiva con una interfaz de usuario y un usuario abre una nueva ventana, querrá registrarse detectores de eventos para sus elementos de interfaz de usuario. Más tarde, cuando el usuario cierre la ventana, querrá eliminar esos oyentes nuevamente. En esos casos, los servicios realmente deben ser mutables. Sin embargo, si está diseñando tales servicios mutables, le animo a pensar en formas de no permitir que los objetos reconfiguren el comportamiento de otros objetos usando métodos públicos como addListener() y removeListener().

- Do nothing inside a constructor, only assign properties
Crear un servicio significa inyectar argumentos de constructor, preparando así el servicio para su uso. El trabajo real se realizará dentro de uno de los métodos del objeto. Dentro de un constructor, a veces puede tener la tentación de hacer más que simplemente asignar propiedades, para que el objeto esté realmente listo para su uso. Tomemos, por ejemplo, la siguiente clase FileLogger.
Tras la construcción, preparará el archivo de registro para su escritura.

```
final class FileLogger implements Logger
{
   private string logFilePath;
   
   public function __construct(string logFilePath){
      logFileDirectory = dirname(logFilePath);
      
      if (!is_dir(logFileDirectory)) {
         mkdir(logFileDirectory, 0777, true);
      }
   
      touch(logFilePath);
      this.logFilePath = logFilePath;
   }
   // ...
}

```

Pero crear una instancia de FileLogger dejará un rastro en el sistema de archivos, incluso si nunca usa el objeto para escribir un mensaje de registro.
Se considera de buena educación no hacer nada dentro de un constructor. Lo único que debe hacer en un constructor de servicios es validar los argumentos del constructor proporcionados y luego asignarlos a las propiedades del objeto.

```
final class FileLogger implements Logger
{
    private string logFilePath;
    
    public function __construct(string logFilePath)
    {
       this.logFilePath = logFilePath;
    }
    
    public function log(string message): void
    {
       this.ensureLogFileExists();
       // ...
    }
    
    private function ensureLogFileExists(): void
    {
        if (is_file(this.logFilePath)) {
           return;
        }
        
        logFileDirectory = dirname(this.logFilePath);
        
        if (!is_dir(logFileDirectory)) {
            mkdir(logFileDirectory, 0777, true);
        }
        
        touch(this.logFilePath);
    }
}
```

Empujar el trabajo fuera del constructor, más profundamente en la clase, es una posible solución. En este caso, sin embargo, solo averiguaremos si es posible escribir en el archivo de registro cuando se escribe el primer mensaje en él. Lo más probable es que deseemos conocer estos problemas antes. En cambio, lo que podríamos hacer es empujar el trabajo fuera del constructor: no queremos que suceda después de construir el FileLogger, sino antes. Quizás una LoggerFactory podría encargarse de eso.

```
final class FileLogger implements Logger
{
    private string logFilePath;

    public function __construct(string logFilePath)
    {
       if (!is_writable(logFilePath)) {
          throw new InvalidArgumentException(
             'Log file path "{logFilePath}" should be writable'
          );
       }
    
      this.logFilePath = logFilePath;
    }
    
    public function log(string message): void
    {
       // ...
    }
}


final class LoggerFactory
{
    public function createFileLogger(string logFilePath): FileLogger
    {
        if (!is_file(logFilePath)) {
           logFileDirectory = dirname(logFilePath);
        
           if (!is_dir(logFileDirectory)) {
              mkdir(logFileDirectory, 0777, true);
           }

           touch(logFilePath);
        }
        
        return new FileLogger(logFilePath);
    }
}
```

Tenga en cuenta que mover el código de configuración del archivo de registro fuera del constructor de FileLogger cambia el contrato del propio FileLogger. En la situación inicial, podría pasar cualquier ruta de archivo de registro, y FileLogger se encargaría de todo (creando el directorio si es necesario y verificando que la ruta del archivo en sí sea modificable). En la nueva situación, FileLogger acepta una ruta de archivo de registro y espera que el directorio que lo contiene ya exista. Podemos impulsar aún más a la fase de arranque de la aplicación y reescribir el contrato de FileLogger para indicar que el cliente debe proporcionar una ruta de archivo a un archivo que ya existe y se puede escribir. El siguiente snipped muestra lo que
se parecería:
```
final class FileLogger implements Logger
{
    private string logFilePath;

    public function __construct(string logFilePath)
    {    
      this.logFilePath = logFilePath;
    }
    
    public function log(string message): void
    {
       // ...
    }
}

final class LoggerFactory
{
    public function createFileLogger(string logFilePath): FileLogger
    {
        if (!is_file(logFilePath)) {
           logFileDirectory = dirname(logFilePath);
        
           if (!is_dir(logFileDirectory)) {
              mkdir(logFileDirectory, 0777, true);
           }
    
           touch(logFilePath);
        }
        
        if (!is_writable(logFilePath)) {
          throw new InvalidArgumentException(
             'Log file path "{logFilePath}" should be writable'
          );
        }
           
        return new FileLogger(logFilePath);
    }
}
```

Echemos un vistazo a otro ejemplo más sutil de un objeto que hace algo en su constructor. Eche un vistazo a la siguiente clase Mailer, que llama a una de sus dependencias dentro del constructor.

```
final class Mailer
{
    private Translator translator;
    private string defaultSubject;
    
    public function __construct(Translator translator)
    {
        this.translator = translator;
        // ...
        this.defaultSubject = this.translator.translate('default_subject');
    }
    // ...
}
```
 
 ¿Qué sucede si cambiamos el orden de las asignaciones?

```
final class Mailer
{
    private Translator translator;
    private string defaultSubject;
    
    public function __construct(Translator translator)
    {
        // ...
        this.defaultSubject = this.translator.translate('default_subject');
        this.translator = translator;
    }
    // ...
}
```

Ahora obtendrá un error fatal al llamar a translate () en null. Esta es la razón por la que la regla de que solo puede asignar propiedades en los constructores de servicios tiene la consecuencia de que las asignaciones pueden ocurrir en cualquier orden. Si las asignaciones tienen que suceder en un orden específico, sabe que está haciendo algo en su constructor.
El constructor de esta clase Mailer también es un ejemplo de cómo los datos contextuales, es decir, la configuración regional del usuario actual, a veces se pasan como un argumento de constructor. Como sabe, la información contextual debe pasarse como un argumento de método.

- Throw an exception when an argument is invalid
Cuando un cliente de una clase proporciona un argumento de constructor no válido, el verificador de tipo normalmente le advertirá, como cuando el argumento requiere una instancia de Logger y el cliente proporciona un valor bool. Sin embargo, hay otros tipos de argumentos en los que basarse únicamente en el sistema de tipos será insuficiente. Por ejemplo, en la siguiente clase Alerting, uno de los argumentos del constructor debe ser un int, que representa una bandera de configuración.

```
final class Alerting
{
    private int minimumLevel;
    
    public function __construct(int minimumLevel)
    {
       this.minimumLevel = minimumLevel;
    }
}
alerting = new Alerting(-99999999);
```

Al aceptar cualquier int para minimumLevel, no puede estar seguro de que el valor proporcionado sea realista y pueda ser utilizado por el código restante de una manera significativa. En su lugar, el constructor debe verificar que el valor sea válido y, si no lo es, lanzar una excepción. Solo después de que el argumento haya sido validado, debe asignarse de la siguiente manera.


```
final class Alerting
{
    private int minimumLevel;
    
    public function __construct(int minimumLevel)
    {
       if (minimumLevel <= 0) {
          throw new InvalidArgumentException(
             'Minimum alerting level should be greater than 0'
          );
       }
    
       this.minimumLevel = minimumLevel;
    }
}

alerting = new Alerting(-99999999);
```

Al lanzar una excepción dentro del constructor, puede evitar que el objeto se construya basándose en argumentos no válidos.
En lugar de lanzar excepciones personalizadas, es bastante común usar funciones de aserción reutilizables para validar métodos y argumentos de constructores.

NOTA
La elección de no lanzar una excepción también podría ser una opción, si eso no rompe el comportamiento del objeto en una etapa posterior. Considere la siguiente clase de enrutador.

```
final class Router
{
     private array controllers;
     private string notFoundController;
     
     public function __construct(
        array controllers,
        string notFoundController
     ) {
        this.controllers = controllers;
        this.notFoundController = notFoundController;
     }
     
     public function match(string uri): string
     {
         foreach (this.controllers as pattern => controller) {
            if (this.matches(uri, pattern)) {
               return controller;
            }
         }
         
         return this.notFoundController;
     }
     
     private function matches(string uri, string pattern): bool
     {
        // ...
     }
}

router = new Router(
   [
      '/' => 'homepage_controller'
   ],
   'not-found'
);

router.match('/');
```

¿Debería validar el argumento de los controladores aquí para verificar que contiene al menos un par de nombre de patrón / controlador de URI? En realidad, no es necesario, porque el comportamiento del enrutador no se interrumpirá si la matriz de controladores está vacía. Si acepta una matriz vacía y el cliente llama a match (), solo devolverá el controlador "no encontrado", porque no se han encontrado patrones coincidentes para el URI dado (ni ningún otro URI). Este es el comportamiento que esperaría de un enrutador, por lo que no debe considerarse un signo de lógica rota.
Sin embargo, debe validar que todas las claves y valores de la matriz de controladores sean cadenas. Esto le ayudará a identificar los errores de programación desde el principio. Considere el siguiente ejemplo:

```

final class Router
{
    // ...
    public function __construct(array controllers)
    {
        foreach (array_keys(controllers) as pattern) {
           if (!is_string(pattern)) {
              throw new InvalidArgumentException(
                 'All URI patterns should be provided as strings'
              );
           }
        }
        
        foreach (controllers as controller) {
           if (!is_string(controller)) {
              throw new InvalidArgumentException(
                 'All controllers should be provided as strings'
              );
           }
        }
        
        this.controllers = controllers;
    }
    // ...
}

```

Alternativamente, puede usar una biblioteca de aserciones o funciones de aserciones personalizadas para validar el contenido de los controladores o usar el sistema de tipos para verificar los tipos por usted, como en el siguiente ejemplo.
Debido a que el método addController() tiene tipos de cadena explícitos para sus argumentos, llamar a este método en cada par clave / valor en la matriz de controladores proporcionada será el equivalente a afirmar que todas las claves y valores en la matriz son cadenas.

```
final class Router
{
    private array controllers = [];
    
    public function __construct(array controllers)
    {
       foreach (controllers as pattern => controller) {
          this.addController(pattern, controller);
       }
    }
    
    private function addController(
       string pattern,
       string controller
    ): void {
       this.controllers[pattern] = controller;
    }
    // ...
}
```


- Define services as an immutable object graph with only a few entry points

Una vez que el framework de la aplicación llama a su controlador (ya sea un controlador web o un controlador para una aplicación de línea de comandos), puede considerar que se conocen todas las dependencias. Por ejemplo, el controlador web necesita un repositorio del que obtener algunos objetos, necesita el motor de plantillas para representar una plantilla, necesita un factory de respuestas para crear un objeto Response, etc. Todas estas dependencias tienen sus propias dependencias, que, cuando se tienen cuidado enumerados como argumentos de constructor, se pueden crear a la vez, lo que a menudo da como resultado un gráfico de objetos bastante grande.
Si el framework decide llamar a un controlador diferente, utilizará un gráfico diferente de objetos dependientes para realizar su tarea. El controlador en sí también es un servicio con dependencias, por lo que puede considerar que los controladores son los puntos de entrada del gráfico de objetos de la aplicación, como se muestra en la figura más abajo.
La mayoría de las aplicaciones tienen algo así como un service container que describe cómo se pueden construir todos los servicios de la aplicación, cuáles son sus dependencias, cómo se pueden construir, etc. El contenedor también se comporta como un service locator. Usted puede
pedirle que te devuelva uno de sus servicios para que puedas usarlo.

Dado lo siguiente,
 Todos los servicios de una aplicación forman un gran gráfico de objetos.
 Los puntos de entrada serán los controladores.
 Ningún servicio necesitará el service locator para recuperar servicios.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/106193210-fb560880-618b-11eb-9573-90f8926b111e.png">
</p>

The graph contains all the services of an application, with controller services
marked as entry point services. These are the only services that can be retrieved directly;
all other services are only available as injected dependencies

Deberíamos concluir que el service container solo necesita proporcionar métodos públicos para recuperar controladores. Los otros servicios definidos en el contenedor pueden y deben permanecer privados, porque solo serán necesarios como dependencias inyectadas para los controladores.
Traducido a código, esto significa que podríamos usar un service container como un service locator para recuperar un controlador. Toda la otra lógica de creación de instancias de servicios que se necesita para producir los objetos del controlador puede permanecer detrás de escena, en métodos privados.


```
final class ServiceContainer
{
     public function homepageController(): HomepageController
     {
        return new HomepageController(
           this.userRepository(),
           this.responseFactory(),
           this.templateRenderer()
        );
     }
     
     private function userRepository(): UserRepository
     {
        //...
     }
     
     private function responseFactory(): ResponseFactory
     {
       //...
     }
     
     private function templateRenderer(): TemplateRenderer
     {
        // ...
     }
 }


if (uri == '/') {
   controller = serviceContainer.homepageController();
   response = controller.execute(request);
   // ...
} elseif (/* ... */) {
  // ...
}
```

Un service container permite la reutilización de servicios, por lo que, comenzando con el controlador como punto de entrada, no todas las ramas del gráfico de objetos serán completamente independientes.
Por ejemplo, otro controlador puede usar la misma instancia de TemplateRenderer que HomepageController (ver imagen debajo). Por eso es importante hacer que los servicios se comporten de la manera más predecible posible. Si aplica todas las reglas discutidas anteriormente, terminará con un gráfico de objetos que se puede instanciar una vez y luego reutilizar muchas veces.


<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/106193885-ddd56e80-618c-11eb-9b4c-0102b1052f06.png">
</p>

Different entry points use different branches of the object graph.
