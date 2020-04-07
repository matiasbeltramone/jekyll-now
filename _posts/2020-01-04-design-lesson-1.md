---
layout: post
title: "Programación Orientada a Objetos: Patrones de Diseño"
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming, Design Patterns]
---

Anteriormente hablabamos que en la simetría que conocemos y encontramos dentro de nuestro mundo, se esconden en nuestro día a día muchos patrones que la inteligencia humana tiene la capacidad de reconocer continuamente, algo parecido a nuestra realidad se encuentra dentro de nuestro Software ¿Cómo es esto así? bueno basicamente podemos resumirlo de la siguiente manera:

### ¿Qué es un Patrón de Diseño?

Son soluciones tipicas a problemas que ocurren comunmente en el diseño de software. Son esquemas, estructuras bases,
que podes personalizar para resolver un problema recurrente del código.

No podes buscar un patrón y copiarlo directamente dentro de tu programa, de la misma manera que hacemos con librerías de terceros.
El patrón no es una pieza de código especifica que simplemente se copia y se pega, como puede ser un algoritmo, es un concepto general para resolver un problema particular.
Solamente podes seguir los detalles, la idea del patrón e implementar una solución que se adapte a la realidad de tu programa.

Los patrones son a menudo confundidos con algoritmos, porque ambos conceptos describen soluciones tipicas a algunos problemas conocidos.
Mientras que un algoritmo siempre define unas acciones claras que debes hacer para llegar a un objetivo en particular, un patrón es más una descripción de alto nivel de una solución particular.
El código del mismo patrón aplicado a dos programas diferentes, puede y seguramente será diferente en su implementación.

Una analogía de un algoritmo es una receta de cocina: Donde claramente existen pasos para llegar a un objetivo (hacer cierta receta). Por otro lado, un patrón es más como un plano: tu puedes ver cuál es el resultado y las características que tiene, pero la implementación exacta para llegar a esto depende de nosotros.
 
### ¿En que consiste un patron?
 
La mayoría de los patrones son descritos muy formalmente entonces la gente puede reproducirlos en muchos contextos.
Aca dejamos las secciones que estan generalmente descritas en la descripción de un patron:
 
• La **intención** de un patrón describe brevemente tanto el problema que ataca cómo su solución.

• La **motivación** explica aún más los detalles del problema y la solución que el patrón nos hace posible.

• La **estructura** de las clases muestra cada parte del patrón y cómo están relacionados los elementos que forman la solución del mismo.

• **Ejemplo de código** en uno de los lenguajes de programación populares hace que sea más fácil comprender la idea detrás del patrón.

Algunos catálogos de patrones enumeran otros detalles útiles, como aplicacion del patrón, pasos de implementación y relaciones con otros patrones.

### Clasificación de patrones

Los patrones de diseño son diferentes por su complejidad, nivel de detalle y aplicabilidad a escala a todo el sistema que esta siendo diseñado.
Me gusta la analogía con la construcción de rutas: puedes hacer una intersección segura instalando algunos semaforos o construyendo una estructura multinivel con pasajes subterranos para peatones o algún puente que pase por arriba de las rutas.
Los patrones más básicos y de bajo nivel son a menudo llamado `idioms`
Por lo general, se aplican solo a un solo lenguaje de programación.

Los patrones más universales y de alto nivel son patrones arquitectónicos. Los desarrolladores pueden implementar estos patrones en cualquier lenguaje. A diferencia de otros patrones, pueden usarse para
diseñar la arquitectura de una aplicación completa.

Además, todos los patrones de diseño de clases se pueden clasificar por su intención, o propósito.

Cubriremos tres grupos principales de patrones:

• Los **patrones de creación** proporcionan mecanismos de creación de objetos que aumentan la flexibilidad y la reutilización del código existente.

• Los **patrones estructurales** explican cómo ensamblar objetos y clases en estructuras más grandes, manteniendo las estructuras flexibles y eficientes.

• Los **patrones de comportamiento** se encargan de una comunicación efectiva y la asignación de responsabilidades entre objetos.

### ¿Quien invento los patrones?

La verdad que es una buena pregunta, pero no muy precisa. Los patrones no son cosas oscuras o conceptos sofisticados, sino todo lo contrario.
Son tipicas soluciones a problemas comunes en el diseño orientado a objetos.
Cuando una solución toma repetición en otros proyectos continuamente, alguien eventualmente le coloca un nombre a esto y 
describe la solución planteada para ese caso con detalles de lo realizado. Esto es basicamente como un patrón es descubierto.

El concepto de patrones fue descrito por primera vez por Christopher Alexander en el libro `A Pattern Lenguage: Towns, Buildings, Construction`. El libro describe un "lenguaje" para diseñar un ambiente urbano.
Las unidades de este lenguaje son patrones. Ellos pueden describir qué tan altas deben ser las ventanas, cuántos pisos debería tener un edificio, que tan grandes deben ser las áreas verdes en la vecindad, y así sucesivamente.

La idea fue recogida por cuatro autores: `Erich Gamma, John Vlissides, Ralph Johnson y Richard Helm`. En 1995, publicaron `Design Patterns: Elements of Reusable Object-Oriented
Software`, en el que aplicaron el concepto de patrones de diseño a la programación. El libro presentó 23 patrones resolviendo
varios problemas de diseño orientado a objetos y se convirtió en el libro más vendido rapidamente.
Debido a su largo nombre, la gente comenzó a llámarlo "La pandilla de los cuatro" que pronto fue simplemente "The GOF (Gang of Four) book".

Desde entonces, docenas de otros patrones orientados a objetos han sido descubierto. El "Enfoque de patrónes" se hizo muy popular en otros campos de programación, por lo que ahora existen muchos otros patrones fuera del diseño orientado a objetos también.

### ¿Porque debería aprender patrones?

La verdad es que podrías trabajar como programador por muchos años sin tener conocimiento sobre ni un patrón. Mucha gente solo se limita a hacer eso. Aún en ciertos casos, podemos pensar, que podríamos implementar algunos patrones sin tener conocimiento de ello al menos con nombre y a sabiendas de esto. Entonces ¿Porque perdería tiempo estudiandolos? Bueno...

Los patrones de diseño son una caja de herramientas de soluciones probadas para problemas comunes en el diseño de software. Aún si nunca te encontraste con estos problemas, saber sobre patrones sigue siendo útil porque esto te enseña como resolver todo tipo de problemas usando principios del diseño orientado a objetos.

Los patrones de diseño definen un lenguaje común que tu y tú equipo pueden utilizar para comunicarse mas eficientemente. Podras decir, "Oh, solo usa el patrón `strategy` para resolver eso", y todo el mundo entendería la idea detras de tu sugerencia. No necesitas explicar que es un patrón `strategy` si tu sabes el patrón y su nombre.

Esta es una pequeña introducción teoríca a los patrones de diseño tan nombrados hoy en día en nuestro ambiente en diferentes niveles de seniorities, lenguajes, paradigmas, niveles de abstracción, etc.
