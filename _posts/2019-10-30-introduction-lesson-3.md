---
layout: post
title: "Introducción a la Programación: Lección 3"
tags: [Introducción a Programación]
---

### Conceptos Básicos: Parte II

### Comentarios de código

El empleado de la tienda telefónica podría anotar algunas notas sobre las características de un
teléfono recién lanzado o sobre los nuevos planes que su compañía ofrece. Estas notas son sólo
para el empleado, no son para que los clientes las lean. Sin embargo, estas notas ayudan al
empleado a hacer mejor su trabajo al documentar el cómo y el por qué de lo que debe decir a los
clientes.

Una de las lecciones más importantes que puedes aprender sobre la escritura de código es que no
es sólo para el ordenador. El código es cada bit tanto, si no más, para el desarrollador como para el
compilador.

Su ordenador sólo se preocupa por el código de máquina, una serie de 0s y 1s binarios, que viene
de la compilación. Hay un número casi infinito de programas que podrías escribir que producen la
misma serie de 0s y 1s. Las decisiones que usted toma sobre cómo escribir su programa son
importantes, no sólo para usted, sino también para los demás miembros de su equipo e incluso
para su futuro.

Usted debe esforzarse no sólo para escribir programas que funcionan correctamente, sino
programas que tengan sentido cuando se examinan. Se puede avanzar mucho en ese esfuerzo
eligiendo buenos nombres para las variables (véase "Variables") y funciones (véase "Funciones").

Pero otra parte importante son los comentarios de código. Estos son fragmentos de texto en su
programa que se insertan para explicarle cosas a un humano. El intérprete/compilador siempre
ignorará estos comentarios.

Hay muchas opiniones sobre lo que hace al código bien comentado; no podemos definir realmente
normas universales absolutas. Pero algunas observaciones y directrices son bastante útiles:

- El código sin comentarios es subóptimo.
- Demasiados comentarios (uno por línea, por ejemplo) es probablemente un signo de código mal escrito.
- Los comentarios deberían explicar por qué, no qué. Pueden opcionalmente explicar cómo si eso
es particularmente confuso.

En JavaScript, hay dos tipos de comentarios posibles: un comentario de una sola línea y un
comentario multilínea:

// This is a single-line comment

/* But this is
a multiline
comment.
*/

El comentario // de una línea es apropiado si va a poner un comentario justo encima de una sola
frase, o incluso al final de una línea. Todo lo que aparece en la línea después de // es tratado como
comentario (y por lo tanto ignorado por el compilador), hasta el final de la línea. No hay
restricciones a lo que puede aparecer dentro de un comentario de una sola línea.

Por ejemplo:
```
var a = 42; // 42 is the meaning of life
```

El comentario /*... */ multilínea es apropiado si tiene varias líneas por valor de explicación que
hacer en su comentario.

He aquí un uso común de los comentarios multilínea:

```
/* The following value is used because
it has been shown that it answers
every question in the universe. */

var a = 42;

También puede aparecer en cualquier parte de una línea, incluso en el medio de una línea, porque
el */ termina con ella. Por ejemplo:

var a = /* arbitrary value */ 42;

console.log( a ); // 42
```

Lo único que no puede aparecer dentro de un comentario multilínea es un */ , porque eso sería
interpretado para terminar el comentario.

Definitivamente querrá comenzar su aprendizaje de programación empezando con el hábito de
comentar código. A lo largo del resto de este capítulo, verás que usaré comentarios para explicarte
las cosas, así que haz lo mismo en tu propia práctica. Confía en mí, todos los que lean tu código te
lo agradecerán!

### Variables

La mayoría de los programas útiles necesitan rastrear un valor a medida que cambia en el curso
del programa, sometiéndose a diferentes operaciones según lo requieran las tareas previstas de su
programa.

La manera más fácil de hacer esto en su programa es asignar un valor a un contenedor simbólico,
llamado variable, así llamado porque el valor en este contenedor puede variar con el tiempo
según sea necesario.

En algunos lenguajes de programación, se declara una variable (contenedor) para mantener un tipo
específico de valor, como number o string. El **tipado estático**, también conocido como ejecución de
tipo, es típicamente citado como un beneficio para la corrección del programa al prevenir
conversiones de valor no deseadas.

Otros lenguajes enfatizan tipos de valores en vez de variables. El *tipado débil*, también conocido
como **tipado dinámico**, permite que una variable contenga cualquier tipo de valor en cualquier
momento. Típicamente se cita como un beneficio para la flexibilidad del programa al permitir que
una sola variable represente un valor, independientemente del tipo de forma que pueda tomar ese
valor en cualquier momento dado en el flujo lógico del programa.

JavaScript utiliza el último enfoque, el tipado dinámico, lo que significa que las variables pueden
contener valores de cualquier tipo sin ningún tipo de imposición.

Como se mencionó anteriormente, declaramos una variable usando la sentencia `var` note que no
hay otro tipo de información en la declaración. Considere este sencillo programa:

```
var amount = 99.99;
amount = amount * 2;
console.log( amount ); // 199.98
// convert `amount` to a string, and
// add "$" on the beginning
amount = "$" + String( amount );
console.log( amount ); // "9.98"
```

La variable amount comienza con el número 99.99 , y luego retiene el resultado numerico de `amount * 2` , que es 199.98.

El primer comando `console.log(...)` tiene que coaccionar implícitamente ese valor numérico a una
cadena para imprimirlo.

Entonces, la sentencia `amount = "$" + String(amount)` coacciona explícitamente el valor 199.98 a
un valor de tipo string y añade un carácter "$" al principio. En este punto, el importe ahora
contiene el valor de string "$199.98" , así que el segundo console.log(...) no necesita hacer
ninguna coerción para imprimir el valor.

Los desarrolladores de JavaScript notarán la flexibilidad de usar la variable amount para cada unode los 99.99 , 199.98 , y los valores de "$199.98" . Los entusiastas del tipado estático preferirían una
variable separada como amountStr para mantener la representación final de "$199.98" del valor,
porque es un tipo diferente.

De cualquier manera, notará que esa cantidad tiene un valor de ejecución que cambia en el curso
del programa, ilustrando el propósito principal de las variables: administrar el estado del programa.

En otras palabras, el estado está rastreando los cambios en los valores a medida que se ejecuta el
programa.

Otro uso común de las variables es para centralizar la configuración de valores. Esto es más
comúnmente llamado constantes, cuando se declara una variable con un valor y se intenta que ese
valor no cambie en todo el programa.

Usted declara estas constantes, a menudo en la parte superior de un programa, de modo que es
conveniente para usted tener un lugar para ir a alterar un valor si es necesario. Por convención, las
variables JavaScript como constantes suelen estar en mayúsculas, con subrayados _ entre varias
palabras.

Aquí hay un ejemplo tonto:

```
var TAX_RATE = 0.08; // 8% sales tax
var amount = 99.99;
amount = amount * 2;
amount = amount + (amount * TAX_RATE);
console.log( amount ); // 215.9784
console.log( amount.toFixed( 2 ) ); // "215.98"
```

Nota: Similar a como sucede en console.log(..) donde la función log(...) es accedida, accede a
traves de una propiedad del objeto console, toFixed(...) aquí es una función a la que se puede
acceder con valores numéricos. Los números en JavaScript no están formateados automáticamente
para los dólares el motor no sabe cuál es su intención y no hay ningún tipo de moneda.
toFixed(...) nos permite especificar con cuántos decimales queremos que se redondee el número,
y produce un string según sea necesario.

La variable TAX_RATE sólo es constante por convención no hay nada especial en este programa
que impida que se cambie. Pero si la ciudad eleva la tasa del impuesto a las ventas al 9%, todavía
podemos actualizar fácilmente nuestro programa estableciendo el valor asignado de TAX_RATE a
0.09 en un sólo lugar, en vez de encontrar muchas ocurrencias del valor 0.08 esparcidas por todo
el programa y actualizarlas todas.

La última versión de JavaScript en el momento de escribir este artículo (comúnmente llamada
"ES6") incluye una nueva forma de declarar constantes, usando const en lugar de var :// as of ES6:

```
const TAX_RATE = 0.08;
var amount = 99.99;
// ..
```

Las constantes son útiles al igual que las variables con valores inalterados, excepto que las
constantes también evitan el cambio accidental de valor en otro lugar después del ajuste inicial. Si
usted trató de asignar cualquier valor diferente a TAX_RATE después de esa primera declaración, su
programa rechazaría el cambio (y en modo estricto, fallaría con un error)

A propósito, esa clase de "protección" contra errores es similar a la ejecución de tipo estático, así
que usted puede ver por qué los tipos estáticos en otros idiomas pueden ser atractivos!

### Bloques

El empleado de la tienda telefónica debe seguir una serie de pasos para completar el proceso de
pago al comprar su nuevo teléfono.

Del mismo modo, en el código a menudo necesitamos agrupar una serie de frases, que a menudo
llamamos bloque. En JavaScript, un bloque se define envolviendo una o más sentencias dentro de
un par de llaves {...}.

Ejemplo:

```
var amount = 99.99;
// a general block
{
  amount = amount * 2;
  console.log( amount );// 199.98
}
```

Este tipo de bloque general autónomo {...} es válido, pero no es tan comúnmente visto en los
programas JS. Típicamente, los bloques se adjuntan a alguna otra instrucción de control, como una
instrucción if (ver "Condicionales") o un bucle (ver "Bucles"). Por ejemplo:

```
var amount = 99.99;
// is amount big enough?
if (amount > 10) { // <-- block attached to `if`
  amount = amount * 2;
  console.log( amount ); // 199.98
}
```

Explicaremos las declaraciones en la siguiente sección, pero como puede ver, el bloque {...}
con sus dos declaraciones se adjunta a `if(amount > 10);` las declaraciones dentro del bloque sólo
se procesarán si las condicionales pasan.

Nota: A diferencia de la mayoría de las otras sentencias como `console.log(amount);`, una
sentencia de bloque no necesita un punto y coma (;) para concluirla.
