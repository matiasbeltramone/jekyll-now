---
layout: post
title: "Guía de objetos DDD"
tags: [Arquitecturas, Architecture, Clean Code, Código Limpio, DDD, Domain Driven Design]
---

## Introducción

No todos los objetos de nuestra aplicación se van a ver parecidos. Algunos objetos tendrán muchos métodos de consultas, algunos tendrán solo metodos de estilo comando.
Algunos tendrán un mix de ambos, pero con un determinado ratio de ellos. Podrás encontrarte con que diferentes tipos de objetos amenudo comparten ciertas caracteristicas,
que resultan en patrones reconocidos en la industria. Por ejemplo, los desarrolladores suelen hablar de "entities", "value objects", "application services", "controllers"
para indicar la naturaleza del objeto del que estamos hablando.

Vamos a estar hablando de algunos tipos comunes de objetos que podemos encontrar en una aplicacion y como podemos reconocerlos en su habitat natural.
Basicamente haremos una guía de objetos...

# Interacción de objetos comunes

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/104241824-034c4380-543d-11eb-9744-c41ab80c478b.png">
</p>

# Controllers

En una aplicación, siempre hay algún tipo de controlador frontal (front controller). Aquí es donde entran todas las solicitudes. Si usamos PHP, este podría ser nuestro archivo `index.php`.
En el framework Spring de Java, `DispatcherServlet` desempeña este papel: según el URI de la solicitud, su método y encabezados, etc., la llamada se reenviará a un controlador, donde la aplicación puede hacer todo lo que necesite antes de poder devolver un Respuesta adecuada.
Para las aplicaciones de línea de comandos (CLI), el "controlador frontal" sería el ejecutable que llamarías, como bin / console, artisan, etc. Según los argumentos que
proporcione el usuario, la llamada se reenviará a algo como un objeto de comando, donde la aplicación puede realizar la tarea solicitada por el usuario.

Aunque técnicamente son bastante diferentes, los `comandos de la consola` son conceptualmente bastante similares a los `controladores web`.
Ambos realizan trabajos que fueron solicitados desde fuera de la aplicación por una persona, o alguna otra aplicación, que envió una solicitud web o ejecutó
la aplicación de consola. Así que llamemos a los comandos de la consola y a los controladores web, "controladores" (controllers).

Los controladores suelen tener un código que revela de dónde vino la llamada. Encontrará menciones de un objeto de solicitud (Request), parámetros de solicitud, formularios, plantillas HTML,
una sesión tal vez o cookies (figura 10.2). Todos estos son conceptos web. Las clases utilizadas aquí a menudo se originan en el web framework que utiliza su aplicación.

Otros controladores mencionan argumentos, opciones o indicadores de la línea de comandos y contienen código para enviar líneas de texto a la terminal y
formateándolos de manera que la terminal pueda entenderlos (ver figura 10.3). Todos estos son signos para el lector de que esta clase toma información y produce la salida
para la línea de comandos.
 
Debido a que los controladores hablan sobre el mecanismo de entrega particular que inició una llamada a ellos (la web, la terminal), los controladores deben considerarse
código de infraestructura (infrastructure code). Facilitan la conexión entre el cliente, que vive en el mundo exterior, y el núcleo de la aplicación (core code).

Cuando un controlador ha examinado la entrada proporcionada, tomará cualquier información que necesite y luego llamará a un servicio de aplicación o un repositorio de modelos de lectura.
Se llamará a un servicio de aplicación cuando se supone que el controlador debe producir algún tipo de efecto secundario, como cuando se supone que debe realizar un cambio
en el estado de la aplicación, enviar un correo electrónico, etc. Se utilizará un repositorio de modelos de lectura si el controlador se supone que debe devolver
alguna información que el cliente solicitó.

```

Un objeto es un controlador si...

 Lo llama un controlador frontal (front controller) y, por lo tanto, es uno de los puntos de entrada para nuestro gráfico de servicios y sus dependencias (imagen abajo),
 contiene código de infraestructura que revela cuál es el mecanismo de entrega, y
 realiza llamadas a un servicio de aplicación o un repositorio de modelos de lectura (o ambos)

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/104243738-cc2b6180-543f-11eb-9bef-cd43dcd1d25d.png">
</p>

Un controlador web típico se parecería al siguiente en el caso de PHP:

```
namespace Infrastructure\UserInterface\Web;

use Infrastructure\Web\Form\ScheduleMeetupType;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\RedirectResponse;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\HttpFoundation\Request;

final class MeetupController extends AbstractController
{
    public function scheduleMeetupAction(Request request): Response
    {
        form = this.createForm(ScheduleMeetupType.className);
        
        form.handleRequest(request);
        
        if (form.isSubmitted() && form.isValid()) {
            // ... 
            return new RedirectResponse('/meetup-details/' . meetup.meetupId());
        }
        
        return this.render(
            'scheduleMeetup.html.twig',
            [
            'form' => form.createView()
            ]
        );
    }
}
```

Un controlador alternativo para linea de comandos podría verse como lo siguiente:
```
namespace Infrastructure\UserInterface\Cli;

use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;

final class ScheduleMeetupCommand extends Command
{
    protected function configure()
    {
        this
        .addArgument('title', InputArgument.REQUIRED)
        .addArgument('date', InputArgument.REQUIRED)
        // ...
        ;
    }
    
    public function execute(
        InputInterface input,
        OutputInterface output
    ) {
        title = input.getArgument('title');
        date = input.getArgument('date');
        // ...
        output.writeln('Meetup scheduled');
    }
}
```

# Application Service

Un servicio de aplicación representa la tarea a realizar. Obtiene cualquier dependencia inyectada como un argumento de constructor. Todos los datos relevantes que se necesitan para realizar la tarea, incluida la información contextual como el ID de usuario que inició sesión o la hora actual, se proporcionarán como argumentos del método. Cuando se originan los datos del propio cliente, serán datos de tipo primitivo. De esa manera, el controlador puede proporcionar al servicio de la aplicación los datos tal como los envió el cliente, sin convertirlos primero.

El código de un servicio de aplicación debe leerse como una receta, con todos los pasos necesarios para realizar el trabajo. Por ejemplo, "Saque un objeto de este repositorio de modelos de escritura, invoque un método en él y guárdelo de nuevo". O bien, "Recopile información de este repositorio de modelos de lectura y envíe un informe a un usuario determinado".

```
Un objeto es un servicio de aplicación si. . .

 realiza una sola tarea,
 no contiene ningún código de infraestructura; es decir, no se ocupa de la solicitud web en sí, de las consultas SQL, del sistema de archivos, etc., y
 describe un caso de uso único que debería tener la aplicación. A menudo se corresponderá uno a uno con una solicitud de función de un interesado. Por ejemplo, debería ser posible añadir un producto al catálogo, cancelar un pedido, enviar una nota de entrega a un cliente, etc.
```

El controlador web y el controlador de consola que vimos anteriormente tomarán los datos de la solicitud (a través de un formulario), o de los argumentos de la línea de comandos, y los proporcionarán al servicio de la aplicación, que se parece a lo siguiente:

```
namespace Application\ScheduleMeetup;

use Domain\Model\Meetup\Meetup;
use Domain\Model\Meetup\MeetupRepository;
use Domain\Model\Meetup\ScheduleDate;
use Domain\Model\Meetup\Title;

final class ScheduleMeetupService
{
    private MeetupRepository meetupRepository;
    
    public function __construct(MeetupRepository meetupRepository)
    {
        this.meetupRepository = meetupRepository;
    }
    
    public function schedule(
       string title, // 1
       string date,
       UserId currentUserId
    ): MeetupId {
       meetup = Meetup.schedule( // 2
         this.meetupRepository.nextIdentity(),
         Title.fromString(title),
         ScheduledDate.fromString(date),
         currentUserId
       );

       this.meetupRepository.save(meetup); // 3

       return meetup.meetupId(); // 4
    }
}

1. The application service receives primitive-type arguments.
2. It converts these primitive-type values to value objects and instantiates a new Meetup entity using these objects
3. It saves the meetup to the write model repository.
4. Finally, it returns the identifier of the new Meetup.

```

A veces, los servicios de aplicacion se denominan "Command Handlers", pero seguirán siendo servicios de aplicación. En lugar de invocar un servicio de aplicación mediante argumentos de tipo primitivo, también puede llamarlo proporcionando un objeto de comando (Command Object), que representa la solicitud del cliente en un solo objeto. Dicho objeto se denomina objeto de transferencia de datos (DTO) porque se puede utilizar para llevar los datos proporcionados por el cliente y transferirlos como una
cosa del controlador al servicio de la aplicación. Debe ser sencillo, fácil de construir el objeto y debe contener solo valores de tipo primitivo, listas simples y, opcionalmente, otros DTO si se requiere algún tipo de jerarquía en especial.

```
namespace Application\ScheduleMeetup;

final class ScheduleMeetup
{
   public string title;
   public string date;
}

// This command contains the data needed to perform the task of scheduling a meetup.

final class ScheduleMeetupService
{
   // ...
   public function schedule(
      ScheduleMeetup command,
      UserId currentUserId
   ): MeetupId {
      meetup = Meetup.schedule(
         this.meetupRepository.nextIdentity(),
         Title.fromString(command.title),
         ScheduledDate.fromString(command.date),
         currentUserId
      );
      
      // ...
   }
}

// The application service could then take the data from the command object.
```

La ventaja de utilizar un objeto comando dedicado es que es fácil instanciarlo en función de datos de cadena deserializados, como un cuerpo de solicitud JSON o XML. También funciona bien con bibliotecas de formularios, que pueden asignar los datos enviados directamente a las propiedades del comando DTO.

# Write model repositories

A menudo, un servicio de aplicación realiza un cambio en el estado de la aplicación, y esto generalmente significa que un objeto de dominio debe modificarse y persistirse. El servicio de la aplicación utiliza una abstracción para esto: un `repositorio`. Para ser más específico, un repositorio de modelos de escritura, porque es
solo se ocupa de recuperar una entidad y almacenar los cambios que se le hacen.
La abstracción en sí será una interfaz que al servicio de aplicación se inyecta como dependencia. Esta interfaz no expone ningún detalle sobre cómo se conservará el objeto. Solo ofrece algunos métodos de propósito general como getById(), save(), add() o update(). Una implementación correspondiente completará los detalles,
tales como qué consultas SQL se emitirán o qué ORM se utilizará para mapear el objeto a una fila en la base de datos.

```
Un objeto es un repositorio de modelos de escritura si...

 ofrece métodos para recuperar un objeto del lugar de almacenamiento y para guardarlo, y
 su interfaz oculta la tecnología subyacente que se ha utilizado.
```

Como ejemplo, el siguiente snippet muestra el MeetupRepository que mostramos como dependencia anteriormente:

```
namespace Domain\Model\Meetup;

interface MeetupRepository
{
   public function save(Meetup meetup): void;
   public function nextIdentity(): MeetupId;
   /**
   * @throws MeetupNotFound
   */
   public function getById(MeetupId meetupId): Meetup
}

namespace Infrastructure\Persistence\DoctrineOrm;

use Doctrine\ORM\EntityManager;
use Domain\Model\Meetup\Meetup;
use Domain\Model\Meetup\MeetupId;
use Ramsey\Uuid\UuidFactoryInterface;

final class DoctrineOrmMeetupRepository implements MeetupRepository
{
    private EntityManager entityManager;
    private UuidFactoryInterface uuidFactory;
    
    public function __construct(
       EntityManager entityManager,
       UuidFactoryInterface uuidFactory
    ) {
       this.entityManager = entityManager;
       this.uuidFactory = uuidFactory;
    }
    
    public function save(Meetup meetup): void
    {
       this.entityManager.persist(meetup);
       this.entityManager.flush(meetup);
    }
    
    public function nextIdentity(): MeetupId
    {
       return MeetupId.fromString(
          this.uuidFactory.uuid4().toString()
       );
    }
    
    // ...
}
```

# Entities

Los objetos que se persisten serán los que le interesen al usuario, los que deberían recordarse incluso cuando la aplicación deba reiniciarse. Estos son
las entidades de la aplicación. Las entidades representan los conceptos de dominio de la aplicación. Contienen datos relevantes y ofrecen un comportamiento útil relacionado con estos datos. En términos de diseño de objetos, a menudo tendrán constructores con nombre porque esto le permite usar nombres específicos de dominio para crear este tipo particular de entidad (named constructors). También tendrán métodos modificadores, que son métodos de comando que cambian el estado de la entidad (CQS - Command Methods).
Las entidades solo tendrán unos pocos métodos de consulta, si es que tienen alguno. La recuperación de información generalmente se delega a un tipo particular de objeto, llamado objeto de consulta (Query object). Volveremos a esto.

Entidades adecuadas (Proper Entities)

Al igual que cualquier objeto, una entidad se protege ferozmente de terminar en un estado inválido. Muchas entidades en su naturaleza no deben considerarse entidades adecuadas, según esta definición.

Cuando se permite un cambio de estado, una entidad generalmente produce un evento de dominio que representa el cambio (Domain Events). Estos eventos se pueden utilizar para averiguar qué ha cambiado exactamente y para anunciar este cambio a otras partes de la aplicación que quieran responder.

```
Un objeto es una entidad si...

1. Tiene un identificador único,
2. Tiene un ciclo de vida,
3. Será persistido (conservado) por un repositorio de modelos de escritura y luego se puede recuperar de él,
4. Utiliza constructores con nombre y métodos de comando para proporcionar al usuario formas de instanciarlo y manipular su estado, y
5. Produce eventos de dominio cuando se crea una instancia o se modifica.
```

Esta imagen representa como trabajan en conjunto los diferentes tipos de objetos vistos para realizar el caso de uso correspondiente:

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/104307762-6a561080-54ae-11eb-8703-694e8f906bd4.png">
</p>

# Value Objects

Los objetos de valor son envoltorios para valores de tipo primitivo, lo que agrega significado y comportamiento útil a estos valores. En el contexto del viaje desde el controlador hasta el servicio de aplicación y el repositorio, debe tenerse en cuenta que a menudo es el servicio de aplicación el que crea una instancia de los objetos de valor y luego los pasa como argumentos al constructor o al método modificador de una entidad. Por tanto, acaban siendo utilizados o almacenados dentro de la entidad.
Sin embargo, es bueno recordar que los objetos de valor no están pensados solo para usarse en combinación con entidades. Se pueden usar en cualquier lugar, y un objeto de valor es de hecho una forma preferida de transmitir valores.

```
Un objeto es un objeto de valor...

 si es inmutable,
 si envuelve datos de tipo primitivo,
 si agrega significado mediante el uso de términos específicos del dominio (p. Ej., No es solo un int, es un año),
 si impone limitaciones mediante la validación (p. Ej., No es una cadena cualquiera, es una cadena con una "@"), y
 actúa como un atractor de comportamiento útil relacionado con el concepto (por ejemplo, Position.toTheLeft(int steps))
```

La entidad Meetup que vimos que se instanciaba anteriormente, junto con sus objetos de valor relacionados y eventos de dominio, se parece a lo siguiente.

```
namespace Domain\Model\Meetup;

final class Meetup
{
    private array events = [];
    private MeetupId meetupId;
    private Title title;
    private ScheduledDate scheduledDate;
    private UserId userId;

    private function __construct() {}
    
    public static function schedule(
       MeetupId meetupId,
       Title title,
       ScheduledDate scheduledDate,
       UserId userId
    ): Meetup {
       meetup = new Meetup();
       
       meetup.meetupId = meetupId;
       meetup.title = title;
       meetup.scheduledDate = scheduledDate;
       meetup.userId = userId;
       
       meetup.recordThat(
           new MeetupScheduled(
             meetupId,
             title,
             scheduledDate,
             userId
          );
       );
       
       return meetup;
    }
    
    // The following methods are examples of other behavior that this Meetup entity could offer...
    
    public function reschedule(ScheduledDate scheduledDate): void
    {
       // ...
       this.recordThat(
          new MeetupRescheduled(this.meetupId, scheduledDate)
       );
    }
    
    public function cancel(): void
    {
       // ...
    }
    
    // ...
    
    private function recordThat(object event): void
    {
       this.events[] = event;
    }
    
    public function releaseEvents(): array
    {
       return this.events;
    }
    
    public function clearEvents(): void
    {
       this.events = [];
    }
}


final class Title
{
    private string title;
    
    private function __construct(string title)
    {
        Assertion.notEmpty(title);
        this.title = title;
    }
    
    public static function fromString(string title): Title
    {
       return new Title(title);
    }
    
    public function abbreviated(string ellipsis = '...'): string // This is an example of useful behavior that value objects tend to attract.
    {
       // ...
    }
}

final class MeetupId
{
    private string meetupId;
    
    private function __construct(string meetupId)
    {
        Assertion.uuid(meetupId);
        this.meetupId = meetupId;
    }
    
    public static function fromString(string meetupId): MeetupId
    {
        return new MeetupId(meetupId);
    }
}
```

# Event Listeners

Ya hemos encontrado eventos de dominio. Se pueden usar para notificar a otros servicios sobre cosas que han sucedido dentro del modelo de escritura. Estos otros servicios (listeners) pueden realizar acciones secundarias, después de que se haya realizado el trabajo principal. Dado que los servicios de aplicación son los que realizan estas tareas principales, los eventos de dominio se pueden usar para notificar a otros servicios después de que se realiza el servicio de aplicación. También pueden hacerlo en el último momento, justo antes de regresar. En ese momento, un servicio de aplicación podría recuperar los eventos registrados de la entidad que ha modificado y entregarlos al despachador de eventos (Event Dispatcher), como se muestra en el siguiente snippet de código:

```
final class RescheduleMeetupService
{
    private EventDispatcher dispatcher;
    
    public function __construct(
       // ...
       EventDispatcher dispatcher
    ) {
       this.dispatcher = dispatcher
    }

    public function reschedule(MeetupId meetupId, /* ... */): void
    {
       meetup = /* ... */;
       meetup.reschedule(/* ... */);
       
       this.dispatcher.dispatchAll(meetup.recordedEvents()); // Dispatch any event that has been recorded inside the Meetup entity.
    }
}

```

Internamente, el despachador reenviará todos los eventos a los servicios llamados "oyentes (listerns)" o "suscriptores (subscribers)", que se han registrado para ciertos tipos particulares de eventos.
Un Event Listener podría realizar acciones secundarias, para las cuales incluso podría llamar a otro servicio de aplicación. Puede utilizar cualquier otro servicio que necesite, como enviar correos electrónicos de notificación sobre el evento de dominio que acaba de ocurrir. Tomemos, por ejemplo, el siguiente listener de NotifyGroupMembers, que notificará a los miembros del grupo cuando se reprograme una reunión.

```
final class NotifyGroupMembers
{
   public function whenMeetupRescheduled(
      MeetupRescheduled event
   ): void {
      /*
      * Send an email to group members using the information from
      * the event object.
      */
   }
}
```

```
Un objeto es un event listener...

 si es un servicio inmutable, con sus dependencias inyectadas, y
 si tiene al menos un método que acepta un solo argumento que sea un evento de dominio.
```

# Read Models and Read Model Repositories

Como se mencionó anteriormente, un controlador podría invocar un servicio de aplicación para realizar una tarea, pero también puede invocar un repositorio de modelos de lectura para recuperar información. Tal repositorio devolverá objetos. Estos objetos no están destinados a ser manipulados, sino a leer información. Anteriormente los llamamos "objetos de consulta" (query objects); solo tienen métodos de consulta, lo que significa que sus usuarios no pueden influir en su estado.
Cuando la llamada al repositorio de modelos de lectura ocurre dentro del controlador, el modelo de lectura que se devuelve podría pasarse al renderizador de plantillas, que podría generar una respuesta HTML usándolo. O podría usarse con la misma facilidad para generar una respuesta codificada JSON a una llamada API. En todos estos casos, el modelo de lectura está diseñado específicamente para adaptarse a la respuesta que se va a generar. Todos los datos necesarios para un caso de uso particular deben estar disponibles dentro del modelo de lectura y no se deben realizar consultas adicionales. Este modelo de lectura es un DTO, porque se utilizará para transferir datos desde el núcleo de la aplicación al mundo exterior. Los valores que se pueden recuperar de dicho modelo de lectura deben tener tipos primitivos.
Como ejemplo, tome el siguiente repositorio de modelos de lectura, que muestra una lista de las próximas reuniones. Sirve para un caso de uso específico y contiene solo los datos necesarios para representar una lista simple.

```
UpcomingMeetup is a read model (or “view model”)—a DTO that carries relevant data about upcoming meetups to be shown in a list on a web page.

namespace Application\UpcomingMeetups;

final class UpcomingMeetup
{
   public string title;
   public string date;
}
```

```
It comes with a repository that returns instances of UpcomingMeetup and could be used by a web controller and passed to a template renderer.

interface UpcomingMeetupRepository
{
    public function upcomingMeetups(DateTimeDateTime today): UpcomingMeetup[]
}
```

```
namespace Infrastructure\ReadModel;

use Application\UpcomingMeetups\UpcomingMeetupRepository;
use Doctrine\DBAL\Connection;

final class UpcomingMeetupDoctrineDbalRepository implements UpcomingMeetupRepository
{
   private Connection connection;
   
   public function __construct(Connection connection)
   {
      this.connection = connection;
   }
   
   public function upcomingMeetups(DateTime today): UpcomingMeetup[]
   {
       rows = this.connection./* ... */;
       
       return array_map(function (array row) {
           upcomingMeetup = new UpcomingMeetup();
           upcomingMeetup.title = row['title'];
           upcomingMeetup.date = row['date'];
           return upcomingMeetup;
       }, rows);
   }
}

This implementation of UpcomingMeetupRepository fetches data directly from the database. It then creates instances of the UpcomingMeetup read model.
```

Un servicio de aplicación en sí mismo también puede utilizar un repositorio de modelos de lectura para recuperar información. Luego, puede usar la información para tomar decisiones o tomar acciones adicionales. Un modelo de lectura que utiliza un servicio de aplicación suele ser un modelo de lectura "más inteligente" que uno que se utiliza para generar una respuesta. Utiliza objetos de valor (value objects) adecuados para sus valores de retorno, en lugar de valores de tipo primitivo, por lo que el servicio de la aplicación no tiene que preocuparse por la validez del modelo de lectura. A menudo parece que un modelo de lectura de este tipo es en sí mismo un modelo de escritura, excepto que no hay forma de realizar cambios en él; después de todo, es un objeto de consulta.
En cuanto a los repositorios de modelos de lectura, deben separarse en una abstracción y una implementación concreta. Al igual que con los repositorios de modelos de escritura, una interfaz ofrecerá uno o más métodos de consulta que se pueden utilizar para recuperar los modelos leídos. La interfaz no da una pista sobre el mecanismo de almacenamiento subyacente para estos modelos.

```
Un objeto es un repositorio de modelos de lectura...

 si tiene métodos de consulta que se ajustan a un caso de uso específico y devolverá modelos de lectura, que también son específicos para ese caso de uso.
```

```
Un objeto es un modelo de lectura...

 si solo tiene métodos de consulta, es decir, es un objeto de consulta (y por lo tanto es
inmutable),
 si está diseñado específicamente para un caso de uso determinado, y
 si todos los datos necesarios (y no más) están disponibles en el momento en que se recupera el objeto.
```

Tenga en cuenta que la distinción entre un repositorio de modelos de lectura y un servicio regular que devuelve un fragmento de información no es tan clara. Por ejemplo, considere la situación en la que un servicio de aplicación necesita un tipo de cambio para convertir un valor monetario en una moneda extranjera. Podría decirse que un servicio que puede proporcionar dicha información es básicamente un repositorio, desde el cual puede obtener el tipo de cambio para una determinada conversión de moneda. Dicho servicio tiene acceso a una "colección" de tipos de cambio que se define en algún lugar que no nos importa. Aún así, este servicio también podría considerarse un servicio regular, y también podría llamarse ExchangeRateProvider o algo así.
La idea principal es que para todos estos servicios necesita una abstracción (en el siguiente snippet vemos un ejemplo) y una implementación concreta, porque la abstracción describe lo que está buscando y la implementación describe cómo puede obtenerlo.

```
A regular service

namespace Application\ExchangeRates;

/* The abstraction is the interface that represents the question we’re asking */
interface ExchangeRateProvider
{
   public function getRateFor(Currency from,Currency to): ExchangeRate;
}

/* The types of the return values used by the interface are also part of the abstraction, because
we care about how we can use these values, but not about how their data ends up in them. */
final ExchangeRate
{
   // ...
}
```

En cuanto a su diseño, algunos objetos no son muy diferentes de otros. Por ejemplo, los eventos de dominio se parecen mucho a los objetos de valor: son objetos inmutables que contienen datos que pertenecen juntos o tienen alta cohesión. La diferencia entre un evento de dominio y un objeto de valor es cómo y dónde se usa: un evento de dominio se creará y registrará dentro de una entidad y luego se enviará (se hará un dispatch en definitiva); un objeto de valor modela un aspecto de la entidad.

# Abstractions, concretions, layers, and dependencies

Hasta ahora, hemos encontrado diferentes tipos de objetos que puede encontrar en su aplicación web o de consola promedio. Además de ciertas características, como los tipos de métodos que tienen estos objetos, qué tipo de información exponen o qué tipo de comportamiento ofrecen, también debemos considerar si son abstractos o concretos, y de qué manera estos objetos son dependientes entre sí...

En términos de abstracción, podemos definir los siguientes rasgos para los tipos de objetos que hemos discutido hasta ahora:

🩸 Los controladores son concretos. A menudo están acoplados a un framework y son específicos para el mecanismo de delivery como HTTP. No tienen ni necesitan una interfaz. El único momento en el que le gustaría ofrecer una implementación alternativa es cuando cambia de framework. En ese caso, querrá volver a escribir estos controladores en lugar de crear una segunda implementación para ellos.

🩸 Los servicios de aplicación son concretos. Representan un caso de uso muy específico de su aplicación. Si la historia de un caso de uso cambia, el servicio de la aplicación en sí cambia, por lo que no tienen una interfaz.

🩸 Las entidades y los objetos de valor son concretos. Son el resultado específico de la comprensión del dominio por parte del desarrollador. Este tipo de objetos evolucionan con el tiempo. No les proporcionamos una interfaz. Lo mismo ocurre con los objetos de modelo de lectura. Los definimos y usamos tal como son, nunca a través de una interfaz.

🩸 Los repositorios (para modelos de escritura y lectura) consisten en una abstracción y al menos una implementación concreta. Los repositorios son servicios que se acercan y se conectan a algo fuera de la aplicación, como una base de datos, el sistema de archivos o algún servicio remoto. Es por eso que necesitan una abstracción que represente lo que hará el servicio y lo que devolverá. La implementación proporcionará todos los detalles de bajo nivel sobre cómo debe hacerlo. Lo mismo ocurre con otros objetos de servicio que se comunicarán con algún servicio fuera de la aplicación. Estos servicios también necesitarán una interfaz y una implementación concreta.

Los servicios para los que tenemos abstracciones, de acuerdo con la lista anterior, deben inyectarse como dependencias abstractas. Si hacemos esto, podemos formar tres grupos útiles, o capas, de objetos:

🛠 The infrastructure layer:

– Controllers

– Write and read model repository implementations

📜 The application layer:

– Application services

– Command objects

– Read models

– Read model repository interfaces

– Event listeners

💜 The domain layer:

– Entities

– Value objects

– Write model repository interfaces

- Domain Events

Teniendo en cuenta que la capa de infraestructura contiene el código que facilita la comunicación con el mundo exterior, se puede dibujar como una capa alrededor de la aplicación y el dominio (ver figura debajo). Asimismo, la aplicación utiliza código de la capa de dominio para realizar sus tareas, por lo que la capa de dominio será la capa más interna de una aplicación.
Para mostrar el uso de capas en su código, puede hacer que los nombres de las capas formen parte de los espacios de nombres de sus clases. Los ejemplos de código mostrados anteriorment también utilizan esta convención.
Al inyectar dependencias abstractas, podemos asegurarnos de que los objetos solo dependan en una dirección: de arriba a abajo. Por ejemplo, un servicio de aplicación que necesita un repositorio de modelos de escritura dependerá de la interfaz de ese repositorio, no de su implementación concreta. Esto tiene dos ventajas principales.

➡️ Primero, podemos probar el código del servicio de la aplicación sin una implementación real del repositorio que necesitaría algún tipo de base de datos que esté en funcionamiento, con el esquema correcto, etc. Tenemos interfaces para todos estos servicios y podemos crear fácilmente test doubles (fakes, mocks, etc) para ellos.

➡️ En segundo lugar, podemos cambiar fácilmente las implementaciones de infraestructura. Nuestra capa de aplicación sobreviviría a un cambio entre frameworks (o una actualización a la próxima versión principal del mismo), y también sobreviviría a un cambio de bases de datos (cuando se dé cuenta de que está mejor con una base de datos gráfica que con una base de datos relacional, por ejemplo) y eliminar servicios (cuando ya no desee obtener tipos de cambio de un servicio externo, sino de su propia base de datos local).

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/104323122-8f557e00-54c4-11eb-9a11-b3b10e90398b.png">
</p>

# Resumen

🔺 El front controller de una aplicación enviará una solicitud entrante a uno de sus controladores. Estos controladores son parte de la capa de infraestructura de la aplicación y saben cómo traducir los datos entrantes en una llamada a un servicio de aplicación o un repositorio de modelos de lectura, que son parte de la capa de la aplicación.

🔺 Un servicio de aplicación será independiente del mecanismo de entrega (HTTP for example) y se puede utilizar con la misma facilidad en aplicaciones web o de consola. Realiza una sola tarea que podría considerarse uno de los casos de uso de la aplicación. En el camino, puede tomar una entidad de un repositorio de modelos de escritura, llamar a un método en ella y guardar su estado modificado. La propia entidad, incluidos sus objetos de valor, son parte del
capa de dominio.

🔺 Un repositorio de modelos de lectura es un servicio que se puede utilizar para recuperar información. Devuelve modelos de lectura que son específicos de un caso de uso y que brindan toda la información necesaria y nada más.

🔺 Los tipos de objetos descritos anteriormente pertenecen naturalmente a capas. Un sistema de capas en el que el código solo depende del código de las capas inferiores ofrece una forma de desacoplar el código de dominio y de aplicación de los aspectos de infraestructura de su aplicación.
