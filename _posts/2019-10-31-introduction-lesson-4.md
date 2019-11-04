---
layout: post
title: "Introducción a la Programación: Lección 4"
tags: [Introducción a Programación]
---

### Conceptos Básicos: Parte III

### Condicionales

"¿Quieres añadir en la pantalla protectores extra a tu compra, por $9.99?"

El empleado de la tienda telefónica le ha pedido que tome una decisión. Y es posible que necesite consultar primero el
estado actual de su billetera o cuenta bancaria para responder a esa pregunta. Pero obviamente,
esta es una simple pregunta de "sí o no".

Hay bastantes maneras en que podemos expresar los condicionales (también conocidos como
decisiones) en nuestros programas. La más común es la declaración **if**. Básicamente, estás
diciendo:

"Si esta condición es cierta, haz lo siguiente...". Por ejemplo:

```
var bank_balance = 302.13;
var amount = 99.99;
if (amount < bank_balance) {
  console.log( "I want to buy this phone!" );
}
```

La sentencia if requiere una expresión entre paréntesis () que puede ser tratada como verdadera
o falsa. En este programa, proporcionamos la expresión `amount < bank_balance`, el cual
efectivamente se evaluará como true o false dependiendo del importe en la variable
`bank_balance`.

Incluso puede proporcionar una alternativa si la condición no es verdadera, llamada cláusula
diferente. Considere lo siguiente:

```
const ACCESSORY_PRICE = 9.99;
var bank_balance = 302.13;
var amount = 99.99;
amount = amount * 2;
// can we afford the extra purchase?
if ( amount < bank_balance ) {
  console.log( "I'll take the accessory!" );
  amount = amount + ACCESSORY_PRICE;
} else {
  console.log( "No, thanks." );
}
```

Aquí, si amount < bank_balance es verdadera, vamos a imprimir "Me llevaré el accesorio!" y
sumamos el 9,99 a nuestra variable amount. De lo contrario, la cláusula dice que responderemos
cortésmente con "No, gracias". y dejar la cantidad sin cambios.

Como discutimos anteriormente en "Valores y Tipos", los valores que no son del tipo esperado son
a menudo coaccionados a ese tipo. La sentencia if espera un booleano , pero si le pasa algo que no
lo es, ocurrirá la coerción.

JavaScript define una lista de valores específicos que se consideran "Falsy" porque al ser forzados a
un booleano , se vuelven false estos incluyen valores como 0 y "". Cualquier otro valor que no
aparezca en la lista de "falsy" es automáticamente "Truthy" cuando se coacciona a un booleano
se convierte en true. Los valores de Truthy incluyen cosas como 99.99 y "gratis".

Los condicionales existen en otras formas además del if. Por ejemplo, la sentencia switch puede
usarse como abreviatura para una serie de sentencias if... else. Los bucles (ver
"Bucles") utilizan un condicional para determinar si el bucle debe continuar o detenerse.

### Bucles

Durante los momentos de mucho trabajo, hay una lista de espera para los clientes que necesitan
hablar con el empleado de la tienda telefónica. Mientras que todavía hay gente en esa lista, ella
sólo necesita seguir sirviendo al próximo cliente.

Repetir un conjunto de acciones hasta que una determinada condición falla en otras palabras,
repetir sólo mientras la condición se mantiene es el trabajo de programar bucles; los bucles
pueden tomar diferentes formas, pero todos satisfacen este comportamiento básico.

Un bucle incluye la condición de prueba así como un bloque (típicamente como {... }). Cada vez
que el bloque de bucle se ejecuta, eso se llama una **iteración**.

Por ejemplo, los modalidades de loops while y do... while ilustran el concepto de repetir un
bloque de sentencias hasta que una condición ya no se evalúa como true:

```
while (numOfCustomers > 0) {
  console.log( "How may I help you?" );
  // help the customer...
  numOfCustomers = numOfCustomers - 1;
}
```

```
// versus:do {
  console.log( "How may I help you?" );
  // help the customer...
  numOfCustomers = numOfCustomers - 1;
} while (numOfCustomers > 0);
```

La única diferencia práctica entre estos bucles es si el condicional se prueba antes de la primera
iteración ( while ) o después de la primera iteración ( do...while ).

En cualquier forma, si las pruebas condicionales son false , la siguiente iteración no se ejecutará.
Eso significa que si la condición es inicialmente false, un bucle while nunca se ejecutará, pero un
do... while se ejecutará sólo la primera vez.

A veces usted está haciendo bucles con el propósito de contar un determinado conjunto de
números, como por ejemplo de 0 a 9 (diez números). Esto se puede hacer estableciendo una
variable de iteración de bucle como i en el valor 0 y aumentando 1 por cada iteración.

**Advertencia**: Por una variedad de razones históricas, los lenguajes de programación casi siempre
cuentan las cosas empezando por 0 en lugar de 1. Si usted no está familiarizado con ese modo de
pensar, puede ser bastante confuso al principio. Tómese un tiempo para practicar el conteo
empezando con 0 para sentirse más cómodo con él!

El condicional se prueba en cada iteración, como si hubiera una declaración implícita dentro del
bucle.

Podemos usar la sentencia break de JavaScript para detener un bucle. También, podemos observar
que es terriblemente fácil crear un bucle que de otro modo funcionaría para siempre sin un
mecanismo break de ruptura.

Vamos a ilustrarlo:

```
var i = 0;
// El bucle `while..true` se ejecutará por siempre no?
while (true) {
  // stop the loop?
  if ((i <= 9) === false) {
    break;
  }
  
  console.log( i );
  
  i = i + 1;
}
// 0 1 2 3 4 5 6 7 8 9
```

Advertencia: Esta no es necesariamente una forma práctica que usted querría usar para sus bucles.
Se presenta aquí sólo con fines ilustrativos.

Mientras que un while (o do...while ) puede realizar la tarea manualmente, hay otra forma sintáctica llamada bucle for para ese propósito:

```
for (var i = 0; i <= 9; i = i + 1) {
  console.log( i );
}
// 0 1 2 3 4 5 6 7 8 9
```

Como se puede ver, en ambos casos el condicional i <= 9 es verdadero para las primeras 10
iteraciones ( i de los valores 0 a 9 ) de cualquiera de las dos formas de bucle, pero se convierte en
false una vez que i es el valor 10.

El bucle for tiene tres cláusulas: la cláusula de inicialización ( var i=0 ), la cláusula de prueba
condicional ( i <=9 ) y la cláusula de actualización ( i = i + 1 ).

Así que si vas a contar con tus
iteraciones de bucle, for es una forma más compacta y a menudo más fácil de entender y escribir.

Hay otras formas de bucle especializadas que intentan iterar sobre valores específicos, como las
propiedades de un objeto donde la prueba condicional implícita es si todas las
propiedades han sido procesadas. El concepto de "bucle hasta que una condición falla" es válido
independientemente de la forma del bucle.

### Funciones

El empleado de la tienda telefónica probablemente no lleva una calculadora para calcular los
impuestos y el monto final de la compra. Esa es una tarea que necesita definir una vez y reutilizar
una y otra vez. Lo más probable es que la empresa tenga un registro de caja (ordenador, tableta,
etc.) con esas "funciones" incorporadas.

De manera similar, su programa seguramente querrá dividir las tareas del código en partes
reutilizables, en vez de repetirse repetirse repetidamente (juego de palabras intencionadas!). La
manera de hacer es definir una función.

Una función es generalmente una sección de código con nombre que puede ser "llamada" por
nombre, y el código dentro de ella se ejecutará cada vez.

Considera lo siguiente:

```
function printAmount() {
  console.log( amount.toFixed( 2 ) );
}

var amount = 99.99;
printAmount(); // "99.99"
amount = amount * 2;
printAmount(); // "199.98"
```

Las funciones pueden opcionalmente tomar argumentos (alias parámetros) -- valores que ustedtransfiere. Opcionalmente, también pueden devolver un valor.

```
function printAmount(amt) {
  console.log( amt.toFixed( 2 ) );
}

function formatAmount() {
  return "$" + amount.toFixed( 2 );
}

var amount = 99.99;
printAmount( amount * 2 ); // "199.98"
amount = formatAmount();
console.log( amount ); // ".99"
```

La función printAmount(..) toma un parámetro que llamamos amt . La función formatAmount()
devuelve un valor. Por supuesto, también puede combinar estas dos técnicas en la misma función.
Las funciones se utilizan a menudo para el código que planea llamar varias veces, pero también
pueden ser útiles sólo para organizar los bits de código relacionados en colecciones de nombre,
incluso si sólo planea llamarlos una vez.

Considera lo siguiente:
```
const TAX_RATE = 0.08;

function calculateFinalPurchaseAmount(amt) {
  // calculate the new amount with the tax
  amt = amt + (amt * TAX_RATE);
  // return the new amount
  return amt;
}

var amount = 99.99;
amount = calculateFinalPurchaseAmount( amount );
console.log( amount.toFixed( 2 ) ); // "107.99"
```

Aunque calculateFinalPurchaseAmount(...) sólo se llama una vez, al organizar su
comportamiento en una función con nombre separado, el código que usa su lógica (la sentencia
amount = calculateFinal...) es más limpio. Si la función tuviera más enunciados en ella, los
beneficios serían aún más pronunciados.

Con estos conceptos básicos, ya podemos seguir adelante con otros conceptos y paradigmas de programación.
