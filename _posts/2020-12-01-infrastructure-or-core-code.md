***Infrastructure or core code?***

Dentro de las arquitecturas limpias como Hexagonal o Ports and Adapters es importante hacer una distinción clara entre el **código de negocio** (core code) de su aplicación y el **código de infraestructura** que lo soporta. Este llamado código de infraestructura conecta la lógica de negocio de su aplicación con los sistemas que la rodean, como la base de datos, el servidor web, el sistema de archivos, etc. **Ambos tipos de código son igualmente importantes**, pero no deben convivir en las mismas clases. El resumen rápido de las razones para hacerlo es que separar el negocio de la infraestructura...

• proporciona una base técnica sólida para realizar domain-first development, y

• permite un conjunto rico y eficaz de posibilidades para pruebas, lo que facilita el test-first development.

Establezcamos primero la diferencia entre los términos **"core code" e "infrastructure code"**. Definiremos a "core code" introduciendo dos reglas para ello. Cualquier otro código que no sigue estas reglas, debe ser considerado **"infrastructure code"**.

**No dependencies on external systems**

```
Core code doesn’t directly depend on external systems, nor does
it depend on code written for interacting with a specific type of
external system.
```

**No depender de sistemas externos**

```
El código de negocio no depende directamente de sistemas externos, ni depende
de código escrito para interaccionar con un tipo especifico de sistema externo.
```

Un **sistema externo** es algo que de una manera u otra vive afuera de tu aplicación, como la base de datos, web service remoto, el reloj del sistema, el sistema de archivos, etc. El código de negocio debería ejecutarse sin estas dependencias externas.
Ejemplos de código que no sigue la primer regla establecida, y por lo tanto debe ser considerado "infrastructure code". No podemos llamar a ninguno de
estos métodos sin que sus dependencias externas estén realmente disponibles.

```

final class NeedsExternalDependencies
{
    public function callARemoteService(): void
    {
        /*
        * To run this code, we need an internet connection,
        * and the API of "remoteservice.com" should be responsive.
        */
        $ch = curl_init('https://remoteservice.com/api');
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        $response = curl_exec($ch);
        
        // ...
    }
    
    public function useTheDatabase(): void
    {
        /*
        * To run this code, the database that we connect to
        * using `new PDO('...')` should be up and running, and
        * it should contain a table called `orders`.
        */
        $pdo = new PDO('...');
        
        $statement = $pdo->prepare('INSERT INTO orders ...');
        
        $statement->execute();
    }
    
    public function loadAFile(): string
    {
        /*
        * To run this code, the `settings.xml` file should exist
        * in the correct location.
        */
        return file_get_contents(__DIR__ . '/../app/config/settings.xml');
    }
}

```


Cuando el código sigue la **primera regla**, significa que podemos ejecutarlo de manera completamente **independiente** de estos servicios externos. La **independencia** es buena para la testeabilidad. Cuando queremos escribir un test para código de negocio, debería ser muy facil de hacerlo.
No tenemos porque necesitar configurar una base de datos, saber que existen ciertas tablas, o tener que configurar ciertos servicios adicionales, o conexiones previas. No necesitará una **conexión a internet** ni un
**archivo disponible en disco** en ubicaciones específicas. **Todo lo que necesita es poder ejecutar el código y tener algo de memoria disponible.**

¿Qué pasa con el siguiente caso de uso? ¿Es también "infrastructure code"?:

```

interface Connection
{
    public function insert(string $table, array $data): void;
}

final class UserRegistration
{
    private Connection $connection;
    
    public function __construct(Connection $connection)
    {
        $this->connection = $connection;
    }
    
    public function registerUser(string $username, string $plainTextPassword): void
    {
        $this->connection->insert(
            'users',
            [
                'username' => $username,
                'password' => $plainTextPassword
            ]
        );
    }
}

```

El método **registerUser()** no usa PDO directamente para conectarse a una base de datos y comenzar a ejecutar consultas en ella. En su lugar, utiliza una abstracción para las conexiones de la base de datos (utiliza la interfaz "Connection"). Esto significa que el objeto **Connection** que se inyecta como un argumento de constructor podría ser reemplazado por una implementación más simple de esa misma interfaz que en realidad no necesita de una base de datos.

```

final class ConnectionDummy implements Connection
{
    /**
    * @var array<array<string,mixed>>
    */
    private array $records;
    
    /**
    * @param array<string,mixed> $data
    */
    public function insert(string $table, array $data): void
    {
        $this->records[$table][] = $data;
    }
}

```

Esto hace posible ejecutar el código en ese método **registerUser()**, sin la necesidad de que la **base de datos real** esté en funcionamiento. ¿Eso hace que este código sea código de negocio o "core code"? **No**, porque la interfaz Connection está diseñada específicamente para comunicarse con bases de datos relacionales, como lo revela la propia firma del método **insert()**. Entonces, aunque el método **registerUser()** no depende directamente de un sistema externo, sí depende del código escrito para interactuar con un tipo específico de sistema externo (base de datos). Esto significa que el código anterior no aplica para código de negocio, sino que es código de infraestructura.

Sin embargo, en general, la **abstracción es la solución ideal para deshacerse de las dependencias de los sistemas externos**. Pero la creación de una abstracción completa para los servicios que dependen de sistemas externos consta de dos pasos:

1. Introducir una interfaz
2. Comunicar el **propósito** en lugar de los detalles de implementación. (RoleInterface sobre HeaderInterfaces)

Ejemplo: en lugar de una interfaz de conexión y un método **insert()**, que solo tiene sentido en el contexto de tratar con bases de datos relacionales, podríamos definir una interfaz de repositorio (Repository Pattern), con un método **save()** en su lugar. Esta interfaz comunica el **propósito** (guardar objetos, es decir, algo genérico sin saber donde guardarlo realmente...) en lugar de los detalles de **implementación** (almacenar datos en tablas).

**No special context needed**

```
Core code doesn’t need a specific environment to run in, nor does
it have dependencies that are designed to run in a specific context
only.
```

**No tener necesidad de contextos específicos**
```
El código de negocio no necesita tener un ambiente específico para poder funcionar, ni tener dependencias que fueron diseñadas para funcionar solamente en un contexto específico.
```

Veamos ejemplos de código que requieren un contexto especial antes de ejecutarse. Suponen que se han configurado ciertas cosas o que se ejecuta
dentro de un tipo específico de aplicación, como una aplicación web o de línea de comandos (CLI).

```
final class RequiresASpecialContext
{
    public function usesGlobalState(): void
    {
        /*
        * Here we rely on global state, and we assume this
        * method gets executed as part of an HTTP request (Web Application environment).
        */
        $host = $_SERVER['HTTP_HOST'];
        
        //...
    }
    
    public function usesAStaticServiceLocator(): void
    {
        /*
        * Here we rely on `LaravelAppRegistry` to have been
        * configured before calling this method.
        */
        $translator = LaravelAppRegistry::get('Translator');
        
        // ...
    }

    public function onlyWorksAtTheCommandLine(): void
    {
        /*
        * Here we rely on `php_sapi_name()` to return a specific
        * value. Only when this application has been started from
        * the command line will this function return 'cli'.
        */
        if (php_sapi_name() !== 'cli') {
            return;
        }
        
        // ...
    }
}
```

En teoría, cierta parte del código podría ejecutarse en cualquier entorno, pero en la práctica resultará complicado hacerlo. Considere el siguiente snipped de código.

```

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;

final class OrderController
{
    public function createOrderAction(RequestInterface $request): ResponseInterface
    {
        // ...
    }
}

```

Se puede crear una instancia de OrderController en cualquier contexto, y sería relativamente fácil llamar al método createOrderAction() y pasarle una instancia de RequestInterface. Sin embargo, está claro que este **código ha sido diseñado para ejecutarse únicamente en un entorno muy específico**, es decir, **una aplicación web**.

Solo si el **código de la clase no requiere ejecutarse en un contexto especial, y sus dependencias tampoco han sido diseñadas para ejecutarse en un contexto especial** para las cuales este es el caso, puede considerarse código de negocio o "core code".

Ejemplos de "core code". Estas clases se pueden instanciar en cualquier lugar y cualquier cliente debería poder llamar a cualquiera de los métodos disponibles. Ninguno de estos métodos depende de nada fuera de la propia aplicación.

```

/*
* This is a proper abstraction for an object that talks to the database:
*/
interface MemberRepository
{
    public function save(Member $member): void;
}

final class MemberService
{
    private MemberRepository $memberRepository;
    
    public function requestAccess(
        string $emailAddress,
        string $purchaseId
    ): void {
        $member = Member::requestAccess(
            EmailAddress::fromString($emailAddress),
            PurchaseId::fromString($purchaseId)
        );
        
        $this->memberRepository->save($member);
    }
}

final class EmailAddress
{
    private string $emailAddress;
    
    private function __construct(string $emailAddress)
    {
        if (!filter_var($emailAddress, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException('...');
        }
        
        $this->emailAddress = $emailAddress;
    }
    
    public static function fromString(string $emailAddress): self
    {
        return new self($emailAddress);
    }
}

final class Member
{
    public static function requestAccess(
        EmailAddress $emailAddress,
        PurchaseId $purchaseId
    ): Member {
        // ...
    }
}

```

No tener que crear un contexto especial para que el código se ejecute es, nuevamente, excelente para la testeabilidad del código. Lo único que tenemos que hacer en un escenario de prueba es crear una instancia de la clase y llamar a un método en ella. Pero seguir las reglas del código de negocio no solo es excelente para realizar pruebas. También ayuda a mantener su lógica de negocio protegida contra todo tipo de cambios externos, como una actualización importante del framework de turno, un cambio a un proveedor de base de datos diferente, cambio en alguna librería en particular, etc.

No es una coincidencia que las clases de este ejemplo estén orientadas al dominio. Ya que esto viene alineado con las capas arquitectónicas de clean architectures (Hexagonal Architecture) donde definimos reglas para las capas de **Dominio y Aplicación** que, naturalmente, se alinean con las reglas para el **"core code"** y de **infraestructure code**. En resumen: todo el código de dominio y los casos de uso de la aplicación deben ser "core code" y no depender de la infraestructura circundante ni estar acoplada a ella.

Esto también explica por qué estoy usando las palabras **"núcleo" (core) e "infraestructura" (infrastructure)**. La infraestructura es un término común que se usa para describir los aspectos técnicos de una interacción. En una aplicación web, la infraestructura admite la comunicación entre su aplicación y el mundo exterior. El núcleo es el centro de su aplicación, la infraestructura está a su alrededor, protegiendo el núcleo y conectándolo a sistemas y usuarios externos.

<hr>
<p align="center">
  <img width="50%" src="https://user-images.githubusercontent.com/22304957/99879944-0f5f1600-2bef-11eb-8ea6-cc01d4270638.png">
</p>
<p align="center">Conectamos el núcleo a los usuarios y sistemas externos mediante la infraestructura.</p>

**Conclusión:**

El código de negocio es código que se puede ejecutar en cualquier contexto, sin ninguna configuración especial, o sistemas externos que deben estar disponibles. Para el código de infraestructura ocurre justamente lo contrario: necesita sistemas externos, una configuración especial o está diseñado para ejecutarse solo en un contexto específico.
