Computación científica con herramientas y flujos de trabajo
===========================================================

:autores: Fernando Pérez, Emmanuelle Gouillart, Gaël Varoquaux, Valentin Haenel

..
    .. image:: phd053104s.png
      :align: center

¿Por qué Python?
----------------

¡Cuáles son las necesidades de los científicos?
...............................................

* Obtener datos (simulación, experimentos)

* Manipular y procesar datos.

* Visualizar resultados... ¡para ver qué estamos haciendo!

* Presentar resultados: generar figuras para reportes o publicaciones y
  hacer presentaciones.

Especificaciones
................

* Conjunto completo de **piezas** correspondiente a métodos numéricos
  clásicos o acciones básicas: no queremos re-programar el gráfico de una
  curva, una transformada de Fourier o un algoritmo de ajuste. ¡No
  reinventes la rueda!

* Fácil de aprender: las ciencias de la computación no son ni nuestro trabajo
  ni nuestra formación. Queremos ser capaces de graficar una curva, suavizar
  una señal o hacer una transformada de Fourier en unos pocos minutos.

* Fácil comunicación con colaboradores, estudiantes y clientes para hacer del
  código algo vivo dentro de un laboratorio o una compañía: el código debería
  ser tan entendible como un libro. El lenguaje debería contener la menor
  cantidad de sintaxis simbólica o rutinas innecesarias que puedan distraer
  al lector del entendimiento matemático o científico del código.

* Código eficiente que se ejecute rápido... pero no es necesario decir que un
  código muy rápido se vuelve inútil si perdemos mucho tiempo en escribirlo.
  Por lo tanto, necesitamos rapidez tanto en el desarrollo como en el tiempo
  de ejecución.

* La idea es evitar tener que aprender un nuevo software para cada problema nuevo.
  Un ambiente/lenguaje para todo, siempreque sea posible.

Soluciones existentes
.....................

¿Qué soluciones usan los científicos para trabajar?

**Lenguajes compilados: C, C++, Fortran, etc.**

* Ventajas:

  * Muy rápidos. Compiladores muy optimizados. Para cálculos intensivos es
    difícil escapar de estos lenguajes.

  * Algunas bibliotecas científicas muy optimizadas han sido escritas para
    estos lenguajes. Ejemplo: BLAS (operaciones vectoriales y matriciales)

* Desventajas:

  * Difícil uso: no hay interactividad durante el desarrollo,
    pasos de compilación obligatorios, sintaxis compleja (&, ::, }}, ;, etc.),
    manejo de uso de memoria manual (difícil en C). Estos son **lenguajes
    difíciles** para científicos ajenos a las ciencias de la computación.

**Lenguajes de script: Matlab**

* Ventajas:

  * Conjunto de bibliotecas muy completas con numerosos algoritmos para temáticas
    muy diferentes. Rápida ejecución debido a que estas bibliotecas por lo general
    están escritas en un lenguaje compilado.

  * Cómodo ambiente de desarrollo: ayuda completa y bien organizada, editor
    integrado, etc.

  * Soporte comercial disponible.

* Desventajas:

  * El lenguaje es pobre y puede llegar a ser restrictivo para usuarios
    avanzados.

  * No es gratuito ni libre.

**Otros lenguajes de script: Scilab, Octave, Igor, R, IDL, etc.**

* Ventajas:

  * Son de código abierto, gratuitos o al menos más baratos que Matlab.

  * Algunas características pueden ser muy avanzadas (estadística en R,
    gráficas en Igor, etc.).

* Desventajas:

  * Menos algoritmos disponibles que Matlab y el lenguaje no es más avanzado.

  * Algunos softwares están muy enfocados a una temática concreta. Por ejemplo, Gnuplot o
    xmgrace para graficar curvas. Estos programas son muy poderosos, pero están
    restringidos a un solo tipo de uso, como por ejemplo para hacer gráficos.

**¿Qué hay de Python?**

* Ventajas:

  * Bibliotecas de cálculo científico muy completas (aunque un poco menos que
    en Matlab).

  * Lenguaje bien pensado, lo que permite escribir código muy legible y bien
    estructurado: "escribimos el código que pensamos".

  * Existen muchas bibliotecas para otras tareas, además de para cálculo científico
    (administración de servidores web, acceso al puerto serie, etc.).

  * Software libre y de acceso abierto, ampliamente difundido con una
    comunidad activa y vibrante.

* Desventajas:

  * Ambiente de desarrollo menos placentero que, por ejemplo, Matlab.
    (Más orientado a los geeks).

  * No se pueden encontrar todos los algoritmos en softwares o toolboxes más
    especializados.

Bloques de construcción para Python científico
----------------------------------------------

Al contrario de Matlab, Scilab o R, Python no viene con un conjunto de
módulos para cálculo científico. A continuación se muestran los bloques
o librerías básicas que, correctamente combinados, permiten obtener un 
completo ambiente de desarrollo para cálculo científico:

* **Python**, un lenguaje computacional genérico y moderno

    * Lenguaje Python: tipos de datos (``string``, ``int``), control de flujo,
      estructuras de datos (listas, diccionarios), patrones, etc.

    * Módulos de la biblioteca estándar.

    * Un gran número de módulos especializados o aplicaciones escritas en
      Python: protocolos web, framework para aplicaciones web, etc. ... y
      cálculo científico.

    * Herramientas de desarrollo (pruebas automáticas, generación de
      documentación)

  .. image:: snapshot_ipython.png
        :align: right
        :scale: 40

* **IPython**, una **consola de Python** avanzada http://ipython.org/

* **Numpy** : proporciona poderosos objetos de **arrays numéricos** y rutinas
  para manipularlos. http://www.numpy.org/

..
    >>> import numpy as np
    >>> np.random.seed(4)

* **Scipy** : rutinas de alto nivel para procesamiento de datos.
  Optimización, regresión, interpolación, etc. http://www.scipy.org/

  .. image:: random_c.jpg
        :scale: 40
        :align: right

* **Matplotlib** : visualización bidimensional, gráficos "listos para publicar"
  http://matplotlib.sourceforge.net/

  |clear-floats|

  .. image:: example_surface_from_irregular_data.jpg
        :scale: 60
        :align: right

* **Mayavi** : visualización tridimensional
  http://code.enthought.com/projects/mayavi/

  |clear-floats|


El flujo de trabajo interactivo: IPython y un editor de textos
--------------------------------------------------------------

**Trabajo interactivo para probar y entender algoritmos**: En esta
sección, describiremos un flujo de trabajo interactivo con
`IPython <http://ipython.org>`__ que es práctico para explorar y
entender algoritmos.

Python es un lenguaje multipropósito. Como tal, no existe un ambiente
bendito para trabajar ni hay una sola manera de usarlo. Aunque esto hace
que sea más difícil para principiantes encontrar su propio camino, esto hace
posible que Python sea usado para escribir programas, en servidores web o
dispositivos embebidos.

.. note:: Documento de referencia para esta sección:

    **IPython user manual:** http://ipython.org/ipython-doc/dev/index.html

Interacción en la línea de comandos
...................................

Iniciar `ipython`:

.. sourcecode:: ipython

    In [1]: print('Hola mundo')
    Hola mundo

Obteniendo ayuda usando el operador **?** después de un objeto:

.. sourcecode:: ipython

    In [2]: print?
    Type:		builtin_function_or_method
    Base Class:	        <type 'builtin_function_or_method'>
    String Form:	<built-in function print>
    Namespace:	        Python builtin
    Docstring:
	print(value, ..., sep=' ', end='\n', file=sys.stdout)

	Prints the values to a stream, or to sys.stdout by default.
	Optional keyword arguments:
	file: a file-like object (stream); defaults to the current sys.stdout.
	sep:  string inserted between values, default a space.
	end:  string appended after the last value, default a newline.


Elaboración del algoritmo en un editor de textos
................................................

Creamos un archivo `mi_fichero.py` en un editor de texto. En EPD (Enthought Python
Distribution), puedes usar `Scite`, disponible en el menú de inicio. En
Python(x,y) puedes usar Spyder. En Ubuntu, si todavía no tienes tu editor
favorito, te aconsejamos instalar `Stani's Python editor`. En el archivo,
agrega las siguientes líneas::

    s = 'Hola mundo'
    print(s)

Ahora, puedes ejecutarlo en IPython y explorar las variables resultantes:

.. sourcecode:: ipython

    In [1]: %run mi_fichero.py
    Hola mundo

    In [2]: s
    Out[2]: 'Hola mundo'

    In [3]: %whos
    Variable   Type    Data/Info
    ----------------------------
    s          str     Hola mundo


.. topic:: **Desde un script hasta funciones**

    Si bien es tentador trabajar solo con scripts, que es un archivo lleno
    de instrucciones a seguir que se siguen una tras otra, iremos planeando
    una evolución progresiva del script hacia un conjunto de funciones:

    * Un script no es reutilizable, las funciones sí.

    * Pensar en términos de funcionas ayuda a dividir el problema en
      pequeños bloques.


Consejos y trucos de IPython
............................

El manual de usuario de IPython contiene mucha información sobre cómo usar
IPython, pero para que puedas iniciarte queremos darte una breve introducción a tres
características útiles: *historial*, *funciones mágicas* y *autocompletado
con tabulador*.

Como una consola UNIX, IPython soporta comandos de historial. Teclea *arriba*
y *abajo* para reutilizar los comandos anteriormente ejecutados:

.. sourcecode:: ipython

    In [1]: x = 10

    In [2]: <cursor arriba>

    In [2]: x = 10

IPython soporta las llamadas funciones *mágicas* anteponiendo a  un comando
el caracter ``%``. Por ejemplo, las funciones ``run`` y ``whos`` de la
sección anterior son funciones mágicas. Tened en cuenta que, el ajuste
``automagic``, que está activado por defecto, te permite omitir el signo ``%``
predecesor al comando. De esta forma, puedes solo escribir la función
mágica y funcionará.

Otras funciones mágicas útiles son:

* ``%cd`` para cambiar el directorio actual.

  .. sourcecode:: ipython

    In [2]: cd /tmp
    /tmp

* ``%timeit`` permite medir el tiempo de ejecución de pequeños fragmentos de
  código usando el módulo ``timeit`` de la biblioteca estándar:

  .. sourcecode:: ipython

      In [3]: timeit x = 10
      10000000 loops, best of 3: 39 ns per loop

* ``%cpaste`` te permite pegar código, especialmente aquel que venga de un
  sitio web que ha sido precedido por el prompt estándar de Python (o sea
  ``>>>``) o con un prompt de IPython (por ejemplo: ``In [3]:``):

  .. sourcecode:: ipython

    In [5]: cpaste
    Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
    :In [3]: timeit x = 10
    :--
    10000000 loops, best of 3: 85.9 ns per loop
    In [6]: cpaste
    Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
    :>>> timeit x = 10
    :--
    10000000 loops, best of 3: 86 ns per loop

.. TODO: Mejorar traducción de "post-mortem debugging". Traducido como "depurado post-mortem".
* ``%debug`` te permite entrar en depuración post-mortem. Esto quiere decir
  que si el código que intentas ejecutar lanza una excepción, usando ``%debug``
  entrarás al depurador en el punto donde ocurrió la excepción.

  .. sourcecode:: ipython

    In [7]: x === 10
      File "<ipython-input-6-12fd421b5f28>", line 1
        x === 10
            ^
    SyntaxError: invalid syntax


    In [8]: debug
    > /home/esc/anaconda/lib/python2.7/site-packages/IPython/core/compilerop.py(87)ast_parse()
         86         and are passed to the built-in compile function."""
    ---> 87         return compile(source, filename, symbol, self.flags | PyCF_ONLY_AST, 1)
         88

    ipdb>locals()
    {'source': u'x === 10\n', 'symbol': 'exec', 'self':
    <IPython.core.compilerop.CachingCompiler instance at 0x2ad8ef0>,
    'filename': '<ipython-input-6-12fd421b5f28>'}

.. note::

    La chuleta (o cheat-sheet) de IPython es accesible a través de la
    función mágica ``%quickref``.

.. note::

    Se muestra una lista de todas las funciones mágicas disponibles cuando
    se escribe ``%magic``.

Además, IPython viene con diversos *alias* que emulan herramientas comunes
de líneas de comando UNIX como ``ls`` para listar archivos, ``cp`` para
copiar archivos y ``rm`` para eliminar archivos. Se muestra una lista de
los alias disponibles cuando se escribe ``alias``:

.. sourcecode:: ipython

    In [1]: alias
    Total number of aliases: 16
    Out[1]:
    [('cat', 'cat'),
    ('clear', 'clear'),
    ('cp', 'cp -i'),
    ('ldir', 'ls -F -o --color %l | grep /$'),
    ('less', 'less'),
    ('lf', 'ls -F -o --color %l | grep ^-'),
    ('lk', 'ls -F -o --color %l | grep ^l'),
    ('ll', 'ls -F -o --color'),
    ('ls', 'ls -F --color'),
    ('lx', 'ls -F -o --color %l | grep ^-..x'),
    ('man', 'man'),
    ('mkdir', 'mkdir'),
    ('more', 'more'),
    ('mv', 'mv -i'),
    ('rm', 'rm -i'),
    ('rmdir', 'rmdir')]

Finalmente, nos gustaría mencionar la característica de *autocompletado con
tabulador*, cuya descripción la extraemos directamente del manual original de IPython:

*El autocompletado con tabulador, especialmente para atributos, es una
manera conveniente de explorar la estructura de cualquier objeto con el
que estés tratando. Simplemente escribe nombre_del_objeto.<TAB> para ver
los atributos del objeto. Además de objetos y palabras claves de Python,
el autocompletado con tabulador también funciona para nombres de archivos
y directorios.*

.. sourcecode:: ipython

    In [1]: x = 10

    In [2]: x.<TAB>
    x.bit_length   x.conjugate    x.denominator  x.imag         x.numerator
    x.real

    In [3]: x.real.
    x.real.bit_length   x.real.denominator  x.real.numerator
    x.real.conjugate    x.real.imag         x.real.real

    In [4]: x.real.

.. :vim:spell:

