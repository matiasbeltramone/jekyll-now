---
layout: post
title: "Programación Orientada a Objetos: Patrones de Diseño"
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming, Design Patterns]
---
 
 ### ¿Qué es un Patron de Diseño?
 
 Son soluciones tipicas a problemas que ocurren comunmente en el diseño de software. Son como esquemas, estructuras bases,
 que podes personalizar para resolver un problema recurrente del codigo.
 
 No podes buscar un patron y copiarlo directamente dentro de tu programa, de la misma manera que hacemos con librerías de terceros.
 El patron no es una pieza de codigo especifica que simplemente se copia y se pega, como puede ser un algoritmo, es un concepto general para resolver un problema particular.
 Podes seguir los detalles del patron e implementar una solución que se adapte a la realidad de tu programa.
 
 Los patrones son a menudo confundidos con algoritmos, porque ambos conceptos describen soluciones tipicas a algunos problemas conocidos.
 Mientras que un algoritmo siempre define unas acciones claras que debes hacer para llegar a un objetivo en particular,
 un patron es más una descripción de alto nivel de una solución particular.
 El codigo del mismo patron aplicado a dos programas diferentes, puede y seguramente será diferente en su implementación.
 
 Una analogía de un algoritmo es una receta de cocina: ambos tienen claramente pasos para llegar a un objetivo. Por otro lado, un patron es más como un plano: tu pudes ver cual es el resultado y las caracteristicas que tiene, pero la implementación exacta para llegar a esto depende de nosotros.
 
 ### ¿En que consiste un patron?
 
 La mayoría de los patrones son descritos muy formalmente entonces la gente puede reproducirlos en muchos contextos.
 Aca dejamos las secciones que estan generalmente descritas en la descripción de un patron:
 
• La **intención** de un patron describe brevemente tanto el problema que ataca junto a su solución.

• La **motivación** explica aún más los detalles del problema y la solución que el patrón nos hace posible.

• La **estructura** de las clases muestra cada parte del patrón y cómo están relacionados los elementos que forman la solución del mismo.

• **Ejemplo de código** en uno de los lenguajes de programación populares hace que sea más fácil comprender la idea detrás del patrón.

Algunos catálogos de patrones enumeran otros detalles útiles, como aplicacion del patrón, pasos de implementación y relaciones con otros patrones.

### Clasificación de patrones

Los patrones de diseño son diferentes por su complejidad, nivel de detalle y aplicabilidad a escala a todo el sistema que esta siendo diseñado.
Me gusta la analogía con la construcción de carreteras: puedes hacer una intersección segura instalando algunos semaforos o construyendo un intercambio multinivel con pasajes subterranos para peatones.
Los patrones más basicos y de bajo nivel son a menudo llamado `idioms`
Por lo general, se aplican solo a un solo lenguaje de programación.

Los patrones más universales y de alto nivel son patrones arquitectónicos. Los desarrolladores pueden implementar estos patrones en cualquier lenguaje. A diferencia de otros patrones, pueden usarse para
diseñar la arquitectura de una aplicación completa.

Además, todos los patrones se pueden clasificar por su intención, o propósito.

Cubriremos tres grupos principales de patrones:

• Los **patrones de creación** proporcionan mecanismos de creación de objetos que aumentan la flexibilidad y la reutilización del código existente.

• Los **patrones estructurales** explican cómo ensamblar objetos y clases en estructuras más grandes, manteniendo las estructuras flexibles y eficientes.

• Los **patrones de comportamiento** se encargan de una comunicación efectiva y la asignación de responsabilidades entre objetos.

### ¿Quien invento los patrones?
La verdad que es una buena pregunta, pero no muy precisa. Los patrones no son oscuros o conceptos sofisticados, sino todo lo contrario.
Son tipicas soluciones a problemas comunes en el diseño orientado a objetos.
Cuando una solución toma repetición en otros proyectos continuamente, alguien eventualmente le coloca un nombre a esto y 
describe la solución planteada para ese caso con detalles de lo realizado. Esto es basicamente como un patron es descubierto.

El concepto de patrones fue descrito por primera vez por Christopher Alexander en el libro `A Pattern Lenguage: Towns, Buildings, Construction`. El libro describe un "lenguaje" para diseñar un ambiente urbano.
Las unidades de este lenguaje son patrones. Ellos pueden describir qué tan altas deben ser las ventanas, cuántos pisos debería tener un edificio, que tan grandes deben ser las áreas verdes en la vecindad, y así sucesivamente.

La idea fue recogida por cuatro autores: `Erich Gamma, John Vlissides, Ralph Johnson y Richard Helm`. En 1995, publicaron `Design Patterns: Elements of Reusable Object-Oriented
Software`, en el que aplicaron el concepto de patrones de diseño a la programación. El libro presentó 23 patrones resolviendo
varios problemas de diseño orientado a objetos y se convirtió en el libro más vendido rapidamente.
Debido a su largo nombre, la gente comenzó a llámarlo "La pandilla de los cuatro" que pronto fue simplemente "The GOF (Gang of Four) book".

Desde entonces, docenas de otros patrones orientados a objetos han sido descubierto. El "Enfoque de patrónes" se hizo muy popular en
otros campos de programación, por lo que ahora existen muchos otros patrones fuera del diseño orientado a objetos también.

### ¿Porque debería aprender patrones?

La verdad es que podrías trabajar como programador por muchos años sin tener conocimiento sobre ni un patrón. Mucha gente solo hace eso. Aun en el caso, podemos pensar, que podríamos implementar algunos patrones sin tener conocimiento de ello. Entonces ¿Porque perdería tiempo estudiandolos?

Los patrones de diseño son una caja de herramientas de soluciones probadas para problemas comunes en el diseño de software. Aun si nunca te encontraste con estos problemas, saber sobre patrones sigue siendo util porque esto te enseña como resolver todo tipo de problemas usando principios del diseño orientado a objetos.

Los patrones de diseño definen un lenguaje comun que tu y tu equipo pueden utilizar para comunicarse mas eficientemente. Podras decir, "Oh, solo usa un singleton para resolver eso", y todo el mundo entendería la idea detras de tu sugerencia. No necesitas explicar que es un singleton si tu sabes el patron y su nombre.

Esta es una pequeña introducción teoría a los patrones de diseño tan normales de uso hoy en día.

