---
layout: post
title: "Programación Orientada a Objetos: Principios de Diseño de Software"
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming, Software Design Principles]
---

### Caracteristicas del buen diseño

Antes que prosigamos con los patrones actuales, vamos a discutir
el proceso de la arquitectura del diseño de software: cosas que debemos apuntar y cosas que mejor debemos evitar.

#### Reutilización de código

Costo y tiempo son dos de las cosas de más valor cuando hablamos de metricas en el desarrollo de cualquier producto de software. Menos tiempo de desarrollo significa entrar más temprano al mercado que los competidores.
Bajo costo de desarrollo significa más dinero para marketing y un alcance más alto de potenciales clientes.

La **reutilización de codigo** es uno de los caminos más comunes para reducir los costos de desarrollo. La intención es obvia: en lugar de desarrollar algo una y otra vez desde el inicio, ¿Porque no reutilizamos codigo existente en nuevos proyectos?

La idea suena genial en papel, pero se torna algo más complicado pasar el codigo existente en un proyecto y hacer que trabaje en un nuevo contexto de otro proyecto. El alto acoplamiento entre componentes, dependencias en clases concretas en lugar de interfaces, hardcoded operations, todo esto reduce la flexibilidad y hace que el codigo sea dificil de reutilizar.

Utilizar patrones de diseño es un camino para incrementar la flexibilidad de los componentes de software y hacer de ellos mas facil de reutilizar. Sin embargo, esto alguna veces tiene un precio de hacer componentes más complicados.

Hay una pisca de conocimiento de `Erich Gamma`, uno de los fundadores padres de los patrones de diseño, sobre el rol de los patrones de diseño en la reutilización de codigo:


```
Veo tres niveles de reutilización.

En el nivel más bajo, reutilizas clases: `class libraries, containers, y quizas algunas class "teams" como container/iterator.

Frameworks son a alto nivel. Ellos realmente intentan destilar tus decisiones de diseño. Ellos identifican las abstracciones claves para resolver un problema, representados por clases y definiendo relaciones entre ellas. JUnit es un framework chico, por ejemplo. Es como el "Hello, World" de los frameworks. Tiene una clase `Test`, `TestCase`, `TestSuite` y relaciones definidas.

Un framework suele ser de tamaño más grande que una sola clase.
Usan el llamado principio de Hollywood de "no nos llames, lo llamaremos".
El framework le permite definir su comportamiento, y te llamará cuando sea tu turno de hacer algo.
Lo mismo con JUnit, ¿verdad? Te llamara cuando quiera ejecutar una prueba para ti, pero el resto sucede en el framework.

También hay un nivel medio. Aquí es donde veo patrones.
Los patrones de diseño son más pequeños y más abstractos que los frameworks. Realmente son una descripción de cómo un par de clases pueden relacionarse e interactuar entre sí. El nivel de
la reutilización aumenta cuando pasas de clases a patrones y finalmente a frameworks.

Lo bueno de esta capa intermedia es que los patrones ofrecen
reutilizar de una manera que sea menos riesgosa que los frameworks. Construir un framework es de alto riesgo y una inversión significativa. Los patrones
te permiten reutilizar ideas y conceptos de diseño independientemente del código concreto.

```

#### Extensibilidad

Cambios es la unica constante en la vida de un programador.

- Si sacas una versión de video juego para Windows, ahora la gente pregunta por una version para macOS.

- Si creas una GUI framework con botones cuadrados, muchos meses despues los botones redondos se convierten en tendencia.

- Si diseñas una brillante arquitectura de un sitio web del estilo e-commerce, y solo un mes despues, los clientes preguntan por una caracteristica nueva donde aceptan ordenes por telefono.

Cada desarrollador de software tiene decenas de historias similares. Existen muchas razones del porque esto sucede.

Primero, entendemos el problema mucho mejor una vez que comenzamos a resolverlo. A menudo en el momento que finalizas la primer versión de la aplicación, es donde estas listo para reescribirlo al proyecto desde el inicio de nuevo porque ahora entendes muchos aspectos del problema mucho mejor.
Además tienes también un nuevo crecimiento profesional, y ahora tu propio codigo luce como una porquería.

Algo más allá de tu control ha cambiado. Por eso es así que muchos equipos de desarrollo pasan de sus ideas originales a algo
nuevo. Todos los que confiaron en flash por ejemplo para una aplicación en línea
han estado re codificando o migrando su código después de
el navegador dejara de admitir flash definitivamente.

La tercera razón es que los postes de la portería se mueven, es decir, siempre hay cambios nuevos. Su cliente esta
encantado con la versión actual de la aplicación, pero ahora
ve once "pequeños" cambios que le gustaría realizar para que pueda hacer otras cosas que nunca mencionó en las sesiones de planificación originales. Estos
no son cambios "frívolos" (o medios tontos, insignificantes): su excelente primera versión hace pensar aún más que es posible realizarlos.

```
Hay un lado positivo: si alguien te pide que cambies
algo en tu aplicación, eso significa que a alguien todavía le importa esa aplicación
```

Es por eso que todos los desarrolladores experimentados intentan proporcionar soluciones para posibles
cambios futuros al diseñar la arquitectura de una aplicación de una manera lo más flexible posible.
