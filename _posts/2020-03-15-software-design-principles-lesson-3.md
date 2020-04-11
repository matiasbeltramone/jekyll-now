---
layout: post
title: "POO Lección 8: Principios de Diseño de Software II"
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming, Software Design Principles]
---

¿Qué es el buen diseño de software? ¿Cómo podemos medirlo? ¿Qué prácticas necesitaras seguir para llegar? ¿Como podes hacer tu arquitectura flexible, estable y fácil de entender?

Estas son las grandes preguntas; pero, afortunadamente, las respuestas son diferentes dependiendo del tipo de aplicación que querramos construir. Sin embargo, hay muchos principios universales del diseño de software que podrán ayudarte a contestar estas preguntas para tu propio proyecto. La mayoría de los patrones de diseño que podamos encontrar son basados en estos principios.

### Principio 1: :package: Encapsulación

<hr>
Identificar los aspectos de tu aplicación que varían y separarlos de los que permanecen iguales.
<hr>

**El principal objetivo de este principio es minimizar el efecto causado por cambios.**

Imaginemos que nuestro programa es un barco, y los cambios son minas que permanecen abajo del agua. Si golpeamos una, el barco se hunde.

Conociendo esto, podemos dividir el casco del barco en compartimientos independientes que puedan ser sellados de forma segura con el objetivo de limitar el daño causado a un compartimiento individual.
Si golpearamos ahora una mina, es probable que el barco **como un todo** siga a flote gracias a este mecanismo, que hace que el daño se quede en una cosa parte del barco.

De la misma manera, en nuestro caso podemos **aislar las partes** de un **programa** que varía en **modulos independientes**, protegiendo el resto del código de efectos adversos que pueda ocasionar ese módulo en cuestión. Como resultado, gastas menos tiempo para que el programa vuelva a funcionar en caso de que falle por un módulo en particular, implementando y probando los cambios de manera más aislada, sin temer que se rompa otra parte. Cuanto menos tiempo pase haciendo cambios, o probando que un cambio en una feature no afecte al resto del programa, más tiempo tendrá para implementar funciones nuevas.

#### Encapsulación: :scroll: A nivel de metodos.

Digamos que estamos haciendo un sitio de e-commerce. En algún lugar de nuestro código, hay un método `getOrderTotal` que calcula el precio total de la orden, incluídos los impuestos, recargos, envíos, etc.

Nosotros podemos anticipar que el **código de los impuestos** relacionados **puede cambiar en el futuro**.
La cantidad de impuestos dependen del país, estado, o quizás de la ciudad donde el cliente reside, y la formula actual podría cambiar con el paso del tiempo por nuevas leyes o regulaciones, algo muy común en Argentina por ejemplo. Como resultado, vamos a necesitar cambiar el metodo `getOrderTotal` bastante seguido. Pero el nombre del método sugiere que no se preocupa acerca del `como` el recargo es calculado.

```
method getOrderTotal() is
  total = 0
  
  foreach item in this.lineItems
    total += item.price * item.quantity

  if (this.country == "US")
    total += total * 0.07 // US VAT
  else if (this.country == "EU"):
    total += total * 0.20 // European VAT
  
  return total
```

ANTES: El cálculo de los impuestos son calculados junto al resto del código del método.

Podemos extraer la lógica para calcular los impuestos en un método por separado, escondiendolo del método original.

```
method getOrderTotal() is
  total = 0
  
  foreach item in order.lineItems
    total += item.price * item.quantity

  total += total * getTaxRate(this.country)
  
  return total
 
method getTaxRate(country) is
  if (country == "US")
    return 0.07 // US VAT
  else if (country == "EU")
    return 0.20 // European VAT
  else
    return 0
```

DESPUÉS: Podes obtener el calculo de impuesto solamente llamando al método designado para calcularlos.

Los cambios relacionados con los impuestos quedan aislados dentro de un solo método.
Además, si la lógica del cálculo de impuestos se vuelve demasiado complicada, ahora es más fácil moverlo a una clase separada.

#### Encapsulación: :file_folder: A nivel de clases.

Con el tiempo, podemos agregar más y más responsabilidades a un método que solía hacer algo simple. Estos comportamientos adicionales a menudo vienen con sus propios campos auxiliares y métodos que eventualmente difuminan la responsabilidad principal de la clase que los contiene. Extraer todo a una nueva clase podría hacer las cosas mucho más claras y simples.

<p align="center">
  <img width="30%" src="https://user-images.githubusercontent.com/22304957/72668237-8346f580-3a03-11ea-8264-d063fd9f3621.png"/>
  
  Antes: Calculabamos los impuestos en la clase `Order`
</p>

Lo que podemos hacer ahora es que los objetos de la clase `Order` deleguen todos los calculos relacionados a impuestos a un objeto especial para realizar esa acción en particular `TaxCalculator`.

<p align="center">
  <img width="50%" src="https://user-images.githubusercontent.com/22304957/72668236-8346f580-3a03-11ea-9fa0-2bc178bee4f7.png"/>
</p>

```
method getOrderTotal() is
  total = 0
  
  foreach item in this.lineItems
    subtotal = item.price * item.quantity
    total += subtotal * taxCalculator.getTaxRate(country, state, item.product)
  
  return total
```

DESPUÉS: El calculo de impuestos esta oculto para la clase `Order` mediante el colaborador `TaxCalculator`

#### Principio 2: :clipboard: Programar una Interfaz no una Implementación. (Un contrato)

<hr>
Programar una interfaz, no una implementación. Depender de abstracciones, no de clases concretas.
<hr>

Podríamos decir que el diseño es suficientemente flexible si puedes extender facilmente sin romper el código existente. Vamos a asegurarnos que esto es correcto mirando otro ejemplo con gatos.

Un `Cat` que puede comer cualquier tipo de comida es más flexible que uno que solo puede comer pescado supongamos. Puedes alimientar al primer gato con pestaco porque esta en el marco de "cualquier comida"; sin embargo, también puedes extender el menú del gato con cualquier comida, por ejemplo: balanceado, en cambio el segundo gato que solo puede comer pescado no puede extender su menú.

Cuando quieres hacer que dos clases colaboren, solemos empezar haciendo una dependiente de la otra. Diablos! A menudo empiezo haciendo esto yo mismo. Sin embargo, hay otra forma más flexible para configurar una colaboración entre objetos:

1. Determinar exactamente que es lo que un objeto necesita del otro: ¿Qué métodos ejecutara?

2. Describa estos métodos en una nueva **interfaz**.

3. Hacer que la clase que es una dependencia, implementar esta interfaz recien creada para que cumpla con el contrato correctamente.

4. Ahora crear la segunda clase que depende de esta interfaz, y hacer que tome como colaborador esa interfaz. Puedes hacer que funcione también relacionando los objetos originales, pero la conexión mediante `interfaces` es mucho más flexible.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72668510-7081f000-3a06-11ea-99e7-bf7b676e1672.png"/>
</p>

Si observamos el **antes y después** de extraer la **interfaz**. El codigo en la **derecha** es **más flexible** que el codigo de la izquierda, pero también es verdad que es **algo más complicado de entender o realizar**.

Después de hacer este cambio, probablemente no sientas de inmediato ningún beneficio de realizar esto. Por el contrario, el código se convirtió en algo más complicado que antes de realizar y seguir el flujo que antes. Sin embargo, si sientes que esto podría ser un buen punto para agregar funcionalidad extra o para que otra gente use tu código y quiera extender la funcionalidad de una manera ms sencilla, sigamos este camino mediante `interfaces`.

#### Ejemplo

Miremos otro ejemplo que ilustre que los objetos a través de **interfaces** podrían resultar más beneficiosos que depender de **clases concretas**. Imaginemos que estamos creando un simulador de una compañia de desarrollo de software. Tenemos diferentes clases que representan varios tipos de empleados.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72668673-184bed80-3a08-11ea-9a99-e40c5b0e1bb7.png"/>
  
  Todas las clases estan altamente acopladas entre si.
</p>

En un principio, la clase `Company` esta altamente acoplada a las clases concretas de empleados. Sin embargo, apesar de la diferencia entre las implementaciones, podemos generalizar los métodos de trabajo relacionados y extraerlos en una interfaz común para todas las clases de empleados.

Después de hacer esto, nosotros podemos aplicar `polimorfismo` dentro de la clase `Company`, tratando varios objetos empleados a través de la interfaz `Employee`.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72668751-c22b7a00-3a08-11ea-8f4f-914a2fc95aed.png"/>
  
  El polimorfismo nos ayuda a simplificar el codigo, pero el resto de la clase `Company` sigue dependiendo de las clases `Employee` concretas.
</p>

La clase `Company` sigue acoplada a las clases `Employee`. Esto es malo porque si introducimos nuevos tipos de compañias que funcionan con otro tipos de empleados, nosotros necesitaremos sobreescribir la mayoría de la clase `Company` en lugar de reutiizar el código.

Para resolver este problema, podemos declarar el método para obtener los empleados como `abstracto`. Cada clase concreta `Company` implementara este método diferentemente, creando solamente los empleados que necesita.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72668827-880ea800-3a09-11ea-8405-8559b066fadc.png"/>
  
  El método primario de la clase `Company` es independiente de la clase concreta empleado. Los objetos `Employee` son creados en las subclases concretas de `Company`.
</p>

Después de este cambio, la clase `Company` se hace independiente de las diferentes clases de `Employee`. Ahora puedes extender esta clase e introducir nuevo tipos de compañías y empleados mientras seguimos reutilizando la porción base de la clase `Company`. Entendiendo la clase `Company` no rompemos cualquier código existente que ya se basa en ella.

Por cierto, acabas de ver la aplicación de un patrón de diseño en acción! Ese fue un ejemplo del patrón `Factory Method`.
No se preocupe: seguramente lo discutiremos más adelante en detalle, en alguna serie de patrones que generemos.

#### Principio 3: ✍️ Composición sobre Herencia.

La herencia es probablemente el camino más obvio y fácil de reutilización de código entre clases. Supongamos que tenes dos clases con el mismo código, procedes de la siguiente manera creas una clase común base para estas dos clases y moves el codigo similar allí, luego extendes de esa clase base y listo. Facilisimo!

Desafortunadamente, la herencia viene con advertencias que a menudo se hacen evidentes solo después de que su programa ya tiene toneladas de clases y cambiar algo se vuelve bastante difícil. Aquí hay una lista de esos problemas:

 - Una subclase no puede reducir la interfaz de la superclase. Tienen que implementar todos los métodos abstractos de la clase padre aún si no lo tienes que utilizar.

 - Cuando sobreescribimos métodos necesitamos estar seguros que el nuevo comportamiento es compatible con el de la clase base. Esto es importante porque los objetos de la subclase podrían ser pasados a un código que espera el objeto de la superclase y no queremos que el código se rompa.

 - La herencia rompe la encapsulación de la superclase porque los detalles internos de la clase padre están disponibles para la subclase. Puede haber una situación opuesta en la que un programador hace que una superclase conozca algunos detalles de las subclases en virtud de facilitar aún más la extensión, en ambos casos no es un camino correcto.

 - Las subclases estan altamente acopladas a las superclases. Cualquier cambio en una superclase podrá romper la funcionalidad de las subclases.

- Intentar reutilizar el código a través de la herencia puede conducir a crear jerarquías de herencia paralelas.
La herencia usualmente toma lugar en una sola dimensión.
Pero cada vez que hay dos o más dimensiones, tienes que crear muchas combinaciones de clases, hinchando la jerarquía de clases a un tamaño ridículo.

**Hay una alternativa a la herencia llamada composición.**

Mientras que la herencia representa la relación "es un" entre clases (un automóvil es un transporte) y siempre lo será, la composición representa el "tiene una" relación (un automóvil tiene un motor).

Debo mencionar que este principio también se aplica a los agregados, una variante de composición más relajada donde un objeto puede tener una referencia a la otra pero no gestiona su ciclo (lifecycle).

Aquí hay un ejemplo: un automóvil tiene un conductor, pero él o ella puede usar otro automóvil o simplemente caminar sin utilizar un automóvil.

#### Ejemplo

Imaginemos que necesitamos crear una app de catálogo para una empresa manufacturera de autos. La compañía hace autos y camiones; pueden ser electricos o a gas; todos los modelos tienen control manual o automatico.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72669268-5a782d80-3a0e-11ea-96f8-dbac3364c855.png"/>
  
  HERENCIA: Extendiendo una clase en muchas dimensiones podría derivar en una combinatoria explosión de subclases.
</p>

Como podemos ver, cada parámetro adicional resulta en multiplicar el número de subclases. Hay mucho código duplicado entre subclases porque una subclase no puede extender de dos clases al mismo tiempo, al menos no en la mayoría de los lenguajes actuales.

Podemos resolver este problema con composición. En lugar de un objeto `Auto o Car` implementando las funciones por si mismo, podemos delegarlo en otros objetos.

El beneficio agregado es que puedes reemplazar el comportamiento en tiempo de ejecución. Por ejemplo. Puedes reemplazar el objeto `Engine` linkeado al objeto `Car` solamente asignando un `Engine` diferente al objeto `Car`.

<p align="center">
  <img src="https://user-images.githubusercontent.com/22304957/72669269-5ba95a80-3a0e-11ea-98ef-87a06ce0cffc.png"/>
  
  COMPOSICIÓN: Diferentes "dimensiones" de funcionalidad extraidas a sus propias jerarquías de clases.
</p>

Esta estructura de clases se asemeja al patron `Strategy`, donde utilizamos diferentes implementaciones para llegar al mismo resultado.
