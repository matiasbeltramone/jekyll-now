---
layout: post
title: Programación Orientada a Objetos: Lección 1.
tags: [POO, OOP, Programación Orientada a Objetos, Object Oriented Programming]
---

### ¿Qué es el Paradigma Orientado a Objetos?

La **programación orientada a objetos** es un paradigma basado en agrupar `propiedades` y su `comportamiento` relacionado,
de un concepto determinado como puede ser una "Vivienda", "Vehiculo", a traves de un elemento especial llamado **objetos**,
los cuales son construidos a partir de una "estructura" o "esquema", definido por el programador, el cuál lo llamamos **clases**.

Los **objetos** normalmente no dejan de ser una **representación de algo** en nuestro mundo (casa, vehiculos, empresas, personas, comida, dinero), aunque también podría ser la representación de algun concepto mas **abstracto** que no podamos ver, sentir, pero que represente algo en particular que sea útil en nuestro **modelo de negocio**.

### Objetos, Clases
<p align="center"><img src="https://user-images.githubusercontent.com/22304957/68073515-d8ae2780-fd6f-11e9-8857-c2b5fd9c466f.png"/>
</p>

Digamos que tenes un gato que se llama Pini. Pini es un `objeto`, una **instancia** de la `clase` **Cat**.

Cada gato tiene distintos atributos:

- Nombre
- Sexo
- Edad
- Tamaño
- Color
- Comida Favorita
- ...

Estos son **atributos** o **propiedades**, también más comunmente conocidos como "estados" de la clase.

Además todos los gatos tienen comportamiento similares:

- Respiran
- Comen
- Caminan
- Duermen
- Maullan
- ...

Estos son **métodos**, o conocidos en la jerga de programación como "comportamiento" de las **clases**.

En conjunto, las **propiedades** y los **métodos** son miembros y conforman nuestra estructura llamada **clase**, de las cuáles podemos crear nuestros **objetos** particulares.

#### Ejemplos
<p align="center"><img src="https://user-images.githubusercontent.com/22304957/68073995-d7cbc480-fd74-11e9-8438-e12981734d61.png"/></p>

Luna, amiga de Oscar, son tambien instancias de la clase **Cat**. Tiene los mismos atributos que Oscar. La diferencia esta en
los valores que toman los atributos: Luna es de sexo femenino, tiene diferente color, peso, etc.

Entonces la `clase` es un esqueleto base para los `objetos`, los cuales son instancias concretas de la clase,
esto significan que toman este esqueleto y lo rellenan con los atributos necesarios
para en este caso un gato en particular (Luna y Oscar).
