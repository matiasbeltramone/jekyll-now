---
layout: post
title: "Programación Orientada a Objetos: Principios de Diseño de Software"
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming, Software Design Principles]
---

### Caracteristicas del buen diseño de software

Más adelante veremos los patrones más actuales, diferentes conceptos de diseños de arquitectura, pero antes vamos a discutir
el proceso de la **arquitectura del diseño de software** es decir: cosas a las que debemos apuntar y cosas que sería mejor evitar.

#### Reutilización de código

**Costo y tiempo** son dos de las cosas de más valor cuando hablamos de métricas en el desarrollo de cualquier producto de software.

 - Menos tiempo de desarrollo significa entrar más temprano al mercado que los competidores.

 - Bajo costo de desarrollo significa más dinero para marketing y un alcance más alto de potenciales clientes.

La **reutilización de codigo** es uno de los caminos más comunes para reducir los costos de desarrollo. La intención es obvia: en lugar de desarrollar algo una y otra vez desde el inicio. ¿Porque no reutilizamos codigo existente en nuevos proyectos?

La idea suena genial en papel, pero se torna algo más complicado en la realidad, ya que pasar el codigo existente en un proyecto y hacer que trabaje en un nuevo contexto de otro proyecto es bastante difícil en muchas ocasiones. El **alto acoplamiento** entre componentes, teniendo dependencias de clases concretas en lugar de **interfaces o abstracciones**, `hardcoded operations`, todo esto en conjunto genera una reducción de la **flexibilidad** de cambios, es decir, llevarlo a otro sistema, haciendo que el código sea difícil de reutilizar.

Utilizar **patrones de diseño** es un camino para incrementar la **flexibilidad** de los componentes de software y hacer de ellos más fácil de **reutilizar**. Sin embargo, esto **genera** algunas veces **un precio** de hacer componentes más complicados de entender a la primera, y de programarlos ya que conlleva un mayor nivel de conocimientos, siendo que el leer el código no sea tan sencillo quizás que tener todo más junto, ya que debemos navegar mediante las interfaces y luego ir a sus colaboradores, implementaciones, etc, generando una cierta fricción al menos a simple vista.

Hay una pisca de conocimiento de `Erich Gamma`, uno de los fundadores padres de los patrones de diseño, sobre el rol de los patrones de diseño en la reutilización de codigo:

#### Veo tres niveles de reutilización

 - En el **nivel más bajo**, reutilizas **clases**: `Class libraries, containers`, y quizás algunas `class teams` como container/iterator.

 - En el **nivel más alto**, los **frameworks**: Ellos realmente intentan destilar tus decisiones de diseño. Ellos identifican las abstracciones claves para resolver un problema, representados por clases y definiendo relaciones entre ellas. JUnit es un framework chico, por ejemplo. Es como el "Hello, World" de los frameworks. Tiene una clase `Test`, `TestCase`, `TestSuite` y relaciones ya definidas.

Un framework suele ser de tamaño más grande que una sola clase.
Usan el llamado principio de Hollywood de "no nos llames, lo llamaremos".
El framework le permite definir su comportamiento, y te llamará cuando sea tu turno de hacer algo.
Lo mismo con JUnit, ¿verdad? Te llamara cuando quiera ejecutar una prueba para ti, pero el resto sucede en el framework.

 - También hay un **nivel medio**. Aquí es donde veo **patrones**.
Los patrones de diseño son más pequeños y más abstractos que los frameworks. Realmente son una descripción de cómo un par de clases pueden relacionarse e interactuar entre sí. El nivel de la reutilización aumenta cuando pasas de clases a patrones y finalmente a frameworks.

Lo bueno de esta **capa intermedia** es que los **patrones** ofrecen reutilizar de una manera que sea **menos riesgosa** que los **frameworks**. Construir un **framework es de alto riesgo** y una inversión significativa. **Los patrones
te permiten reutilizar ideas y conceptos de diseño independientemente del código concreto**.

#### Extensibilidad

**Los cambios son la única constante en la vida de un programador**.

 - Si sacas una versión de video juego para **Windows** y se vuelve exitoso... ahora la gente preguntará por una versión para **macOS**.

 - Si creas una GUI framework por ejemplo con botones cuadrados, muchos meses después los botones redondos se convierten en tendencia y debes extender tu framework para obtener esta capacidad.

 - Si diseñas una brillante arquitectura de un sitio web del estilo e-commerce, solo un mes después es probable que los clientes te preguntan por una característica nueva donde aceptan ordenes por telefono, practicamente siempre casi rompiendo un poco con todo tu modelado y pensamiento de arquitectura inicial.

Cada desarrollador de software tiene decenas de historias similares. **Existen muchas razones del porque esto sucede...**

Primero, entendemos el problema mucho mejor una vez que comenzamos a resolverlo. A menudo en el momento que finalizas la primer versión de la aplicación, es donde estas listo para reescribirlo al proyecto desde el inicio de nuevo porque ahora entendes aspectos de los problemas mucho mejor, es decir, tenes **mayor conocimiento del modelo de negocio**.

Además tienes también un nuevo **crecimiento profesional**, y ahora tu propio codigo luce como una porquería... Sisi estoy seguro que lo que creías perfecto en Marzo, luego en Julio cuando pasas de nuevo por ese "servicio o lo que sea" dices que porquería es esto, vamos... ¿A quien no le ha pasado?

**Algo más allá de tu control ha cambiado**. Por eso es así que muchos equipos de desarrollo pasan de sus ideas originales a algo
nuevo. Todos los que confiaron en flash por ejemplo para una aplicación en línea han estado recodificando o migrando su código después de que el navegador dejara de admitir flash definitivamente.

La tercera razón es que los postes de la portería se mueven, es decir, **siempre hay cambios nuevos**, no nos dejemos engañar, **SIEMPRE** hay cambios, nuestro mundo tiende al caos, y la mente de nuestros clientes no escapan a ellos, somos humanos, siempre buscamos algo nuevo, una mejora, ideas nuevas, etc.

Si bien su cliente esta encantado con la versión actual de la aplicación, llega un día en el que viene la típica frase, esta todo andando muy bien pero se me ocurrieron unas mejoras o cambios, ahora nuestro cliente ve 11 "pequeños cambios" que le gustaría realizar para que pueda hacer otras cosas que nunca mencionó en las sesiones de planificación originales. Estos para nuestra mala suerte no son cambios "frívolos" (es decir, medios tontos, insignificantes como dicen ellos que son muchas veces): Pero bueno después de todo su excelente primera versión hace pensar que es posible realizarlos asique no es tan malo después de todo ¿O si lo es?...

Sin embargo si lo pensamos dos veces... Hay un **lado positivo**: si alguien te pide que cambies algo en tu aplicación, eso significa que a alguien todavía le importa esa aplicación y esta progresando, lo cuál nos generará esta sensación de haber realizado algo útil para nuestro cliente, que esta creciendo, y que tenemos la posibilidad de hacer que crezca más aportando nuestro gran valor de parte de la técnología para su negocio.

Es por eso que todos los **desarrolladores experimentados** `intentan` proporcionar soluciones para posibles
cambios futuros al diseñar la arquitectura de una aplicación de una manera lo más flexible posible sin que esto entorpesca o relentice más de lo necesario el desarrollo de la aplicación.
