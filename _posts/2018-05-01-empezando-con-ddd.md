
Domain Driven Design no es sobre tecnología. Es sobre el desarrollo del conocimiento del negocio y utiliza la tecnologia para darle valor. Una vez que eres capaz de entender el negocio tu compania trabaja en conjunto, tu puedes participar en el modelo de desarrollo descubriendo procesos para producir un Lenguaje Ubicuo.

¿POR QUE DOMAIN-DRIVEN DESIGN?
El software no solo debe tener sentido para los desarrolladores sino tambien para el negocio. DDD enfatiza en que tanto el lado del negocio como los desarrolladores hablen el mismo lenguaje.

Se alinean las prioridades del software con las del negocio.

Con DDD todos aprenden y contribuyen descubriendo el dominio del negocio.

El conocimiento no solo pertenece a los desarrolladores, con DDD todos concocen que esta pasando con el negocio.

No hace falta establecer traducciones entre el lenguaje de expertos del dominio y los desarrolladores, todos hablan el mismo lenguaje.

El diseño es el codigo y el codigo es el diseño, la unica implementacion verdadera por el lenguaje comun. Enfocada en desarrrollo continuo a traves de procesos agiles.

DDD provee un framework para diseños tacticos y estrategicos. La estrategia para enfatizar las areas mas importantes a desarrollar basadas en el valor del negocio y la parte tactica que trata sobre patrones y bloques de codigo que lo convierten en facil de testear.


Domain Driven Design es un enfoque para software entregable, que esta enfocado en 3 pilares:

Ubiquitous Lenguaje: Donde los expertos del dominio y los desarrollares trabajan juntos para construir un lenguaje en comun. No existen un "nosotros vs ellos", es siempre sobre NOSOTROS. Desarrollo de software es una inversion de negocio no solo un costo. El esfuerzo en construir un lenguaje ubicuo ayuda a tener un conocimiento profundo del dominio sobre todos los miembros del equipo.

Strategic Design: Ayuda a definir las relaciones internas y dar feedbacks tempranos dels sistema. En el lado tecnico, el diseño estrategico proteje cada servicio de negocio proveyendo la motivacion de como una arquitectura orientada a servicios debería ser lograda.

Tactical Design: DDD provee herramientas y los bloques de construccion por cada iteracion como un codigo entregable. Produce software que es correcto segun los modelos de expertos, hace codigo testeable y menos propenso a errores.

LENGUAJE UBICUO
Junto con los Bounded Contexts(Limites del contexto) el lenguaje ubicuo es uno de las principales fuerzas del DDD.

Bounded context es un concepto de limite sobre un determinado sistema.

El lenguaje ubicuo dentro de un limite tiene un especifico significado contextual. Estos conceptos fuera de este contexto pueden tener un signficado completamente diferente.

Entonces... Como capturamos este lenguaje tan especial?

Etiquetas con nombres por acciones fisica y conceptos de dominio.
Creando un glosario de terminos, con sus definiciones correspondientes.
Compartir y evolucionar el conocimiento recogido con el resto del equipo.

Domain-Driven Design NO ES UNA BALA DE PLATA, como todo, depende del contexto. Como regla de oro, hay que usarlo para simplificar el dominio, nunca para agregar mas complejidad.

Si tu aplicacion tiene pocos casos de uso, evolucionar alrededor de manipulacion de datos y simples operaciones de CRUD, probablemente no necesites DDD.

Si tu aplicacion tiene menos de unsos 30 casos de usos, es probable simplificar usando algun framework como Symfony o Laravel para manejar esta logica de dominio.

Pero si tiene más casos de usos, o si tenemos conocimientos de que el sistema se hara una gran bola de nieve, o si tenemos conocimiento de que crecera con muchas complejidades, deberiamos considerar usar DDD para combatir esta complejidad.

Otro punto a considera no solo si tenemos conocimientos de que va a crecer si no tambien de que a menudo va a estar cambiando, DDD puede ser definitivamente de mucha ayuda manejando la complejidad del sistema y permitiendo refactorizar tu modelo a traves del tiempo.

Si tu no entiendes el dominio, estas trabajando en el porque es nuevo y nadie ha invertido en una solucion antes, esto puede significar que es complejo y suficientes razones para empezar aplicando DDD. Necesitas trabajar muy cerca con los Expertos del DOminio para obtener los modelos de negocio correctos.

PRINCIPALES DESAFIOS
En nuestra jornada aplicando DDD, nos encontraremos con diferentes y desafiantes desafios.
Requiere un pensamiento sobre el dominio del negocio, terminologias, busqueda y colaboracion con los expertos del dominio mas que solo codificar. Por lo que quiere tiempo y esfuerzo.

Necesitas tener un compromiso de los expertos del dominio para involucrarse en el proceso de creación de software. Los necesitas para cubrir el conocimiento profundo del dominio. Esto requiere una abierta, saludable, respetuosa y conitnua conversacion entre los expertos para modelar su lenguaje dentro del software.

Los desarrolladores somos pensadores tecnicos. Soluciones tecnicas son nuestra especialidad. No esta mal, pero el unico problema es que a veces pensar menos tecnicamente es mejor. En orden a pensar en el comportamiento de los objetos, necesitamos pensar en el Lenguaje ubicuo primero.

VALOR DE USAR DDD

La mejor manera de justificar el uso de una tecnica o tecnologia es proveer de valor al negocio. Los princiaples beneficios de aplicar DDD son:

Modelos significativos y utiles del dominio.

Expertos del dominio colaboran en el diseño de software.

Mejor experiencia de usuario.

Limites claros

Mejor organizacion de arquitectura

Modelo basado en agil iterativo y continuo

Mejores herramientas, tanto estrategicas como tacticas.

CONCLUSION
Implentar DDD requiere mucho esfuerzo. Si feura facil todos estarian escribiendo un codigo de excelente calidad, y sabemos que no es asi verdad?

Entonces DDD no es sobre tecnologia, es sobre dar valor en el campo que estas trabajando, dando foco al modelo. Todos forman parte del proceso de descubrir el dominio, desarrolladores, expertos del dominio en equipo para construir el conocimiento basico base compartiendo el mismo lenguaje, Ubiquitous Lenguage.

Nos provee de herramientas de modelado estrategias y tacticas para diseñar con alta calidad de software. Estrategicamente tiene como objetivo la direccion de negocio, ayuda a definir relaciones internas y tecnicamente proteje cada servicio de negocio definiento limites fuertes. Por otro lado el diseño tactico nos provee utiles bloques de contruccion para diseños iterativos.

Nosotros estudiamos el contexto en que DDD tiene sentido. DDD no es una bala de plata para cualquier problema de Software, y depende altamente de la cantidad de complejidad que estas lidiando.

Aplicar DDD es una inversion a largo termino, require esfuerzo activo. Los expertos de dominio seran requeridos para colaborar muy cercamente con los desarrolladores, y los desarrolladores  tendran que pensar en terminos del negocio. Al final, es el cliente el que debe ser complacido.


