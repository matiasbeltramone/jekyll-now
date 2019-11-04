---
layout: post
title: "Introducción a la Programación: Lección 2"
tags: [Introducción a Programación]
---

### Conceptos Básicos

En base a lo visto en la lección anterior, debemos aclarar algunos conceptos que hemos mencionado para que luego podamos entender todos los conceptos
más avanzados comencemos...

### Operadores

Los operadores son cómo realizamos acciones sobre variables y valores. Ya hemos visto dos operadores JavaScript, el `=` y el `*`.

El operador `*` realiza la multiplicación matemática. Bastante simple, ¿verdad?

El operador `=` igual se utiliza para la asignación primero calculamos el valor en el lado derecho
(valor fuente) del `=` y luego lo ponemos en la variable que especificamos en el lado izquierdo (variable objetivo).

**Advertencia**: Esto puede parecer un extraño orden inverso para especificar la asignación. En lugar
de `a = 42`, algunos pueden preferir voltear el orden para que el valor fuente esté a la izquierda y la
variable de destino esté a la derecha, por ejemplo `42 -> a` (¡Esto no es válido JavaScript!).

Desafortunadamente, la forma ordenada `a = 42`, y variaciones similares, prevalece en los lenguajes
de programación modernos. Si se siente poco natural, simplemente pasar un tiempo ensayando
ordenando en su mente para acostumbrarse a ella.

Considerando lo siguiente:

```
a = 2;
b = a + 1;
```

Aquí, asignamos el valor `2` a una variable. Entonces, obtenemos el valor de la variable `a` (todavía 2),
sumar `1` al mismo, resultando en el valor `3`, luego almacenar ese valor en la variable `b`.

Aunque no sea técnicamente un operador, necesitarás la palabra clave var en cada programa, ya
que es la forma principal de declarar (o crear) variables (ver "Variables").

Siempre debe declarar la variable por nombre antes de usarla. Pero usted solo necesita declarar un
variable una vez para cada alcance (ver "Alcance"); puede ser usado tantas veces como sea necesario. Por ejemplo:

```
var a = 20;
a = a + 1;
a = a * 2;
console.log( a ); // 42
```

Éstos son algunos de los operadores más comunes en JavaScript:

● Asignación: `=` como en a = 2 .

● Matemáticos: `+` (adición), `-` (sustracción), `*` (multiplicación), y `/` (división), como en un `a*3`.

● Asignación compuesta: `+=, -=, *=, y /=` son operadores compuestos que combinan una
operación matemática con asignación, como en `+= 2` (igual que a = a + 2).

● Incremento/Disminución: `++` (incremento), `--` (decremento), como en `a++` (similar a = a + 1 ).

● Acceso a la propiedad de objetos: como en `console.log()`. Los objetos son valores que
contienen otros valores en ubicaciones específicas denominadas propiedades, `obj.a` significa un
valor de objeto llamado obj con una propiedad del nombre a. Alternativamente se puede acceder
a las propiedades como obj["a"].

● Igualdad: `==` (iguales), `===` (estríctamente iguales), `!=` (no iguales), `!==` (no estríctamente iguales),
como en `a == b`. Consulte "Valores y tipos".

● Comparación: `<` (menos de), `>` (mayor que), `<=` (menos o iguales), `>=` (mayores o iguales), como en `a
<= b`. Consulte "Valores y tipos".

● Lógicos: `&&` (y), `||` (o), como en un `a || b` que selecciona a o b . Estos operadores se utilizan para
expresar condicionales compuestos (ver "Condicionales"), como si a o b son true (verdaderos).

Nota: Para más detalles y cobertura de operadores no mencionados aquí, ver Mozilla Expresiones y
operadores de Developer Network (MDN)
(https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)

### Valores y Tipos

Si usted le pregunta a un empleado en una tienda de teléfonos cuánto cuesta un teléfono
determinado y le dicen "noventa y nueve con noventa y nueve" (es decir, $99.99), le están dando una
cifra numérica real en dólares que representa lo que tendrá que pagar (más impuestos) para
comprarlo. Si usted desea comprar dos de esos teléfonos, usted puede fácilmente hacer el cálculo
mental y duplicar ese valor para obtener $199.98.

Si ese mismo empleado recoge otro teléfono similar pero dice que es "gratis" (quizás
entrecomillando), no le está dando un número, sino otro índice de representación de su costo
esperado ($0.00) la palabra "gratis".

Cuando más tarde pregunte si el teléfono incluye un cargador, esa respuesta sólo podría haber
sido "sí" o "no".

De maneras muy similares, cuando expresas valores en un programa, eliges diferentes
representaciones para esos valores en función de lo que piensas hacer con ellos.

Estas diferentes representaciones de valores se denominan **tipos** en la terminología de
programación. JavaScript tiene tipos incorporados para cada uno de estos llamados valores
primitivos:

● Cuando necesitas hacer matemáticas, quieres un número .

● Cuando necesite imprimir un valor en la pantalla, necesitará una cadena de texto (uno o más
caracteres, palabras, frases. En adelante los denominaremos string ).

● Cuando usted necesita tomar una decisión en su programa, necesita un booleano (ya sea true ,
verdadero o false , falso).

Los valores que se incluyen directamente en el código fuente se llaman **literales**. Los literales de
string y están rodeados por comillas dobles "..." o comillas simples ('...') la única diferencia
es la preferencia estilística. number y literales booleanos se presentan tal como están (es decir, 42, true, etc.).

Fíjese en lo siguiente:

 - "I am a string";

 - 'I am also a string';

 - 42;

 - true;

 - false;
 
 Más allá de los tipos de cadena / número / valor booleano, es común que los lenguajes de
programación proporcionen arrays, objetos, funciones y mucho más. Cubriremos mucho más
sobre valores y tipos a lo largo de estas lecciones.

### Conversión entre tipos

Si tiene un número pero necesita imprimirlo en la pantalla, debe convertir el valor a un string , y en
JavaScript esta conversión se llama "coercion" (coerción, coacción). Del mismo modo, si alguien
introduce una serie de caracteres numéricos en un formulario de una página de comercio
electrónico, eso es un string , pero si necesita usar ese valor para realizar operaciones
matemáticas, debe transformarlo a un número.

JavaScript proporciona varias facilidades diferentes para realizar transformaciones a la fuerza entre
tipos.

Por ejemplo:

```
var a = "42";
var b = Number( a );
console.log( a ); // "42"
console.log( b ); // 42
```

El uso del Number(...) (una función propia de Javascript) como se muestra es una coerción
explícita de cualquier otro tipo a la directiva tipo de number. Eso debería ser bastante sencillo.

Pero un tema controvertido es lo que ocurre cuando se intenta comparar dos valores que no son
del mismo tipo, lo que requeriría una coerción implícita.

Al comparar la cadena "99.99" con el número 99.99 , la mayoría de las personas estarían de
acuerdo en que son equivalentes. Pero no son exactamente iguales, ¿verdad? Es el mismo valor en
dos representaciones diferentes, dos tipos diferentes. Podrías decir que son "vagamente iguales", ¿no?

Para ayudarle en estas situaciones comunes, JavaScript a veces se activa e implícitamente
transforma los valores a los tipos correspondientes.

Por lo tanto, si utiliza el operador `==` para hacer la comparación "99.99" == 99.99 , JavaScript
convertirá el lado izquierdo "99.99" a su número equivalente 99.99. La comparación se convierte
entonces en 99.99 == 99.99 , lo cual es por supuesto cierto.

Aunque diseñado para ayudarte, la coerción implícita puede crear confusión si no te has tomado el
tiempo para aprender las reglas que gobiernan su comportamiento. La mayoría de los
desarrolladores de JS nunca lo han hecho, así que la sensación común es que la coerción implícita
está confundiendo y perjudicando a los programas con errores inesperados, y por lo tanto debe
ser evitada. Incluso a veces se le llama un defecto en el diseño del lenguaje. Sin embargo, la coerción implícita es
un mecanismo que se puede aprender, y además debe ser aprendido por cualquiera que desee tomarse en serio la programación en
JavaScript. No sólo no es confuso una vez que usted aprende las reglas, sino que en realidad puede mejorar sus programas!

El esfuerzo bien vale la pena.

Daremos dos lecciones más sobre estos conceptos básicos...

