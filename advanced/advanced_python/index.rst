.. |==>| unicode:: U+02794 .. thick rightwards arrow

.. default-role:: py:obj

============================================================
Expresiones avanzadas en python (Advanced Python Constructs)
============================================================
:autor: Zbigniew Jędrzejewski-Szmek

En este capítulo se tratan algunas características de Python que se 
podrían considerar como avanzadas --- en el sentido en que no todos los lenguajes
las tienen y, también, en el sentido en que son más útiles en bibliotecas y programas
más complejos pero no en el sentido de ser particularmente especialzadas o particularmente
complicadas.

Es importante subrayar que este capítulo trata, puramente, sobre el lenguaje en sí mismo
--- acerca de características soportadas a través de sintaxis especial complementada
por funcionalidad de la biblioteca estándar (stdlib) de Python la cual 
no podría ser implementada a través de inteligentes módulos externos.

El proceso de desarrollar el lenguaje de programación Python, su sintaxis,
es único porque es muy transparente, los cambios propuestos son evaluados
desde diferentes ángulos y discutidos de forma pública en listas de correo
y la decisión final trata de encontrar un balance entre la importancia de
los casos de uso previstos, la carga de incluir más características al lenguaje,
la consistencia con el resto de la sintaxis y si la variante propuesta es la
más sencilla de leer, escribir y entender. Este proceso se encuentra formalizado
en las propuestas de mejora de Python (PEP_, de ahora en adelante, por sus
siglas en inglés, 'Python Enhanced Proposals'). Como resultado, las características descritas
en este capítulo fueron añadidas posteriormente después de comprobar que
sirven para resolver problemas reales y de que su uso es lo más simple posible.

.. _PEP: http://www.python.org/dev/peps/

.. contents:: Contenidos del capítulo
   :local:
   :depth: 4



Iteradores, expresiones generadoras y generadores
===============================================

Iteradores
^^^^^^^^^

.. sidebar:: Simplicidad

   La duplicación del esfuerzo es un derroche y reemplazar
   varios de los enfoques propios con una característica estándar,
   normalmente, deriva en hacer las cosas más legibles además de más
   interoperable.

                 *Guido van Rossum* --- `Añadiendo tipado estático opcional a Python` (`Adding Optional Static Typing to Python`_)

.. _`Adding Optional Static Typing to Python`:
   http://www.artima.com/weblogs/viewpost.jsp?thread=86641


Un iterador es un objeto adherido al 'protocolo de iterador' (`iterator protocol`_)
--- basicamente esto significa que tiene un método `next <iterator.next>` ('next' por siguiente),
el cual, cuando se le llama, devuelve la siguiente 'pieza' (o 'item') en la secuencia y, cuando
no queda nada para ser devuelto, lanza la excepción 
`StopIteration <exceptions.StopIteration>`.

.. _`iterator protocol`: http://docs.python.org/dev/library/stdtypes.html#iterator-types

Un objeto iterador permite hacer bucles una única vez. Mantiene
el estado (posición) de una iteración individual o, explicado
de otra forma, cada bucle sobre una secuencia requiere un objeto
iterador individual. Esto significa que podemos iterar sobre la misma secuencia
más de una vez de forma concurrente. Separar la lógica de la iteración de la secuencia
permite tener más de una forma diferente de iterar.

La llamada del método `__iter__ <object.__iter__>` en un contenedor es 
la forma más sencilla de tener un objeto iterador. La función `iter` 
hace eso por nosotros ahorrándonos tiempo de tecleado.

>>> nums = [1,2,3]      # notas que ... varía: son objetos diferentes
>>> iter(nums)                           # doctest: +ELLIPSIS
<listiterator object at ...>
>>> nums.__iter__()                      # doctest: +ELLIPSIS
<listiterator object at ...>
>>> nums.__reversed__()                  # doctest: +ELLIPSIS
<listreverseiterator object at ...>

>>> it = iter(nums)
>>> next(it)            # next(obj) simplemente llama a obj.next()
1
>>> it.next()
2
>>> next(it)
3
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration

Cuando se usa en un bucle, finalmente se llama a 
`StopIteration <exceptions.StopIteration>` y se provoca la finalización
del bucle. Pero si se invoca de forma explícita podemos ver que, una vez
que el iterador está 'agotado', al invocarlo nuevamente veremos que se lanza
la excepción comentada anteriormente.

La forma compuesta de bucle `for..in <for>` también usa el método
``__iter__``. Esto nos permite iniciar de forma transparente la 
iteración sobre la secuencia. Pero si ya disponemos del iterador podemos
usarlo en el bucle ``for`` de la misma forma. Para conseguir esto, los iteradores
disponen del método ``__iter__``, además del método ``next``, el cual
devuelve el iterador (``self``).

El soporte para la iteración es dominante en Python:
todas las secuencias y contenedores desordenados que se encuentran
en la biblioteca estándar permiten esto. Este concepto se amplía
a otras cosas: e.g. los objetos ``fichero`` soporta la iteración sobre líneas.

>>> f = open('/etc/fstab')
>>> f is f.__iter__()
True

El ``fichero`` es un iterador en sí mismo y su método ``__iter__`` no crea un objeto separado: 
solo se crea un hilo (thread) de acceso secuencial.

Expresiones generadoras
^^^^^^^^^^^^^^^^^^^^^

Una segunda forma en la cual son creados objetos iteradores es a través de
**expresiones generadoras**, que son la base de la 'comprensión de listas'
(**list comprehensions**). Para aumentar la claridad sobre el tema, una expresión generadora
siempre debe estar encerrada entre paréntesis('()'), corchetes ('[]') o mediante una expresión.
Si se usan paréntesis se crea un generador iterador. En cambio, si se usan corchetes, el proceso
se 'cortocircuita' y obtenemos una ``lista``. ::

    >>> (i for i in nums)                    # doctest: +ELLIPSIS
    <generator object <genexpr> at 0x...>
    >>> [i for i in nums]
    [1, 2, 3]
    >>> list(i for i in nums)
    [1, 2, 3]

En Python 2.7 y 3.x la sintaxis de la comprension de listas se extendió a
**comprensión de diccionarios y conjuntos (sets)**.
Se crea un ``conjunto`` cuando la expresión generadora se encuentra encerrada
por llaves ('{}'). Se crea un ``diccionario`` cuando la expresión generadora
contiene "pares" de la forma ``clave:valor``::

    >>> {i for i in range(3)}   # doctest: +SKIP
    set([0, 1, 2])
    >>> {i:i**2 for i in range(3)}   # doctest: +SKIP
    {0: 0, 1: 1, 2: 4}

Si todavía estás usando alguna de las versiones previas de Python,
la sintaxis es un poco 'peor': ::

    >>> set(i for i in 'abc')
    set(['a', 'c', 'b'])
    >>> dict((i, ord(i)) for i in 'abc')
    {'a': 97, 'c': 99, 'b': 98}

Las expresiones generadoras son bastante sencillas, no hay mucho más
que decir sobre ellas excepto un pequeño añadido: en versiones antiguas de Python
la variable de índexación (``i``) se filtrará (in old Pythons the index variable (i) would leak), 
esto ha sido corregido en versiones >= 3.

Generadores
^^^^^^^^^^

.. sidebar:: Generadores

  Un generador es una función que crea una 
  secuencia de resultados en lugar de una valor individual.

          *David Beazley* --- `A Curious Course on Coroutines and Concurrency`_

.. _`A Curious Course on Coroutines and Concurrency`:
   http://www.dabeaz.com/coroutines/

Una tercera manera de crear objetos iteradores es llamando a la función
generador. Un **generador** es una función que contiene la palabra clave
:simple:`yield`. Hay que destacar que la mera presencia de esta palabra
clave cambia completamente la naturaleza de esta función: esta declaración
``yield`` no debe ser invocada, o incluso alcanzada, pero provoca que la
función sea clasificada como un generador. Cuando se llama a una función
normal se empiezan a ejecutar las instrucciones contenidas en el cuerpo
de esa misma función. Cuando se llama a un generador la ejecución para
después de la primera instrucción contenida en el cuerpo. Una invocación
de una función generadora crea un objeto generador, adheriéndose al 
protocolo del iterador. De la misma forma que en las invocaciones a
funciones normales, se permiten invocaciones concurrentes y recursivas.

Cuando se llama a ``next`` la función se ejecuta hasta el primer ``yield``.
Cada vez que una instrucción ``yield`` da un valor éste se convierte en
el valor de retorno de ``next``. Después de ejecutar la instrucción
``yield``, la ejecución de la función se suspende. ::

    >>> def f():
    ...   yield 1
    ...   yield 2
    >>> f()                                   # doctest: +ELLIPSIS
    <generator object f at 0x...>
    >>> gen = f()
    >>> gen.next()
    1
    >>> gen.next()
    2
    >>> gen.next()
    Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
    StopIteration

Vamos a ver la vida de una invocación individual de una función generadora. ::

    >>> def f():
    ...   print("-- start --")
    ...   yield 3
    ...   print("-- middle --")
    ...   yield 4
    ...   print("-- finished --")
    >>> gen = f()
    >>> next(gen)
    -- start --
    3
    >>> next(gen)
    -- middle --
    4
    >>> next(gen)                            # doctest: +SKIP
    -- finished --
    Traceback (most recent call last):
     ...
    StopIteration

Contrariamente a una función normal, donde la ejecución de
``f()`` provocaría la inmediata ejecución del primer ``print``,
``gen`` se asigna sin ejecutar ninguna de las instrucciones 
presentes en el cuerpo de la función. Solo cuando se invoca
``gen.next()`` por ``next``, se ejecuta la instrucción por encima del primer
``yield``. El segundo ``next`` muestra ``-- middle --`` y la ejecución
se detiene en el segundo ``yield``. El tercer ``next`` muestra
``-- finished --`` y se alcanza el final de la función. Debido a que
no se alcanza un nuevo ``yield`` se lanza una excepción.

¿Qué sucede con la función después de yield, cuando el control pasa al
cliente ('caller')? El estado de cada generador se almacena en el objeto
generador. Desde el punto de vista de la función generadora, casi parece que
esté corriendo en un hilo ('thread') separado pero esto es solo una iusión.:
la ejecución es estrictamente mono-hilo ('single-threaded') pero el intérprete
mantiene y restablece el estado entre las peticiones para que
sea usado por el siguiente valor.

¿Por qué son útiles los generadores? Como se ha visto en las partes 
sobre iteradores, una función generadora es únicamente una forma 
diferente de crear un objeto iterador. Todo lo que se puede hacer
con instrucciones ``yield`` se puede hacer también con métodos ``next``.
Sin embargo, usar una función y dejar que el intérprete haga su magia para
crear un iterador tiene sus ventajas. Una función puede ser mucho más
corta que tener que definir una clase con los métodos requeridos ``next``
e ``__iter__``. Y lo que es más importante, para el creador del
generador es más fácil entender el estado en el cual se mantienen
las variables locales en contraposición a atributos instanciados,
los cuales deben ser usados para pasar datos entre las invocaciones
consecutivas de ``next`` en el objeto iterador.

¿Una pregunta más amplia sería saber por qué los iteradores son útiles?
Cuando un iterador se usa en un bucle, el bucle se convierte en algo muy
simple. El código para inicializar el estado, para decidir si el bucle 
se ha acabado y para encontrar el siguiente valor se extrae de forma
separada. Esto permite destacar el cuerpo del bucle --- la parte interesante.
Además, es posible reusar el código del iterador en otras partes del código.

Comunicación bidireccional
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cada declaración ``yield`` provoca que un valor sea pasado al cliente ('caller').
Esta es la razón para la introducción de los generadores por el :pep:`255` 
(implementado en Python 2.2).  Pero la comunicación en el sentido contrario
también es útil. Una forma obvia sería algún estado externo,
variable global o un objeto mutable compartido. La comunicación
directa es posible gracias al :pep:`342` (implementado in 2.5). Se logró
cambiando la antigua y aburrida declaración ``yield`` a una expresión. 
Cuando el renerador continua la ejecución después de una declaración
``yield``, el cliente ('caller') puede hacer una llamada a un método en
el objeto generador para pasar un valor **hacia** el generador, el cual es
devuelto después por la declaración ``yield``, o un método diferente para
inyectar una excepción al generador.

El primero de los nuevos métodos es `send(value) <generator.send>`, el cual
es similar a `next() <generator.next>`, pero pasa un ``valor`` al generador
que será usado por el valor de la expresión ``yield``. De hecho, ``g.next()`` 
y ``g.send(None)`` son equivalentes.

El segundo de los nuevos métodos es `throw(type, value=None, traceback=None) <generator.throw>`
que es equivalente a::

  raise type, value, traceback

en el lugar de la declaración ``yield``.

A diferencia de :simple:`raise` (que lanza una excepción desde el lugar actual
de ejecución), ``throw()`` primero reanuda el generador y solo entonces lanza 
una excepción. La palabra throw (lanzar, tirar,...) fue seleccionada porque
sería indicativa de colocar la excepción en otro lugar y se asocia con excepciones
en otros lenguajes de programación.

¿Qué sucede cuando una excepción es lanzada dentro del generador?
Puede ser lanzada explícitamente o puede ser lanzada cuando se está 
ejecutando alguna declaración o puede ser inyectada en el lugar de 
una declaración ``yield`` mediante el método ``throw()``. En
cualquier caso, cuando una excepción se propaga de la manera estándar:
podría ser interceptada por una cláusula ``except`` o ``finally`` o, si no,
provoca que se aborte la ejecución de la función generadora y se propaga
en el cliente (caller).

Para completar la sección, merece la pena mencionar que los iteradores
generadores también disponen de un método `close() <generator.close>`, 
el cual puede ser usado para forzar a un generador que de otra manera 
sería capaz de proporcionar más valores
para terminar inmediatamente. Permite al método del generador `__del__ <object.__del__>`
destruir objetos manteniendo el estado del generador.

Vamos a definir un generador que muestra lo que se pasa a través
de send y throw. ::

    >>> import itertools
    >>> def g():
    ...     print '--start--'
    ...     for i in itertools.count():
    ...         print '--yielding %i--' % i
    ...         try:
    ...             ans = yield i
    ...         except GeneratorExit:
    ...             print '--closing--'
    ...             raise
    ...         except Exception as e:
    ...             print '--yield raised %r--' % e
    ...         else:
    ...             print '--yield returned %s--' % ans

    >>> it = g()
    >>> next(it)
    --start--
    --yielding 0--
    0
    >>> it.send(11)
    --yield returned 11--
    --yielding 1--
    1
    >>> it.throw(IndexError)
    --yield raised IndexError()--
    --yielding 2--
    2
    >>> it.close()
    --closing--

.. note:: ``next`` o ``__next__``?

  En Python 2.x, el método iterador para recuperarel siguiente valor
  se llama `next <iterator.next>`. Es invocado de forma explícita a 
  través del a función global `next`, lo que significa que debería
  ser llamado``__next__``. Al igual que la función global `iter` llama
  a `__iter__ <iterator.__iter__>`. Esta inconsistencia se ha corregido
  en Python 3.x, donde ``it.next`` se convierte en ``it.__next__``.  
  Para otros métodos del generador --- ``send`` y ``throw`` --- la
  situación es más compleja because debido a que estos métodos no son
  llamados implícitamente por el intérprete. No obstante, hay una propuesta
  de extensión de la sintaxis que permite a ``continue`` tomar un 
  argumento que será pasado a `send <generator.send>` en el iterador
  del bucle. Si esta extensión es aceptada, es probable que 
  ``gen.send`` se convierta en ``gen.__send__``. El último de los métodos
  de un generador, `close <generator.close>`, ha sido nombrado de forma
  incorrecta de forma obvia ya que es invocado de forma implícita.

Encadenando generadores
^^^^^^^^^^^^^^^^^^^

.. note::

  Esto ha sido implementado en Python 3.3 (`PEP 380: Syntax for Delegating to a Subgenerator`_).

.. _`PEP 380: Syntax for Delegating to a Subgenerator`:
   http://docs.python.org/3/whatsnew/3.3.html#pep-380-syntax-for-delegating-to-a-subgenerator

Digamos que estamos escribiendo un generador y queremos arrojar un número
de valores generados por un segundo generador, un **subgenerador**.
Si la cesión de valores es la única inquietud, se podría realizar sin mucha dificultad
usando un bucle como

.. code-block:: python

  subgen = some_other_generator()
  for v in subgen:
      yield v

Sin embargo, si el subgenerador debe actuar adecuadamente con el 
cliente ('caller') en el caso de llamadas a ``send()``, ``throw()`` 
y ``close()``, las cosas se transforman en algo más complejo. 
La declaración ``yield`` tiene que ser custodiadas por una estructura
:compound:`try..except..finally <try>` similar a la definida en la anterior
sección para "depurar" la función generadora. Este código se encuentra en
:pep:`380#id13`:

.. code-block:: python

   yield from some_other_generator()

Esto se comporta como el bucle explícito mostrado más arriba, arrojando repetidamente
valores desde ``some_other_generator`` hasta que se agota, pero también transmite
``send``, ``throw`` y ``close`` al subgenerador.

Decoradores
==========

.. sidebar:: Resumen

   Esta maravillosa característica del lenguaje apareció casi pidiendo disculpas
   y con la preocupación de que podría resultar poco útil.

                   *Bruce Eckel* --- An Introduction to Python Decorators

.. documentation error:
.. The result must be a class object, which is then bound to the class name.
.. file:///usr/share/doc/python2.7/html/reference/compound_stmts.html
.. >>> def deco(cls):return None
.. ...
.. >>> @deco
.. ... class A: pass
.. ...
.. >>> A
.. >>> type(A)
.. <class 'NoneType'>
.. >>> print(A)
.. None

Debido a que las funciones y clases son objetos, pueden ser distribuidos.
Debido a que son objetos mutables, pueden ser modificados. El acto de 
alterar un objeto función o un objeto clase después de haber sido 
construido pero antes de haber sido delimitado a su nombre se conoce como
decorador.

Hay dos cosas escondidas detrás de un "decorador" --- una es la
función que se encarga de hacer el trabajo de decorador, i.e., la
que realiza el trabajo, y la otra es la expresión que se adhiere a
la sintaxis del decorador, i.e. una @ y el nombre de la función
decoradora.

Una función puede ser decorada usando la sintaxis de los decoradores
para funciones::

    @decorator             # ②
    def function():        # ①
        pass

- Una función se define de la forma estándar. ①
- Una expresión qu comienza con ``@`` colocada antes de la definición
  de la función es el decorador ②. TLa parte después de ``@`` mdebe  ser 
  una expresión simple, normalmente será solo el nombre de una función
  o de una clase. Esta parte será evaluada primero y, después, la función
  definida debajo está lista, el decorador será llamado con objeto función recién
  definido como único parámetro. El valor devuelto por el decorador
  se adjunta al nombre original de la función.

Los decoradores pueden aplicarse a funciones y clases. Para las clases, la semántica
es la misma --- la definición de la clase original se usa como un argumento para llamar
al decorator y lo que sea que devuelva es asignado bajo el nombre original.

Antes de que fuera implementada la sintaxis del decorador (:pep:`318`), era
posible conseguir el mismo efecto asignando la función o la clase o una variable
temporal para después invocar al decorador explícitamente que, finalmente, 
asignaba el valor devuelto al nombre de la función. Esto parece que implica
mucho tecleo, como realmente sucede, además de tener que repetir la función decorada
como una variable temporal al menos tres veces, lo que puede provocar errores.
El ejemplo anterior es equivalente a::

    def function():                  # ①
        pass
    function = decorator(function)   # ②

Los decoradores puden ser apilados --- el orden de aplicación es de abajo a arriba
o de dentro hacia afuera. La semántica sería de la siguiente forma, la
función originalmente definida se usa como argumento para el primer decorador,
lo que sea que devuelva el primer decorador se usa como argumento para el
segundo decorador, ..., y lo que sea que devuelva el último decorador se
adjunta bajo el nombre de la función original.

La sintaxis de los decoradores fue elegida por su legibilidad.
Debido a que el decorador se especifica antes que la cabecera de 
la función, resulta obvio que no es parte del cuerpo de la función
y está claro que solo puede operar sobre la función completa.
Ya que la expresión está prefijada con ``@`` se encuentra resaltada
y es difícil pasarla por alto ("en tu cara",
de acuerdo al PEP :) ). Cuando se aplica más de un decorador,
cada uno se emplaza en una línea para que sea de fácil lectura.


Reemplazando o modificando el objeto original
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Los decoradores pueden devolver tanto el mismo objeto función u objeto clase
como pueden devolver un objeto completamente diferente. En el primer caso,
el decorador puede aprovecharse del hecho de que un objeto función o un objeto clase
son mutables y se les pueden añadir atributos, e.g. se le podría añadir documentación
(`docstring`) a una clase. Un decorador podría hacer algo útil incluso sin llegar
a modificar el objeto, por ejemplo, registrar la clase decorada en un registro global.
En el segundo caso, cualquier cosa es posible a priori: cuando algo diferente 
es sustituido por la función o clase original, el nuevo objeto puede ser totalmente
diferente. Sin embargo, este comportamiento no refleja el propósito de los decoradores:
El objetivo principal de un decorador es alterar el objeto decorado, no realizar 
algo impredecible. Por tanto, cuando una función se encuentra "decorada", siendo reemplazada
con una función diferente, la nueva función, normalmente, llama a la función original, después
de realizar algo de trabajo de preparación. De la misma forma, cuando una clase ha sido 
"decorada", siendo reemplazado con otra clase, la nueva clase, normalmente, deriva de la 
clase original. Cuando el propósito de la función decoradora (o decorador) es hacer algo
"siempre que se llama a la función decorada", como por ejemplo registrar cada llamada
a la función decorada, solo podrían ser usados el segundo tipo de decoradores. Por otra
parte, si el primer caso es suficiente, sería recomendable usarlo puesto que es más
simple.

Decoradores implementados como clases y funciones
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El único requerimiento de los decoradores es el siguiente, solo pueden ser
llamados con un único argumento. Esto significa que los decoradores pueden
ser implementados como funciones normales o como clases con un método
`__call__ <object.__call__>` o, en teoría, incluso como funciones lambda.

Vamos a comparar los usos de decorador como función o como clase. 
La expresión decoradora (la parte que se encuentra inmediatamente
después de ``@``) puede ser solo un nombre o puede ser una llamada. 
La forma de uso con solo el nombre es mejor (menos a escribir, 
queda más limpio) pero solo es posible usarla cuando no se necesitan
argumentos para personalizar el decorador. Los decoradores escritos
como funciones pueden ser usados en los dos siguientes casos:

>>> def simple_decorator(function):
...   print u"haciendo una decoración"
...   return function
>>> @simple_decorator
... def function():
...   print u"dentro de la función"
haciendo una decoración
>>> function()
dentro de la función

>>> def decorador_con_argumentos(arg):
...   print u"definiendo un decorador"
...   def _decorador(function):
...       # en esta función interna, arg también está disponible
...       print u"haciendo una decoración,", arg
...       return function
...   return _decorador
>>> @decorador_con_argumentos("abc")
... def function():
...   print u"dentro de la función"
definiendo un decorador
haciendo una decoración, abc
>>> function()
dentro de la función

Los dos ejemplos de decoradores triviales mostrados en el código de más arriba
se encuentran en la categoría de decoradores que devuelven la función original.
Si tuvieran que devolver otra función, sería necesario otro nivel extra 
de anidameniento. En el peor de los casos, tres niveles de funciones anidadas.

>>> def reemplazando_decorador_con_argumentos(arg):
...   print u"definiendo el decorador"
...   def _decorador(function):
...       # en esta función interna, arg también está disponible
...       print u"haciendo una decoración,", arg
...       def _envoltorio(*args, **kwargs):
...           print u"dentro del envoltorio,", args, kwargs
...           return function(*args, **kwargs)
...       return _envoltorio
...   return _decorador
>>> @reemplazando_decorador_con_argumentos("abc")
... def function(*args, **kwargs):
...     print u"dentro de la función,", args, kwargs
...     return 14
definiendo el decorador
haciendo la decoración, abc
>>> function(11, 12)
dentro del envoltorio, (11, 12) {}
dentro de la función, (11, 12) {}
14

La función ``_envoltorio`` (``_wrapper`` en inglés) se defina para que
acepte todos los argumentos de las palabras clave (`keywords`) posicionales. 
En general, desconocemos los argumentos que podría aceptar la función decorada, 
por tanto, la función envoltorio lo único que hacer es pasar todos los argumentos
a la función envuelta. Una consecuencia desafortunada es que la aparente lista
de argumentos es poco orientativa.

Comparados con los decoradores que se definen como una función, complejos decoradores
definidos como clases son más simples. Cuando se crea un objeto, al método
`__init__ <object.__init__>` solo se le permite devolver `None`
y el tipo del objeto creado no puede ser modificado. Esto significa que
cuando un decorador se encuentra definido como una clase, no tiene mucho
sentido usar la forma sin argumentos: el objeto final decorado sería solamente
una instancia de la clase decorada, devuelto por la llamada al constructor,
lo cual no es muy útil. Por tanto, sería suficiente que discutiéramos decoradores
basados en clases en los que los argumentos son dados en la expresión decoradora
 y el método decorador ``__init__`` se usa para la construcción del decorador.

>>> class clase_decoradora(object):
...   def __init__(self, arg):
...       # este método será llamado en la expresión decoradora
...       print "in decorador init,", arg
...       self.arg = arg
...   def __call__(self, function):
...       # Este método será llamado para que haga el trabajo
...       print "in decorator call,", self.arg
...       return function
>>> deco_instance = clase_decoradora('foo')
in decorator init, foo
>>> @deco_instance
... def function(*args, **kwargs):
...   print u"en la función,", args, kwargs
en la función call, foo
>>> function()
in function, () {}

Al contrario que lo que establecen las reglas normales (:PEP:`8`), 
los decoradores escritos como clases se comportan más como funciones y,
por tanto, su nombre, a veces, comienza con una letra minúscula.

En realidad, no tiene mucho sentido crear una clase nueva solo para tener
un decorador que devuelve la función original. Se supone que los objetos
mantienen el estado y estos decoradores son más útiles cuando el decorador
devuelve un nuevo objeto.

>>> class replacing_decorator_class(object):
...   def __init__(self, arg):
...       # Este método será llamado en la expresión decoradora
...       print "in decorator init,", arg
...       self.arg = arg
...   def __call__(self, function):
...       # este método será llamado para hacer el trabajo
...       print "in decorator call,", self.arg
...       self.function = function
...       return self._wrapper
...   def _wrapper(self, *args, **kwargs):
...       print "in the wrapper,", args, kwargs
...       return self.function(*args, **kwargs)
>>> deco_instance = replacing_decorator_class('foo')
in decorator init, foo
>>> @deco_instance
... def function(*args, **kwargs):
...   print "in function,", args, kwargs
in decorator call, foo
>>> function(11, 12)
in the wrapper, (11, 12) {}
in function, (11, 12) {}

Un decorador como este puede hacer casi cualquier cosa, ya que puede modificar
el objeto función original y 'machacar' los argumentos, llamar a la función
original o no y, después, 'machacar' el valor de retorno.

Copiando el docstring (documentación) y otros atributos de la función original
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando un decorador devuelve una nueva función que reemplaza a
la función original nos encontramos que, desafortunadamente,
hemos perdido el nombre original de la función, el docstring original y la lista 
de argumentos originales. Podríamos traspasar todos esos atributos de la función original
a la nueva función poniendo ``__doc__`` (el docstring), ``__module__``
y ``__name__`` (el nombre completo de la función) y
``__annotations__`` (información extra sobre los argumentos y los valores
de retorno de la función disponible en Python 3). Esto se puede hacer de forma automática
usando `functools.update_wrapper`.

.. sidebar:: `functools.update_wrapper(wrapper, wrapped) <functools.update_wrapper>`

   "Actualiza una función envoltorio (wrapper) para que se parezca a la función que 
    está envolviendo (wrapped function)."

>>> import functools
>>> def better_replacing_decorator_with_args(arg):
...   print "defining the decorator"
...   def _decorator(function):
...       print "doing decoration,", arg
...       def _wrapper(*args, **kwargs):
...           print "inside wrapper,", args, kwargs
...           return function(*args, **kwargs)
...       return functools.update_wrapper(_wrapper, function)
...   return _decorator
>>> @better_replacing_decorator_with_args("abc")
... def function():
...     "extensive documentation"
...     print "inside function"
...     return 14
defining the decorator
doing decoration, abc
>>> function                           # doctest: +ELLIPSIS
<function function at 0x...>
>>> print function.__doc__
extensive documentation

Una cosa importante que echamos en falta en la lista de atributos y que
podría ser copiada en la función de reemplazo: la lista de argumentos.
Los valores por defecto para los argumentos se pueden modificar a través
de los atributos ``__defaults__``, ``__kwdefaults__`` pero, desafortunadamente,
la lista de argumentos no puede ser un atributo por si misma. Esto significa que
``help(function)`` mostrará una lista de argumentos poco útil que será confusa para
el usuario de la función. Una forma fea pero efectiva para sortear este problema
sería crear un ``wrapper`` de forma dinámica usando ``eval``. Esto se podría
automatizar usando el módulo externo ``decorator``. Este módulo proporciona
soporte para el decorador ``decorator``, que toma un ``wrapper`` y lo convierte
en un decorador que preserva la ``firma`` de la función.

Resumiendo, los decoradores deberían usar siempre ``functools.update_wrapper``
o cualquier otra manera de poder copiar los atributos de las funciones.

Ejemplos en la librería estándar
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Primero de todo deberíamos destacar el hecho de que hay muchos
decoradores útiles en la librería estándar. Hay tres decoradores
que son parte importante del lenguaje:

- `classmethod` provoca que un método se convierta en una "class method",
  que significa que un método pueda ser invocado sin necesidad de crear
  una instancia de la clase. Cuando se invoca un método normal, el intérprete
  inserta el objeto instancia como el primer parámetro posicional ``self``. 
  Cuando se invoca un ``class method``, la clase misma será el primer parámetro,
  a menudo llamado ``cls``.

  Los ``Class methods`` siguen siendo accesibles a través del ``namespace`` de la clase
  y de esa forma no se contamina el ``namespace`` del módulo. Los ``Class methods``
  pueden ser usados como contructores alternativos::

    class Array(object):
        def __init__(self, data):
	    self.data = data

        @classmethod
        def fromfile(cls, file):
            data = numpy.load(file)
            return cls(data)

  Esta forma es más limpia que usar múltiples ``flags`` para ``__init__``.

- `staticmethod` se aplica a métodos para convertirlos en estáticos,
  i.e. básicamente una función normal pero accessibles a través del 
  namespace de la clase. Esto resulta útil cuando la función solo es necesaria
  dentro de la clase (su nombre estaría prefijado con ``_``) o cuando queremos 
  que el usuario piense en el método conectado/relacionado con su clase, a pesar
  de que esto no es un requerimiento de su implementación.

- `property` es la respuesta pythónica al problema de los ``getters`` y los 
  ``setters``. Un método decorado con ``property`` se convierte en un ``getter``
  que es invocado automáticamente en el acceso al atributo.

  >>> class A(object):
  ...   @property
  ...   def a(self):
  ...     "an important attribute"
  ...     return "a value"
  >>> A.a                                   # doctest: +ELLIPSIS
  <property object at 0x...>
  >>> A().a
  'a value'

  En este ejemplo, ``A.a`` es un atributo de solo lectura. Además está documentado:
  ``help(A)`` incluye el docstring para el atributo ``a``
  tomado del método ``getter``. Si definimos ``a`` como una propiedad podremos calcularla
  al vuelo y tiene el efecto colateral de hacerla de solo lectura ya que no se define
  ningún ``setter``.

  Para disponer de un ``setter`` y un ``getter``se requieren dos métodos,
  obviamente. A partir de Python 2.6 la sintaxis de preferencia sería la siguiente::

    class Rectangle(object):
        def __init__(self, edge):
            self.edge = edge

        @property
        def area(self):
            """Computed area.

            Setting this updates the edge length to the proper value.
            """
            return self.edge**2

        @area.setter
        def area(self, area):
            self.edge = area ** 0.5

  La forma de funcionar de esto sería la siguiente: el decorador ``property`` 
  reemplaza el método ``getter`` con un objeto ``property``. Este objeto dispone de
  tres métodos, ``getter``, ``setter`` y ``deleter``, que podrían ser usados
  como decoradores. Su trabajo es establecer el ``getter``, el ``setter`` 
  y el ``deleter`` del objeto ``property`` (almacenados como los atributos ``fget``,
  ``fset`` y ``fdel``). El ``getter`` se puede establecer como en el ejemplo
  de más arriba, cuando se crea el objeto. Cuando se define el `` setter`` ya se dispone
  del objeto ``property`` en ``area`` y le añadimos el ``setter`` usando el método ``setter``. 
  Todo esto ocurre cuando estamos creando la clase.

  Después de que se haya creado una instancia de la clase, el objeto ``property`` 
  es un objeto especial. Cuando el intérprete ejecuta el acceso al atributo, la asignación o la
  eliminación el trabajo se delega a los métodos del objeto ``property``.

  Para clarificar lo anterior vamos a definir un ejemplo "debug"::

    >>> class D(object):
    ...    @property
    ...    def a(self):
    ...      print "getting", 1
    ...      return 1
    ...    @a.setter
    ...    def a(self, value):
    ...      print "setting", value
    ...    @a.deleter
    ...    def a(self):
    ...      print "deleting"
    >>> D.a                                    # doctest: +ELLIPSIS
    <property object at 0x...>
    >>> D.a.fget                               # doctest: +ELLIPSIS
    <function a at 0x...>
    >>> D.a.fset                               # doctest: +ELLIPSIS
    <function a at 0x...>
    >>> D.a.fdel                               # doctest: +ELLIPSIS
    <function a at 0x...>
    >>> d = D()               # ... varies, this is not the same `a` function
    >>> d.a
    getting 1
    1
    >>> d.a = 2
    setting 2
    >>> del d.a
    deleting
    >>> d.a
    getting 1
    1

  Hay que echarle un poco de imaginación para relacionar las ``Properties`` y la sintaxis de un decorador. 
  Se viola una de las premisas de la sintaxis de los decoradores --- en donde el nombre no está duplicado
  --- pero no se ha conseguido inventar nada mejor. Es un buen uso usar el mismo nombre para los métodos
  ``getter``, ``setter`` y ``deleter``.

  .. en la documentación de ``property`` se menciona que esto solo funciona para las
     clases antiguas (old-style) pero parece que alquien ha cometido un error.

Algunos ejemplos más nuevos incluirían:

- `functools.lru_cache` memoriza una función arbitraria
  manteniendo una ``caché`` limitada de argumentos:respuesta pares (Python 3.2)

- `functools.total_ordering` is a class decorator which fills in
  missing ordering methods
  (`__lt__ <object.__lt__>`, `__gt__ <object.__gt__>`,
  `__le__ <object.__le__>`, ...)
  based on a single available one (Python 2.7).


..
  - `packaging.pypi.simple.socket_timeout` (en Python 3.3) agrega un ``socket`` 
     ``timeout`` cuando se descargan datos a través de un ``socket``.


Funciones obsoletas (Deprecation of functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Digamos que queremos mostrar un aviso de que una función será discontinuada (``deprecating``)
en ``stderr`` cuando invoquemos por primera vez una función que ya no queremos. Si no queremos
modificar la función podríamos usar un decorador::

  class deprecated(object):
      """Print a deprecation warning once on first use of the function.

      >>> @deprecated()                    # doctest: +SKIP
      ... def f():
      ...     pass
      >>> f()                              # doctest: +SKIP
      f is deprecated
      """
      def __call__(self, func):
	  self.func = func
	  self.count = 0
	  return self._wrapper
      def _wrapper(self, *args, **kwargs):
	  self.count += 1
	  if self.count == 1:
	      print self.func.__name__, 'is deprecated'
	  return self.func(*args, **kwargs)

.. TODO: use update_wrapper here

Esto se puede implementar también como una función::

  def deprecated(func):
      """Print a deprecation warning once on first use of the function.

      >>> @deprecated                      # doctest: +SKIP
      ... def f():
      ...     pass
      >>> f()                              # doctest: +SKIP
      f is deprecated
      """
      count = [0]
      def wrapper(*args, **kwargs):
          count[0] += 1
          if count[0] == 1:
              print func.__name__, 'is deprecated'
          return func(*args, **kwargs)
      return wrapper

Un decorador para eliminar bucles ``while``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pensemos en una función que nos devuelve una lista de cosas mediante un bucle
while dentro de la función. Si desconocemos cuantos objetos serán necesarios, 
una forma estándar de hacer esto sería::

  def find_answers():
      answers = []
      while True:
	  ans = look_for_next_answer()
	  if ans is None:
	      break
	  answers.append(ans)
      return answers

Esto es correcto mientras el cuerpo del bucle sea compacto. Desde el momento en
que el bucle se convierte en algo más complejo, como a menudo sucede en
el código real, tendremos algo poco legible. Podríamos simplificar eso usando
una delcaración ``yield`` pero entonces el usuario tendría que hacer una llamada
explícita a ``list(find_answers())``.

Podemos definir un decorador que nos contruya la lista::

  def vectorized(generator_func):
      def wrapper(*args, **kwargs):
	  return list(generator_func(*args, **kwargs))
      return functools.update_wrapper(wrapper, generator_func)

Y nuestra función se convertirá en::

  @vectorized
  def find_answers():
      while True:
	  ans = look_for_next_answer()
	  if ans is None:
	      break
	  yield ans

Un sistema de registro de ``plugins``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Esta es una clase decoradora que no modifica la clase, simplemente la coloca en un 
registro global. Pertenece a la categoría de decoradores que 
devuelven la función original::

  class WordProcessor(object):
      PLUGINS = []
      def process(self, text):
          for plugin in self.PLUGINS:
              text = plugin().cleanup(text)
          return text

      @classmethod
      def plugin(cls, plugin):
          cls.PLUGINS.append(plugin)

  @WordProcessor.plugin
  class CleanMdashesExtension(object):
      def cleanup(self, text):
          return text.replace('&mdash;', u'\N{em dash}')

Aquí hemos usado un decorador para descentralizar el registro de 
plugins. Llamamos a nuestro decorador con un nombre en lugar de con un verbo ya que lo usamos
para declarar que nuestra clase es un plugin para ``WordProcessor``. El método 
``plugin`` simplemente anexa la clase a la lista de plugins.

Unas palabras acerca del propio plugin: reemplaza entidades HTML para enfatizar el texto 
(``em-dash``) con el carácter Unicode para enfatizar. Se aprovecha de la
'notación literal unicode'_ para insertar un carácter usando su nombre en la 
base de datos unicode ("EM DASH"). Si el carácter Unicode fue introducido
directamente sería imposible distinguirlo de un texto enfatizado en la fuente
del programa.

.. _`notación literal unicode`:
   http://docs.python.org/2.7/reference/lexical_analysis.html#string-literals

Más ejemplos y lecturas recomendadas
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* :pep:`318` (function and method decorator syntax)
* :pep:`3129` (class decorator syntax)
* http://wiki.python.org/moin/PythonDecoratorLibrary
* http://docs.python.org/dev/library/functools.html
* http://pypi.python.org/pypi/decorator
* Bruce Eckel

  - `Decorators I`_: Introduction to Python Decorators
  - `Python Decorators II`_: Decorator Arguments
  - `Python Decorators III`_: A Decorator-Based Build System

.. _`Decorators I`: http://www.artima.com/weblogs/viewpost.jsp?thread=240808
.. _`Python Decorators II`: http://www.artima.com/weblogs/viewpost.jsp?thread=240845
.. _`Python Decorators III`: http://www.artima.com/weblogs/viewpost.jsp?thread=241209


Gestores de contexto (``context managers``)
===========================================

Un gestor de contexto es un objeto con los métodos `__enter__ <object.__enter__>` y
`__exit__ <object.__exit__>` los cuales pueden ser usados en la declaración
:compound:`with`::

  with manager as var:
      do_something(var)

es, en el más simple de los casos, equivalente a::

  var = manager.__enter__()
  try:
      do_something(var)
  finally:
      manager.__exit__()

En otras palabras, el protocolo de gestor de contextos definidos en el :pep:`343`
permite la extracción de la parte aburrida de la estructura
:compound:`try..except..finally <try>` en una clase separada manteniendo solo
el bloque de interés ``do_something``.

1. Primero se llama al método `__enter__ <object.__enter__>`. Puede devolver un
   valor que será asignado a ``var``.
   La parte ``as`` es opcional: si no está presente, el valor devuelto
   por ``__enter__`` será ignorado.
2. El bloque de código bajo ``with`` se ejecutará.  Como si fueran condiciones
   ``try``. Se podrá ejecutar con éxito hasta el final o, si algo falla, puede
   :simple:`break`, :simple:`continue`` o :simple:`return` o
   lanzar una excepción. Pase lo que pase, una vez que el bloque ha finalizado, se 
   producirá una llamada al método `__exit__ <object.__exit__>`.
   Si se lanzó una excepción, la información se manda a ``__exit__``, el cual se describe
   en la siguiente subsección. En el caso normal, las excepciones podrían ser ignoradas
   como si fueran una condición ``finally`` y serían relanzadas después de que
   finalice ``__exit__``.

Pensemos que nos queremos asegurar de que un fichero se ha cerrado inmediatamente
después de que hayamos escrito en él::

  >>> class closing(object):
  ...   def __init__(self, obj):
  ...     self.obj = obj
  ...   def __enter__(self):
  ...     return self.obj
  ...   def __exit__(self, *args):
  ...     self.obj.close()
  >>> with closing(open('/tmp/file', 'w')) as f:
  ...   f.write('the contents\n')

Aquí hemos hecho que la llamada a ``f.close()`` se haga después de que se salga 
del bloque ``with``. Ya que el cerrar ficheros es una operación tan común,
el soporte para esto ya está presente en la clase ``file``. 
Dispone de un método ``__exit__`` que llama a ``close`` y que podría
ser usado como un gestor de contexto::

  >>> with open('/tmp/file', 'a') as f:
  ...   f.write('more contents\n')

El uso común de ``try..finally`` está liberando recursos. Se han implementado
diferentes casos: en la fase ``__enter__`` se adquiere el recurso y en la fase
``__exit__``  se libera el recurso. La excepción, en el caso de que se lanzase,
se propagaría. De la misma forma que lo visto para los ficheros, existen otras
operaciones naturales a relaizar después de que el objeto haya sido usado y es más
conveniente tener el soporte ya creado. Con cada lanzamiento, Python proporciona
soporte en más lugares:

* todos los objetos similares a ficheros:

  - `file` |==>| cerrado automáticamente
  - `fileinput`, `tempfile` (py >= 3.2)
  - `bz2.BZ2File`, `gzip.GzipFile`,
    `tarfile.TarFile`, `zipfile.ZipFile`
  - `ftplib`, `nntplib` |==>| cierra conexión (py >= 3.2 o 3.3)
* cierres (`locks`)

  - `multiprocessing.RLock` |==>| cierra y apertura
  - `multiprocessing.Semaphore`
  - `memoryview` |==>| lanzamiento automático (py >= 3.2 y 2.7)
* `decimal.localcontext` |==>| modifica la precisión de cálculos de forma temporal
* `_winreg.PyHKEY <_winreg.OpenKey>` |==>| abre y cierra la `hive key`
* `warnings.catch_warnings` |==>| deshabilita temporalmente los avisos
* `contextlib.closing` |==>| lo mismo que en el ejemplo de más arriba, llama a ``close``
* programación paralela

  - `concurrent.futures.ThreadPoolExecutor` |==>| invocar en paralelo y después matar el `thread pool` (py >= 3.2)
  - `concurrent.futures.ProcessPoolExecutor` |==>| invocar en paralelo y después matar el `process pool` (py >= 3.2)
  - `nogil` |==>| evitar el problema del GIL temporalmente (solo en cython :( )


Capturando excepciones
^^^^^^^^^^^^^^^^^^^^^^

Cuando se lanza una excepción en el bloque ``with``, es pasada como
argumentos a ``__exit__``. Se usan tres argumentos, el mismo que es
devuelto por :py:func:`sys.exc_info`: tipo, valor y `traceback`. Cuando no se lanza
una excepción, se usará ``None`` para los tres argumentos.  El gestor de contexto
puede "tragarse" la excepción devolviendo un valor real desde ``__exit__``. 
Las excepciones son sencillas de ignorar ya que si
``__exit__`` no usa ``return`` y llega la final se devolverá ``None``, 
un valor falso, y, por tanto, la excepción será relanzada después de que
finalice ``__exit__``.

La habilidad para capturar excepciones proporciones posibilidades muy interesantes. 
Un ejemplo clásico proviene de ``unit-tests`` --- queremos asegurarnos que determinado
código lanza determinada excepción de forma correcta::

  class assert_raises(object):
      # based on pytest and unittest.TestCase
      def __init__(self, type):
          self.type = type
      def __enter__(self):
          pass
      def __exit__(self, type, value, traceback):
          if type is None:
              raise AssertionError('exception expected')
          if issubclass(type, self.type):
              return True # swallow the expected exception
          raise AssertionError('wrong exception type')

  with assert_raises(KeyError):
      {}['foo']

Usindo generadores para definir gestores de contexto
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando discutimos sobre los generadores_, se dijo que se se prefería los generadores
a los iteradores implementados como clases debido a que son más cortos, más dulces
y el estado se almacena como local, no instancias o variables.
or otra parte, como se describió en `comunicación bidireccional`_,
el flujo de datos entre el generador y el òbjeto`que lo llama puede ser
bidireccional. Esto incluye las excepciones, las cuales pueden ser lanzadas
en un generador. Nos gustaría implementar gestores de contexto como funciones generadoras
especiales. De hecho, el protocolo de los generadores se diseño para
soportar estos casos de uso.

.. code-block:: python

  @contextlib.contextmanager
  def some_generator(<arguments>):
      <setup>
      try:
	  yield <value>
      finally:
	  <cleanup>

El `helper` `contextlib.contextmanager` toma un generador y lo convierte
en un gestor de contenidos. El generador debe obedecer algunas normas
forzadas por la función envoltorio (`wrapper`) --- debe usar
``yield`` exactamente una única vez. La parte anterior al ``yield`` 
se ejecuta a partir de ``__enter__``, el bloque de código protegido
por el gestor de contexto se suspende en ``yield`` y el resto se
ejecuta en ``__exit__``. Si se lanza una excepción, el intérprete se lo pasa
al `wrapper` a través de los argumentos de ``__exit__`` y la función
`wrapper` lo apunta hacia la declaración ``yield``. 
A través del uso de generadores los gestores de contexto son más cortos y simples.

Vamos a reescribir el ejemplo ``closing`` como un generador::

  @contextlib.contextmanager
  def closing(obj):
      try:
	  yield obj
      finally:
	  obj.close()

Vamos a reescribir el ejemplo ``assert_raises`` como un generador::

  @contextlib.contextmanager
  def assert_raises(type):
      try:
	  yield
      except type:
	  return
      except Exception as value:
	  raise AssertionError('wrong exception type')
      else:
	  raise AssertionError('exception expected')

¡Aquí usamos un decorador para transformar un generador en un gestor de contexto!
