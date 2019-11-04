---
layout: post
title: "Introducción a la Programación: Lección 1"
tags: [Introducción a Programación]
---

### Introducción

Los estudiantes que toman mis clases a menudo han tratado de enseñarse a sí mismos materias como HTML o JavaScript leyendo entradas
en blogs o copiando y pegando código, pero no han sido capaces de dominar realmente el material
que les permitirá codificar el resultado deseado. Y debido a que no comprenden realmente los
pormenores de ciertos temas de codificación, no pueden escribir código poderoso o depurar su
propio trabajo, ya que no entienden realmente lo que está sucediendo.

Vamos a introducirnos en algunos conceptos básicos de la programación de la mano de Javascript, ya que con el navegador
podemos realizar pruebas sencilla de lo que estamos hablando, sin necesitar de editores extras.

### Código

Empecemos desde el principio.

### ¿Qué es un Programa?

Un programa, a menudo denominado código fuente o simplemente código, es un conjunto de
instrucciones especiales para decirle al equipo qué tareas realizar. Por lo general, el código se
guarda en un archivo de texto, aunque con JavaScript también puede escribir código directamente
en una consola de desarrollo en un navegador, que cubriremos en breve.
Las reglas para el formato válido y las combinaciones de instrucciones se denominan lenguaje
informático, a veces conocido como su sintaxis, al igual que el inglés le dice cómo deletrear
palabras y cómo crear oraciones válidas usando palabras y puntuación.

### Sentencias

En un lenguaje informático, un grupo de palabras, números y operadores que realizan una tarea
específica es una expresión. En JavaScript, una sentencia puede tener el siguiente aspecto:

```
a = b * 2;
```

Los caracteres a y b se llaman **variables** (ver "Variables"), que son como simples casillas en las que
se puede almacenar cualquiera de tus cosas. En los programas, las variables contienen valores
(como el número 42) que debe utilizar el programa. Piense en ellos como marcadores de posición
simbólicos para los propios valores.

Por el contrario, el `2` es sólo un valor en sí mismo, llamado valor literal, porque se encuentra solo
sin ser almacenado en una variable.

Los caracteres "=" y "*" son **operadores** (ver "Operadores") realizan acciones con los valores y
variables como asignación y multiplicación matemática.

La mayoría de las expresiones en JavaScript terminan con un punto y coma ( ; ) al final.

La sentencia `a = b * 2;` le dice al ordenador, aproximadamente, que obtenga el valor actual
almacenado en la variable b, multiplique ese valor por 2, y luego almacene el resultado de nuevo en
otra `variable` que llamamos a.

Los programas son sólo colecciones de muchas de estas afirmaciones, que en conjunto describen
todos los pasos que toma para cumplir con el propósito de su programa.

### Expresiones

Las declaraciones se componen de una o más **expresiones**. Una expresión es cualquier referencia a
una variable o valor, o un conjunto de variables y valores combinados con operadores.
Por ejemplo:

```
a = b * 2;
```

Esta declaración tiene cuatro expresiones:

● `2` es una expresión de valor literal

● `b` es una expresión variable, lo que significa que recupera su valor actual

● `b * 2` es una expresión aritmética, lo que significa hacer la multiplicación

● `a = b * 2` es una expresión de asignación, lo que significa asignar el resultado de la función `b * 2` en la expresión de la variable a (más información sobre las asignaciones posteriores)

Una expresión general que sobresale por sí sola también se llama una declaración de expresión,
como la expresión siguiente: `b * 2;`

Este tipo de expresión no es muy común o útil, ya que generalmente no tendría ningún efecto en la
ejecución del programa, recuperaría el valor de b y lo multiplicaría por 2, pero luego no haría nada
con ese resultado.

Una expresión más común es una una declaración de expresión de llamada (ver "Funciones"), ya
que la declaración entera es la expresión de llamada de función en sí misma:

```
alert(a);
```

### Ejecución de un programa

¿Cómo le dicen al ordenador qué hacer esas colecciones de frases de programación?

El programa necesita ser ejecutado, también conocido como **ejecución del programa**.

Declaraciones como `a = b * 2` son útiles para los desarrolladores cuando leen y escriben, pero en
realidad no están en una forma que la computadora pueda entender directamente. Por lo tanto, se
utiliza una utilidad especial en la computadora (ya sea un intérprete o un compilador) para traducir
el código que escribes en comandos que una computadora puede entender.

Para algunos lenguajes de computadora, esta traducción de comandos se hace típicamente de
arriba a abajo, línea por línea, cada vez que se ejecuta el programa, lo que generalmente se llama
**interpretación del código**.

Para otros idiomas, la traducción se hace con anticipación, llamada **compilación del código**, de
modo que cuando el programa se ejecuta más tarde, lo que se está ejecutando es en realidad las
instrucciones de la computadora ya compiladas listas para usar.

Dato de color: Típicamente se afirma que JavaScript es interpretado, porque su código fuente JavaScript es
procesado cada vez que se ejecuta. Pero eso no es del todo exacto. El motor JavaScript compila el
programa sobre la marcha e inmediatamente ejecuta el código compilado.

### Pruébelo usted mismo

Este capítulo va a introducir cada concepto de programación con simples fragmentos de código, todos escritos en JavaScript.

No se puede enfatizar lo suficiente: mientras usted pasa por este capítulo y puede que necesite pasar el tiempo para repasarlo varias veces debería practicar cada uno de estos conceptos
escribiendo el código usted mismo. La forma más fácil de hacerlo es abrir la consola de
herramientas del desarrollador en su navegador más cercano (Firefox, Chrome, IE, etc.)

Para escribir varias líneas en la consola a la vez, utilice <shift> + <enter> para pasar a la siguiente línea nueva.
Una vez que pulse <enter> por sí mismo, la consola ejecutará todo lo que acaba de escribir.

Familiaricémonos con el proceso de ejecutar código en la consola. Primero, sugiero abrir una
pestaña vacía en su navegador. Prefiero hacer esto escribiendo: about:blank: en la barra de
direcciones. A continuación, asegúrese de que su consola de desarrollador está abierta, como
acabamos de mencionar.

Ahora, escriba este código y vea cómo funciona:

```
a = 21;
b = a * 2;
console.log( b );
```

Escribir el código anterior en la consola de Chrome debería producir algo como el archivo siguiente:
```
42
```

Vamos, inténtalo. La mejor manera de aprender a programar es empezar a codificar!

### Salida

En el fragmento de código anterior, usamos `console.log(...)`. Brevemente, veamos de qué se trata
esa línea de código.

Puede que lo hayas adivinado, pero así es exactamente como imprimimos el texto (también
conocido como salida al usuario) en la consola de desarrollo. Hay dos características de esa
afirmación que deberíamos explicar.

 - En primer lugar, la parte de log(b) se denomina función; véase ("Funciones"). Lo que está
sucediendo es que estamos entregando la variable b a esa función, que le pide que tome el valor
de b y lo imprima en la consola.

 - Segundo, la consola. es una referencia de objeto donde se encuentra la función log(...).

Otra forma de crear un resultado que se puede ver es ejecutar una declaración de alerta(...).

Por ejemplo:

```
alert(b);
```

Si lo ejecuta, notará que en lugar de imprimir la salida a la consola, muestra un cuadro emergente
"OK" con el contenido de la variable b. Sin embargo, el uso de `console.log(...)` generalmente hará
que aprender a codificar y ejecutar sus programas en la consola sea más fácil que el uso de
`alert(...)`, ya que puede imprimir muchos valores a la vez sin interrumpir la interfaz del
navegador.

### Entrada

Mientras discutimos la salida, también puede preguntarse acerca de la entrada (es decir, recibir
información del usuario).

La forma más común de hacerlo es que la página HTML muestre elementos de formulario (como
cuadros de texto) a un usuario en el que puede escribir, y luego usar JS o leer esos valores en las
variables de su programa.

Pero hay una manera más fácil de obtener información para fines de ganancia y demostración
sencillos. Utilice la función `prompt(...)`:

```
age = prompt( "Please tell me your age:" );
console.log( age );
```

Como usted puede haber adivinado, el mensaje que usted pasa al `prompt(...)` en este caso,"Por
favor dígame su edad:" se imprime en la ventana emergente.

Una vez que envíe el texto de entrada haciendo clic en "Aceptar", observará que el valor que ha
escrito se almacena en la `variable de edad`, que luego emitimos con `console.log(...)`.
