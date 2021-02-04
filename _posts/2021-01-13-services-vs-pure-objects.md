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

# Pure Objects

Mencionamos anteriormente que hay dos tipos de objetos: servicios y otros objetos.
El segundo tipo de objetos se puede dividir en subtipos más específicos, a saber, objetos de valor y entidades (a veces conocidos como "modelos"). Los servicios crearán o recuperarán entidades, las manipularán o las pasarán a otros servicios. También crearán objetos de valor y los pasarán como argumentos de método, o crearán copias modificadas de ellos. En este sentido, las entidades y los objetos de valor son los materiales que utilizan los servicios para realizar sus tareas.

Características principales de nuestros objectos puros

- Require the minimum amount of data needed to behave consistently

```
final class Position
{
    private int x;
    private int y;
    
    public function __construct()
    {
       // empty
    }
    
    public function setX(int x): void
    {
       this.x = x;
    }
    
    public function setY(int y): void
    {
       this.y = y;
    }
    
    public function distanceTo(Position other): float
    {
       return sqrt(
          (other.x - this.x) ** 2 +
          (other.y - this.y) ** 2
       );
    }
}

position = new Position();
position.setX(45);
position.setY(60);
```

Hasta que hayamos llamado setX () y setY (), el objeto está en un estado inconsistente. Podemos notar esto si llamamos a distanceTo () antes de llamar a setX () o setY (); no dará una respuesta significativa.
Dado que es crucial para el concepto de una posición que tenga partes x e y, tenemos que hacer cumplir esto haciendo que sea imposible crear un objeto Posición sin proporcionar valores tanto para x como para y.

```
final class Position
{
    private int x;
    private int y;
    
    public function __construct(int x, int y)
    {
       this.x = x;
       this.y = y;
    }
    
    public function distanceTo(Position other): float
    {
       return sqrt(
          (other.x - this.x) ** 2 +
          (other.y - this.y) ** 2
       );
    }
}

position = new Position(45, 60);
```

Este es un ejemplo de cómo se puede usar un constructor para proteger un invariante de dominio, que es algo que siempre es cierto para un objeto dado, según el conocimiento de dominio que tiene sobre el concepto que representa. El invariante de dominio que se protege aquí es: "Una posición tiene una coordenada x e y".

- Require data that is meaningful

En el ejemplo anterior, el constructor aceptaría cualquier número entero, positivo o negativo y hasta el infinito en ambas direcciones. Ahora considere otro sistema de coordenadas, donde las posiciones consisten en una latitud y una longitud, que juntas determinan un lugar en la tierra. En este caso, no todos los valores posibles de latitud y longitud ser considerado significativo.

```
final class Coordinates
{
   private float latitude;
   private float longitude;
   
   public function __construct(float latitude, float longitude)
   {
      this.latitude = latitude;
      this.longitude = longitude;
   }
   
   // ...
}

meaningfulCoordinates = new Coordinates(45.0, -60.0);
offThePlanet = new Coordinates(1000.0, -20000.0);
```

Asegúrese siempre de que los clientes no puedan proporcionar datos que no tengan sentido. Lo que cuenta como sin sentido puede expresarse como invariante de dominio también. En este caso, el invariante es, “La latitud de una coordenada es un valor entre –90 y 90 inclusive. La longitud de una coordenada es un valor entre –180 y 180 inclusive ".
Cuando diseñe sus objetos, déjese guiar por estos invariantes de dominio. Reúna más invariantes a medida que avanza e incorpórelos en sus pruebas unitarias. Como ejemplo, la siguiente lista usa la utilidad expectException():

```
expectException(
   InvalidArgumentException.className,
   'Latitude',
   function() {
   new Coordinates(90.1, 0.0);
   }
);

expectException(
   InvalidArgumentException.className,
   'Longitude',
   function() {
   new Coordinates(0.0, 180.1);
   }
);

// and so on...
```

Para que estas pruebas pasen, lanza una excepción en el constructor tan pronto como algo sobre los argumentos proporcionados parezca incorrecto.

```
final class Coordinates
{
   // ...
   public function __construct(float latitude, float longitude)
   {
      if (latitude > 90 || latitude < -90) {
        throw new InvalidArgumentException(
          'Latitude should be between -90 and 90'
        );
      }
      
      this.latitude = latitude;
      
      if (longitude > 180 || longitude < -180) {
        throw new InvalidArgumentException(
          'Longitude should be between -180 and 180'
        );
      }
      
      this.longitude = longitude;
   }
}
```

Aunque el orden exacto de las declaraciones en su constructor no debería importar (como discutimos anteriormente), aún se recomienda que realice las verificaciones directamente sobre sus asignaciones de propiedades asociadas. Esto facilitará que el lector comprenda cómo se relacionan las dos declaraciones.
En algunos casos, no es suficiente verificar que cada argumento del constructor sea válido por sí solo. A veces, es posible que deba verificar que los argumentos del constructor proporcionados sean significativos juntos. El siguiente ejemplo muestra la clase ReservationRequest, que se utiliza para mantener cierta información sobre una reserva de hotel.

```
final class ReservationRequest
{
    public function __construct(
       int numberOfRooms,
       int numberOfAdults,
       int numberOfChildren
    ) {
       // ...
    }
}
```

Al discutir las reglas comerciales para este objeto con un experto en el dominio, puede aprender sobre las siguientes reglas:

 Siempre debe haber al menos un adulto (porque los niños no pueden reservar una habitación de hotel por su cuenta).
 Todo el mundo puede tener su propia habitación, pero no se pueden reservar más habitaciones que huéspedes. (No tendría sentido permitir que las personas reserven habitaciones donde nadie duerma).

Entonces, resulta que numberOfRooms y numberOfAdults están relacionados y solo pueden considerarse significativos juntos. Tenemos que asegurarnos de que el constructor tome ambos valores y aplique las reglas comerciales correspondientes, como se muestra en el siguiente snippet:

```
final class ReservationRequest
{
   public function __construct(
     int numberOfRooms,
     int numberOfAdults,
     int numberOfChildren
   ) {
      if (numberOfRooms > numberOfAdults + numberOfChildren) {
         throw new InvalidArgumentException(
           'Number of rooms should not exceed number of guests'
         );
      }
      
      if (numberOfAdults < 1) {
         throw new InvalidArgumentException(
           'numberOfAdults should be at least 1'
         );
      }
      
      if (numberOfChildren < 0) {
         throw new InvalidArgumentException(
           'numberOfChildren should be at least 0'
         );
      }
   }
}
```

En otros casos, los argumentos del constructor pueden parecer a primera vista estar relacionados, pero un rediseño podría ayudarlo a evitar validaciones de múltiples argumentos. Considere la siguiente clase, que representa un trato comercial entre dos partes, donde hay una cantidad total de dinero que debe dividirse entre dos partes.

```
final class Deal
{
   public function __construct(
      int totalAmount,
      int amountToFirstParty,
      int amountToSecondParty
   ) {
      // ...
   }
}
```

Debe al menos validar los argumentos del constructor por separado (la cantidad total debe ser mayor que 0, etc.). Pero también hay una invariante que abarca todos los argumentos: la suma de lo que obtienen ambas partes debe ser igual a la cantidad total. El siguiente snippet muestra cómo puede verificar esta regla.

```
final class Deal
{
   public function __construct(
      int totalAmount,
      int amountToFirstParty,
      int amountToSecondParty
   ) {
      // ...
      if (amountToFirstParty + amountToSecondParty != totalAmount) {
         throw new InvalidArgumentException(/* ... */);
      }
   }
}
```

Como habrá notado, esta regla podría aplicarse de una manera mucho más simple. Se podría decir que ni siquiera es necesario proporcionar el monto total en sí, siempre que el cliente proporcione números positivos para amountToFirstParty y amountToSecondParty. El objeto Deal podría calcular por sí solo cuál era el monto total del trato sumando estos valores. La necesidad de validar los argumentos del constructor juntos desaparece.

```
final class Deal
{
    private int amountToFirstParty;
    private int amountToSecondParty;
    
    public function __construct(
       int amountToFirstParty,
       int amountToSecondParty
    ) {
       if (amountToFirstParty <= 0) {
         throw new InvalidArgumentException(/* ... */);
       }
       
       this.amountToFirstParty = amountToFirstParty;
       
       if (amountToSecondParty <= 0) {
          throw new InvalidArgumentException(/* ... */);
       }
       
       this.amountToSecondParty = amountToSecondParty;
    }
    
    public function totalAmount(): int
    {
       return this.amountToFirstPart + this.amountToSecondParty;
    }
}
```

Otro ejemplo en el que parecería que los argumentos del constructor deben validarse juntos es la siguiente clase, que representa una línea.

```
final class Line
{
    public function __construct(
       bool isDotted,
       int distanceBetweenDots
    ) {
    
       if (isDotted && distanceBetweenDots <= 0) {
          throw new InvalidArgumentException(
             'Expect the distance between dots to be positive.'
          );
       }
       // ...
    }
}
```

Sin embargo, esto podría tratarse de manera más elegante proporcionando al cliente dos formas distintas de definir una línea: punteada y sólida. Se podrían construir diferentes tipos de líneas con diferentes constructores.

```
final class Line
{
    private bool isDotted;
    private int distanceBetweenDots;
    
    public static function dotted(int distanceBetweenDots): Line
    {
        if (distanceBetweenDots <= 0) {
           throw new InvalidArgumentException(
              'Expect the distance between dots to be positive.'
           );
        }
        
        line = new Line(/* ... */);
        line.distanceBetweenDots = distanceBetweenDots;
        line.isDotted = true;
        
        return line;
    }
    
    public static function solid(): Line
    {
       line = new Line();
       
       line.isDotted = false; // No necesitamos preocuparnos de la distancia entre puntos en este caso
       
       return line;
    }
}
```

Estos métodos se llaman named constructors.

Si se asegura de que cada objeto tenga los datos mínimos requeridos proporcionados en el momento de la construcción, y que estos datos sean correctos y significativos, solo encontrará objetos completos y válidos en su aplicación. Debe ser seguro asumir que puede usar todos los objetos según lo previsto. No debería haber sorpresas ni necesidad de rondas de validación adicionales.

- Don’t use custom exception classes for invalid argument exceptions

Hasta ahora, hemos estado lanzando una InvalidArgumentException genérica cada vez que un argumento de método no coincide con nuestras expectativas. Podríamos usar una clase de excepción personalizada que se extienda desde InvalidArgumentException. La ventaja de hacerlo es que podríamos detectar tipos específicos de excepciones y tratarlos de maneras específicas.

```
final class SpecificException extends InvalidArgumentException
{
}

try {
  // try to create the object
} catch (SpecificException exception) {
  // handle this specific problem in a specific way
}
```

Sin embargo, rara vez debería necesitar hacer eso con excepciones de argumentos no válidos. Un argumento no válido significa que el cliente está utilizando el objeto de forma no válida. Por lo general, esto se debe a un error de programación. En ese caso, será mejor que falles y no intentes recuperarte, sino que corrijas el error.

Para RuntimeExceptions, por otro lado, a menudo tiene sentido usar clases de excepciones personalizadas porque es posible que pueda recuperarse de ellas o convertirlas en mensajes de error fáciles de usar. Analizaremos las excepciones de tiempo de ejecución personalizadas y cómo crearlas en otra ocasión.

- Test for specific invalid argument exceptions by analyzing the exception’s message

Incluso si solo usa la clase genérica InvalidArgumentException para validar los argumentos del método, aún necesita una forma de distinguirlos en una prueba unitaria. Echemos otro vistazo a la clase y al constructor Coordenadas.

```
final class Coordinates
{
// ...
public function __construct(float latitude, float longitude)
{
if (latitude > 90 || latitude < -90) {
throw new InvalidArgumentException(
'Latitude should be between -90 and 90'
);
}
this.latitude = latitude;
if (longitude > 180 || longitude < -180) {
throw new InvalidArgumentException(
'Longitude should be between -180 and 180'
);
}
this.longitude = longitude;
}
}
```

Incluso si solo usa la clase genérica InvalidArgumentException para validar los argumentos del método, aún necesita una forma de distinguirlos en una prueba unitaria. Echemos otro vistazo a la clase y al constructor Coordenadas.

Queremos verificar que los clientes no puedan pasar los argumentos incorrectos, por lo que podemos escribir algunas pruebas, como las siguientes.

En el último caso de prueba, la InvalidArgumentException que se lanza desde el constructor no es la que esperaríamos que fuera. Debido a que el caso de prueba reutiliza un valor no válido para la latitud (–90,1) del caso de prueba anterior, intentar construir un objeto Coordenadas arrojará una excepción que nos indicará que "la latitud debe estar entre –90,0 y 90,0". Pero se suponía que la prueba verificaría que el código rechazaría valores inválidos de longitud. Esto deja al descubierto la verificación de rango para la longitud en un escenario de prueba, aunque todas las pruebas tengan éxito.
Para evitar este tipo de error, asegúrese de verificar siempre que la excepción que detecta en una prueba unitaria sea de hecho la esperada. Una forma pragmática de hacer esto es verificar que el mensaje de excepción contenga ciertas palabras predefinidas.

```
expectException(
   InvalidArgumentException.className,
   'Longitude',
   function() {
   new Coordinates(-90.1, 180.1);
   }
);
```

Agregar esta expectativa sobre el mensaje de excepción a la prueba en el snippet anterior hará que la prueba falle. Pasará de nuevo una vez que proporcionemos al constructor un valor sensible para la latitud.

- Extract new objects to prevent domain invariants from being verified in multiple places (Value Objects)

A menudo encontrará la misma lógica de validación repetida en la misma clase, o incluso en diferentes clases. Como ejemplo, eche un vistazo a la siguiente clase de usuario y cómo tiene que validar una dirección de correo electrónico en varios lugares, utilizando una función de la biblioteca estándar del idioma.

```
final class User
{
private string emailAddress;
public function __construct(string emailAddress)
{
if (!is_valid_email_address(emailAddress)) {
throw new InvalidArgumentException(
'Invalid email address'
);
}
this.emailAddress = emailAddress;
}

// ...
public function changeEmailAddress(string emailAddress): void
{
if (!is_valid_email_address(emailAddress)) {
throw new InvalidArgumentException(
'Invalid email address'
);
}
this.emailAddress = emailAddress;
}
}

expectException(
InvalidArgumentException.className,
'email',
function () {
new User('not-a-valid-email-address');
}
Creates a valid
);

user = new User('valid@emailaddress.com');
expectException(
InvalidArgumentException.className,
'email',
function () use (user) {
user.changeEmailAddress('not-a-valid-email-address');
}
);

```

Aunque podría extraer fácilmente la lógica de validación de la dirección de correo electrónico en un método separado, la mejor solución es introducir un nuevo tipo de objeto que represente una dirección de correo electrónico válida. Dado que esperamos que todos los objetos sean válidos en el momento en que se crean, podemos omitir la parte "válida" del nombre de la clase e implementarla de la siguiente manera.

```
final class EmailAddress
{
private string emailAddress;
public function __construct(string emailAddress)
{
if (!is_valid_email_address(emailAddress)) {
throw new InvalidArgumentException(
'Invalid email address'
);
}
this.emailAddress = emailAddress;
}
}
```

Siempre que encuentre un objeto EmailAddress, sabrá que representa un valor que ya ha sido validado:

```
final class User
{
private EmailAddress emailAddress;
public function __construct(EmailAddress emailAddress)
{
this.emailAddress = emailAddress;
}
// ...
public function changeEmailAddress(EmailAddress emailAddress): void
{
this.emailAddress = emailAddress;
}
}
```

Envolver valores dentro de nuevos objetos llamados objetos de valor no solo es útil para evitar la lógica de validación repetida. Tan pronto como observe que un método acepta un valor de tipo primitivo (string, int, etc.), debería considerar la posibilidad de introducir una clase para él. La pregunta guía para decidir si hacer esto o no es: "¿Cualquier string, int, etc. es aceptable aquí? " Si la respuesta es no, introduzca una nueva clase para el concepto.
Debe considerar que la clase de objeto de valor en sí es un tipo, al igual que string, int, etc., son tipos. Al introducir más objetos para representar conceptos de dominio, está ampliando efectivamente el sistema de tipos. El compilador o el tiempo de ejecución de su lenguaje podrá brindarle soporte mucho mejor, porque puede realizar la verificación de tipos por usted y asegurarse de que solo se utilicen los tipos correctos al pasar argumentos de método y devolver valores.

- Extract new objects to represent composite values (Money: Represented by Amount and Currency Value Objects)

Al crear todos estos nuevos tipos, encontrará que algunos de ellos naturalmente pertenecen juntos y siempre se pasan juntos de una llamada a otro. Por ejemplo, una cantidad de dinero siempre viene con la moneda de la cantidad, como en la siguiente lista. Si un método recibiera solo una cantidad, no sabría cómo manejarlo.

```
final class Amount
{
// ...
}

final class Currency
{
// ...
}

final class Product
{
public function setPrice(
Amount amount,
Currency currency
): void {
// ...
}
}

final class Converter
{
public function convert(
Amount localAmount,
Currency localCurrency,
Currency targetCurrency
): Amount {
// ...
}
}
```

En este último ejemplo, el tipo de retorno es bastante confuso. Se devolverá un Monto y se espera que la moneda de este monto coincida con la Moneda objetivo dada. Pero esto no es evidente al observar los tipos utilizados en este método.
Siempre que observe que los valores van juntos (o siempre se pueden encontrar juntos), envuelva esos valores en un nuevo tipo. En el caso de Monto y Moneda, un buen nombre para la combinación de los dos podría ser "dinero", lo que da como resultado la clase Money.

```
final class Money
{
public function __construct(Amount amount, Currency currency)
{
// ...
}
}
```

El uso de este tipo indica que desea mantener estos valores juntos, aunque si desea usarlos por separado, aún puede hacerlo.

Agregar más tipos de objetos conduce a más escritura. ¿Es eso realmente necesario?

100 tiene menos caracteres que el nuevo Amount (100), pero toda esa escritura adicional le brinda los beneficios de usar tipos de objetos:
1 Puede estar seguro de que los datos que envuelve el objeto han sido validados.
2 Un objeto generalmente expone comportamientos adicionales significativos que hacen uso de sus datos.
3 Un objeto puede mantener juntos valores que van juntos o que tienen alta cohesión.
4 Un objeto le ayuda a mantener los detalles de implementación lejos de sus clientes.

Si cree que es complicado crear todos estos objetos uno por uno basándose en valores primitivos, siempre puede introducir métodos auxiliares para crearlos. Aquí tienes un ejemplo:
// Antes de:
money = new Money(new Amount(100), new Currency('USD'));
// Después:
money = Money.create(100, 'USD');

- Use assertions to validate constructor arguments

Ya hemos visto varios ejemplos de constructores que lanzan excepciones cuando algo anda mal. La estructura general es siempre así:

if (somethingIsWrong ()) {
lanzar una nueva InvalidArgumentException (/ * ... * /);
}

Estas verificaciones al comienzo de los métodos se denominan "afirmaciones" y son básicamente verificaciones de seguridad. Las afirmaciones se pueden utilizar para establecer la situación, examinar los materiales y señalar si algo anda mal. Por esta razón, las afirmaciones también se denominan "verificaciones de condiciones previas". Una vez que haya superado estas afirmaciones, debería ser seguro realizar la tarea en cuestión con los datos que se han proporcionado.
Debido a que a menudo escribirá los mismos tipos de comprobaciones en muchos lugares diferentes, será conveniente utilizar una biblioteca de aserciones en su lugar. 1 Dicha biblioteca contiene muchas funciones de aserción que cubrirán casi todas las situaciones. Estos son algunos ejemplos:

Assertion.greaterThan (valor, límite);
Assertion.isCallable (valor);
Afirmación entre (
valor,
límite inferior,
limite superior
);
// y así...

La pregunta es siempre: "¿Debería verificar que estas assertions funcionen en una prueba unitaria para su objeto?" La pregunta guía es: "¿Sería teóricamente posible que en tiempo de ejecución del lenguaje detectara este caso?" Si la respuesta es sí, no escriba una prueba unitaria.
Por ejemplo, un lenguaje escrito dinámicamente como PHP no tiene una forma de establecer el tipo de un argumento en una lista de <nombre de clase>. En cambio, tendría que confiar en el tipo de array que es bastante genérico. Para verificar que un array dado es de hecho una lista plana de objetos de cierto tipo, usaría una aserción, como esta:
 
```
final class EventDispatcher
{
public function __construct(array eventListeners)
{
Assertion.allIsInstanceOf(
eventListeners,
EventListener.className
);
// ...
}
}
```

Dado que esta es una condición de error que podría detectar un sistema de tipos más evolucionado, no es necesario que escriba una prueba unitaria que detecte la excepción AssertionFailedException lanzada por allIsInstanceOf (). Sin embargo, si tiene que inspeccionar un valor dado y verificar que esté dentro de un rango determinado, o si tiene que verificar la cantidad de elementos en una lista, etc., tendrá que escribir una prueba unitaria que muestre que ha cubierto los casos de borde. Volviendo a un ejemplo anterior, el invariante de dominio de que una latitud determinada siempre está entre –90 y 90 inclusive debe verificarse con una prueba.

```
expectException(
AssertionFailedException.className,
'latitude',
function() {
new Coordinates(-90.1, 0.0)
}
);
// and so on...
```

No recopile excepciones

Aunque las herramientas a veces lo permiten, no debe guardar las excepciones de aserción y lanzarlas como una lista. Las assertions no están destinadas a proporcionar al usuario una lista conveniente de cosas que están mal. Están destinados al programador, que necesita saber que está utilizando un constructor o un método de forma incorrecta. Tan pronto como notes algo malo, haz que el objeto grite.
Si desea proporcionar al usuario una lista de cosas incorrectas sobre los datos que proporcionó (al enviar un formulario, enviar una solicitud de API, etc.), debe usar un objeto de transferencia de datos (DTO) y validarlo en su lugar. Discutiremos este tipo de objeto más luego.

- Don’t inject dependencies; optionally pass them as method arguments

Los servicios pueden tener dependencias y deben inyectarse como argumentos de constructor. Pero a otros objetos no se les debe inyectar ninguna dependencia, solo valores, objetos de valor o listas de ellos. Si un objeto de valor aún necesita un servicio para realizar alguna tarea, puede inyectarlo opcionalmente como un argumento de método, como en la siguiente lista.

```
final class Money
{
private Amount amount;
private Currency currency;
public function __construct(Amount amount, Currency currency)
{
this.amount = amount;
this.currency = currency;
}
public function convert(
ExchangeRateProvider exchangeRateProvider,
Currency targetCurrency
): Money {

exchangeRate = exchangeRateProvider.getRateFor(
this.currency,
targetCurrency
);

return exchangeRate.convert(this.amount);
}
}
```

A veces puede parecer un poco extraño pasar un servicio como argumento de método, por lo que tiene sentido considerar también implementaciones alternativas. Tal vez no deberíamos pasar el servicio ExchangeRateProvider, sino solo la información que obtenemos de él: ExchangeRate. Esto requeriría que Money exponga sus objetos internos Amount y Currency, pero ese puede ser un precio razonable a pagar por no inyectar la dependencia.
Esto da como resultado una situación como la siguiente.

```
final class ExchangeRate
{
public function __construct(
Currency from,
Currency to,
Rate rate
) {
// ...
}
public function convert(Amount amount): Money
{
// ...
}
}
money = new Money(/* ... */);
exchangeRate = exchangeRateProvider.getRateFor(
money.currency(),
targetCurrency
);
converted = exchangeRate.convert(money.amount());
```

Después de mover las cosas una vez más, podríamos conformarnos con una solución que involucre solo exponer el objeto Moneda interno de Money, no su Cantidad, como se hace en la siguiente lista. (Volveremos al tema de exponer las partes internas de los objetos en la sección 6.3).

```
final class Money
{
public function convert(ExchangeRate exchangeRate): Money
{
Assertion.equals(
this.currency,
exchangeRate.fromCurrency()
);
return new Money(
exchangeRate.rate().applyTo(this.amount),
exchangeRate.targetCurrency()
);
}
}
money = new Money(/* ... */);
exchangeRate = exchangeRateProvider.getRateFor(
money.currency(),
targetCurrency
);
converted = money.convert(exchangeRate);
```

Se podría argumentar que esta solución expresa más claramente el conocimiento de dominio que tenemos sobre el dinero y los tipos de cambio. Por ejemplo, la cantidad convertida estará en la moneda de destino del tipo de cambio, y su moneda "fuente" será la misma moneda que la moneda del monto original.
En algunos casos, la necesidad de pasar servicios como argumentos de método podría ser un indicio de que el comportamiento debería implementarse como un servicio. En el caso de convertir una cantidad de dinero a una moneda determinada, también podríamos crear un servicio y dejar que haga el trabajo, recopilando toda la información relevante de los objetos Monto y Moneda que se le proporcionan.

```
final class ExchangeService
{
private ExchangeRateProvider exchangeRateProvider;
public function __construct(
ExchangeRateProvider exchangeRateProvider
) {
this.exchangeRateProvider = exchangeRateProvider;
}
public function convert(
Money money,
Currency targetCurrency
): Money {
exchangeRate = this.exchangeRateProvider
.getRateFor(money.currency(), targetCurrency);
return new Money(
exchangeRate.rate().applyTo(money.amount()),
targetCurrency
);
}
}
```

La solución que elija dependerá de qué tan cerca desee mantener el comportamiento de los datos, si cree que es demasiado para un objeto como Money saber también sobre los tipos de cambio, o cuánto desea evitar exponer las partes internas del objeto.

- Use named constructors

Para los servicios, está bien usar la forma estándar de definir constructores (función pública __construct()). Sin embargo, para otros tipos de objetos, se recomienda que utilice constructores con nombre. Estos son métodos estáticos públicos que devuelven una instancia. Podrían considerarse fábricas de objetos.

### Crear a partir de valores de tipo primitivo

Un caso común para usar constructores con nombre es construir un objeto a partir de uno o más valores de tipo primitivo. Esto da como resultado métodos como fromString (), fromInt (), etc. Como ejemplo, observe la siguiente clase Date.

```
final class Date
{
private const string FORMAT = 'd/m/Y';
private DateTime date;
private function __construct()
{
// do nothing here
}
public static function fromString(string date): Date
{
object = new Date();
DateTime = DateTime.createFromFormat(
Date.FORMAT,
date
);
We’d still have to assert that
createFromFormat() doesn’t
return false.
object.date = DateTime;
return object;
}
}
date = Date.fromString('1/4/2019');
```

Es importante agregar un método constructor regular, pero privado, para que los clientes no puedan omitir el constructor con nombre que les ofrece, lo que posiblemente dejaría el objeto en un estado no válido o incompleto.

Espera, ¿esto funciona?
Puede parecer extraño que este método público estático fromString () pueda crear una nueva instancia de objeto y manipular la propiedad de fecha de la nueva instancia. Después de todo, esta propiedad es privada, por lo que no debería permitirse, ¿verdad?

El alcance de los métodos y propiedades generalmente se basa en clases, no en instancias, por lo que las propiedades privadas pueden ser manipuladas por cualquier objeto, siempre que sea de la misma clase exacta. El método fromString () en este ejemplo cuenta como un método de la misma clase, que
es por eso que puede manipular la propiedad de la fecha directamente, sin la necesidad de un setter.

No agregue inmediatamente toString (), toInt (), etc. Cuando agrega un constructor con nombre que crea un objeto basado en un valor de tipo primitivo, puede sentir la necesidad de simetría y desea agregar un método que pueda convertir el objeto de nuevo al valor de tipo primitivo. Por ejemplo, tener un constructor fromString () puede llevarlo a proporcionar automáticamente un método toString () también. Asegúrese de hacer esto solo una vez que haya una necesidad comprobada.

### Introduce a domain-specific object

Cuando discuta el concepto de una "orden de venta" con su experto en el dominio, nunca hablarían sobre "construir" una orden de venta. Tal vez hablarían sobre "crear" un pedido de ventas, o podrían usar un término más específico como "realizar" un pedido de ventas. Busque estas palabras y utilícelas como nombres de métodos para sus constructores nombrados.

```
final class SalesOrder
{
public static function place(/* ... */): SalesOrder
{
// ...
}
}
salesOrder = SalesOrder.place(/* ... */);
```

Opcionalmente, use el constructor privado para aplicar restricciones. Algunos objetos pueden ofrecer varios constructores con nombre, porque hay diferentes formas de construirlos. Por ejemplo, si desea un valor decimal con cierta precisión, puede elegir un valor entero con una precisión entera positiva como la forma normalizada de representar dicho número. Al mismo tiempo, es posible que desee permitir que los clientes utilicen sus valores existentes, que son cadenas o flotantes, como entrada para trabajar con dicho valor decimal. El uso de un constructor privado ayuda a garantizar que sea cual sea el método de construcción elegido, el objeto terminará en un estado completo y consistente. Por ejemplo:

```
final class DecimalValue
{
private int value;
private int precision;
private function __construct(int value, int precision)
{
this.value = value;
Assertion.greaterOrEqualThan(precision, 0);
this.precision = precision;
}
public static function fromInt(
int value,
int precision
): DecimalValue {
return new DecimalValue(value, precision);
}
public static function fromFloat(
float value,
int precision
): DecimalValue {
return new DecimalValue(
(int)round(value * pow(10, precision)),
precision
);
}
public static function fromString(string value): DecimalValue
{
result = preg_match('/^(\d+)\.(\d+)/', value, matches);
if (result == 0) {
throw new InvalidArgumentException(/* ... */);
}
wholeNumber = matches[1];
decimals = matches[2];
valueWithoutDecimalSign = wholeNumber . decimals;
return new DecimalValue(
(int)valueWithoutDecimalSign,
strlen(decimals)
);
}
}
```

En resumen, el uso de constructores con nombre ofrece dos ventajas principales:
 Se pueden utilizar para ofrecer varias formas de construir un objeto.
 Se pueden utilizar para introducir sinónimos específicos de dominio para crear un objeto.
Además de crear entidades y objetos de valor, los constructores con nombre pueden utilizarse para ofrecer formas convenientes de crear instancias de excepciones personalizadas. Discutiremos estos más adelante, en la sección 5.2.

- Don’t use property fillers (fromArray(array $data))

La aplicación de todas las reglas de diseño de objetos de este libro conducirá a objetos que tienen el control total de lo que entra en ellos, lo que permanece dentro y lo que un cliente puede hacer con ellos. Una técnica que funciona completamente contra este estilo de diseño de objetos son los métodos de relleno de propiedades, que se parecen al siguiente método fromArray ().

```
final class Position
{
private int x;
private int y;
public static function fromArray(array data): Position
{
position = new Position();
position.x = data['x'];
position.y = data['y'];
return position;
}
}
```

Este tipo de método podría incluso convertirse en una utilidad genérica que copiaría valores de la matriz de datos en las propiedades correspondientes mediante la reflexión. Aunque puede parecer conveniente, las partes internas del objeto ahora están al aire libre, así que siempre asegúrese de que la construcción de un objeto se realice de una manera que esté completamente controlada por el objeto en sí.

Al final de este capítulo, veremos una excepción a esta regla. Para los objetos de transferencia de datos, un relleno de propiedades podría ser una forma de mapear, por ejemplo, datos de formularios en un objeto. Un objeto de este tipo no necesita proteger sus datos internos tanto como una entidad o un objeto de valor.

- Don’t put anything more into an object than it needs

Es común comenzar a diseñar un objeto pensando en lo que debe incluir. Para los servicios, puede terminar inyectando más dependencias de las que necesita, por lo que debe inyectar dependencias solo cuando las necesite. Lo mismo ocurre con otros tipos de objetos: no requiera más datos de los estrictamente necesarios para implementar el comportamiento del objeto.
Un tipo de objeto que a menudo termina transportando más datos de los necesarios es un objeto de evento, que representa algo que ha sucedido en algún lugar de la aplicación. Un ejemplo de tal evento es la siguiente clase ProductCreated.

```
final class ProductCreated
{
public function __construct(
ProductId productId,
Description description,
StockValuation stockValuation,
Timestamp createdAt,
UserId createdBy,
/* ... */
) {
// ...
}
}

this.recordThat( // Inside the product entity
new ProductCreated(
/* ... */
)
);
```

Si no sabe qué datos de eventos serán importantes para los oyentes de eventos que aún no se han implementado, no agregue nada. Simplemente agregue un constructor sin argumentos en absoluto y agregue más datos cuando se necesiten. De esta manera, proporcionará datos según sea necesario.
¿Cómo saber qué datos deberían entrar realmente en el constructor de un objeto? Diseñando el objeto de una manera basada en pruebas. Esto significa que primero debe saber cómo se utilizará un objeto.

- Don’t test constructors

Escribir pruebas para sus objetos, especificando su comportamiento deseado, le permitirá averiguar qué datos se necesitan realmente en el momento de la construcción y qué datos se pueden proporcionar más adelante. También lo ayudará a determinar qué datos deben exponerse más adelante y qué datos pueden permanecer detrás de escena, como detalles de implementación del objeto.
Como ejemplo, echemos otro vistazo a la clase Coordenadas que vimos anteriormente.

```
final class Coordinates
{
// ...
public function __construct(float latitude, float longitude)
{
if (latitude > 90 || latitude < -90) {
throw new InvalidArgumentException(
'Latitude should be between -90 and 90'
);
}
this.latitude = latitude;
if (longitude > 180 || longitude < -180) {
throw new InvalidArgumentException(
'Longitude should be between -180 and 180'
);
}
this.longitude = longitude;
}
}
```

¿Cómo podemos probar que el constructor funciona? ¿Qué pasa con la siguiente prueba?

```
public function it_can_be_constructed(): void
{
coordinates = new Coordinates(60.0, 100.0);
assertIsInstanceOf(Coordinates.className, coordinates);
}
```

Esto no es muy informativo. De hecho, es imposible que la aserción falle a menos que el constructor haya lanzado una excepción, que es un flujo de ejecución que explícitamente no estamos probando aquí.
¿Cuál es la tarea del constructor? A juzgar por el código, se trata de asignar los argumentos del constructor dados a las propiedades internas del objeto. Entonces, ¿cómo podemos estar seguros de que ha funcionado? Podríamos agregar captadores, lo que nos permitiría averiguar qué hay dentro de las propiedades del objeto, de la siguiente manera

```
final class Coordinates
{
// ...
public function latitude(): float
{
return this.latitude;
}
public function longitude(): float
{
return this.longitude;
}
}
```

La siguiente lista muestra cómo podríamos usar esos captadores en una prueba unitaria.

```
public function it_can_be_constructed(): void
{
coordinates = new Coordinates(60.0, 100.0);
assertEquals(60.0, coordinates.latitude());
assertEquals(100.0, coordinates.longitude());
}
```

Pero ahora hemos introducido una forma para que los datos internos salgan del objeto, sin otra razón que probar el constructor. Mira lo que hemos hecho aquí: probamos el código del constructor después de escribirlo. Hemos estado probando este código, sabiendo lo que está sucediendo allí, lo que significa que la prueba está muy cerca de la implementación de la clase. Hemos estado colocando datos en un objeto, sin siquiera saber si alguna vez los volveremos a necesitar. En conclusión, hemos hecho demasiado, demasiado pronto, sin una buena dosis de distancia desde la implementación del objeto.
Lo único que podemos y debemos hacer en este punto es probar que el constructor no acepta argumentos inválidos. Hemos hablado de esto antes: debe verificar que proporcionar valores de latitud y longitud fuera de sus rangos aceptables activa una excepción, lo que hace que sea imposible construir el objeto Coordenadas. Más adelante, hablaremos más sobre la exposición de datos, pero por ahora siga el siguiente consejo:
 Solo pruebe un constructor para ver las formas en las que debería fallar.
 Solo pase datos como argumentos de constructor cuando los necesite para implementar un comportamiento real en el objeto.
 Solo agregue captadores para exponer datos internos cuando algún otro cliente que no sea la prueba los necesite.

Una vez que comience a agregar el comportamiento real al objeto, implícitamente probará la ruta feliz para el constructor de todos modos, porque al hacerlo, necesitará un objeto completamente instanciado.

- The exception to the rule: Data transfer objects

Las reglas descritas en este capítulo se aplican a entidades y objetos de valor; nos preocupamos mucho por la consistencia y validez de los datos que terminan dentro de dichos objetos. Estos objetos solo pueden garantizar un comportamiento correcto si los datos que utilizan también son correctos.
Hay otro tipo de objeto que no he mencionado hasta ahora, al que no se aplican la mayoría de las reglas anteriores. Es un tipo de objeto que encontrará en los bordes de una aplicación, donde los datos provenientes del mundo exterior se convierten en una estructura con la que la aplicación puede trabajar. La naturaleza de este proceso requiere que se comporte un poco diferente a las entidades y los objetos de valor.
Este tipo especial de objeto se conoce como objeto de transferencia de datos (DTO):
 Se puede crear un DTO utilizando un constructor normal.
 Sus propiedades se pueden configurar una a una.
 Todas sus propiedades están expuestas.
 Sus propiedades contienen solo valores de tipo primitivo.
 Las propiedades pueden contener opcionalmente otros DTO o matrices simples de DTO.

### Usar propiedades públicas

Dado que un DTO no protege su estado y expone todas sus propiedades, no hay necesidad de captadores y definidores. Esto significa que es suficiente usar propiedades públicas para ellos. Debido a que los DTO se pueden construir en pasos y no requieren que se proporcione una cantidad mínima de datos, no necesitan métodos de constructor.
Los DTO se utilizan a menudo como objetos de comando, que coinciden con la intención del usuario y contienen todos los datos necesarios para cumplir su deseo. Un ejemplo de un objeto de comando de este tipo es el siguiente comando ScheduleMeetup, que representa el deseo del usuario de programar una reunión con el título dado en la fecha indicada.

```
final class ScheduleMeetup
{
public string title;
public string date;
}
```

La forma en que puede usar un objeto de este tipo es, por ejemplo, llenándolo con los datos enviados con un formulario y luego pasándolo a un servicio, que programará la reunión para el usuario. Se puede encontrar un ejemplo de implementación en la siguiente lista.

```
final class MeetupController
{
public function scheduleMeetupAction(Request request): Response
{
formData = /* ... */;
scheduleMeetup = new ScheduleMeetup();
scheduleMeetup.title = formData['title'];
scheduleMeetup.date = formData['date'];
this.scheduleMeetupService.execute(scheduleMeetup);
// ...
}
}
```

El servicio creará una entidad y algunos objetos de valor y eventualmente los conservará. Cuando se crean instancias, estos objetos generarán excepciones si hay algún problema con los datos que se les proporcionaron. Sin embargo, estas excepciones no son realmente fáciles de usar; ni siquiera se pueden traducir fácilmente al idioma del usuario. Además, debido a que interrumpen el flujo de la aplicación, las excepciones no se pueden recopilar y devolver al usuario como una lista de errores de entrada.

### Don’t throw exceptions, collect validation errors

Si desea permitir que los usuarios corrijan todos sus errores de una vez, antes de volver a enviar el formulario, debe validar los datos del comando antes de pasar el objeto al servicio que lo va a manejar. Una forma de hacerlo es agregando un método validate () al comando, que puede devolver una lista simple de errores de validación. Si la lista está vacía,
significa que los datos enviados eran válidos.

```
final class ScheduleMeetup
{
public string title;
public string date;
public function validate(): array
{
errors = [];
if (this.title == '') {
errors['title'][] = 'validation.empty_title';
}
if (this.date == '') {
errors['date'][] = 'validation.empty_date';
}
DateTime.createFromFormat('d/m/Y', this.date);
errors = DateTime.getLastErrors();
if (errors['error_count'] > 0) {
errors['date'][] = 'validation.invalid_date_format';
}
return errors;
}
}
```

Las bibliotecas de formularios y validaciones pueden ofrecerle herramientas de validación más convenientes y reutilizables. Por ejemplo, los componentes de Symfony Form y Validator funcionan muy bien con este tipo de objeto de transferencia de datos.

### Use property fillers when needed

Anteriormente discutimos los rellenos de propiedad y cómo no se deben usar cuando se trabaja con la mayoría de los objetos; exponen todas las partes internas del objeto. En el caso de un DTO, esto no es un problema porque un DTO no protege sus partes internas de todos modos. Por lo tanto, si tiene sentido, puede agregar un método de relleno de propiedad a un DTO, como copiar datos de formulario o datos de solicitud JSON directamente en un objeto de comando. Dado que llenar las propiedades es lo primero que debería sucederle a un DTO, tiene sentido implementar el relleno de propiedades como un constructor con nombre.

```
final class ScheduleMeetup
{
public string title;
public string date;
public static function fromFormData(
array formData
): ScheduleMeetup {
scheduleMeetup = new ScheduleMeetup();
scheduleMeetup.title = formData['title'];
scheduleMeetup.date = formData['date'];
return scheduleMeetup;
}
}
```
