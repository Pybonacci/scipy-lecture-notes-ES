=====================================
Debugging code / Código de depuración
=====================================

:autor: Gaël Varoquaux

En este tutorial se exploran herramientas que ayudan a entender tu código:
depuración para encontrar y corregir *bugs* (errores).

Esto no es específico de la comunidad científica Python pero las estrategias que vamos a emplear
se ajustan a sus necesidades.

.. topic:: Prerrequisitos

    * Numpy
    * IPython
    * nosetests (http://readthedocs.org/docs/nose/en/latest/)
    * pyflakes (http://pypi.python.org/pypi/pyflakes)
    * gdb para la parte de *C-debugging* (depurado de C).

.. contents:: Contenido de los capítulos
   :local:
   :depth: 4


Evitando *bugs*
===============

Buenas prácticas para evitar tener problemas al escribir código
---------------------------------------------------------------

.. sidebar:: Brian Kernighan

   *“Everyone knows that debugging is twice as hard as writing a
   program in the first place. So if you're as clever as you can be
   when you write it, how will you ever debug it?”*

* Todos nosotros escribimos código con errores. Acéptalo. Habrá que lidiar con ello.
* Escribe tu código con *testing* (las pruebas) y *debugging* (la depuración) en mente.
* Keep It Simple, Stupid (KISS). Mantenlo simple, estúpido.

  * ¿Cuál es la cosa más simple que podría funcionar?

* Don't Repeat Yourself (DRY). No te repitas.

  * Cada pieza de conocimiento debe tener una única, inequívoca y acreditada
    representación dentro del sistema.
  * Constantes, algoritmos, etc...

* Intenta limitar la cantidad de interdependencias de tu código (*Loose Coupling* / acoplamiento simple)
* Nombra a tus variables, funciones y módulos de forma significativa y explicativa (no usar
  nombres matemáticos)

pyflakes: Análisis estático veloz
---------------------------------

Existen varias herramientas de análisis estático en Python; algunas de ellas son: 
* `pylint <http://www.logilab.org/857>`_, 
* `pychecker <http://pychecker.sourceforge.net/>`_, y 
* `pyflakes <http://pypi.python.org/pypi/pyflakes>`_.
* `pep8 <http://pypi.python.org/pypi/pep8>`_
* `flake8 <http://pypi.python.org/pypi/flake8>`_

En nuestro caso nos vamos a focalizar en pyflakes, la cual es la herramienta más simple.

    * **Rápido, simple**

    * Detecta errores de sintaxis, *imports* ausentes, errores en nombres.

Otra buena recomendación es la herramienta flake8 que es una combinación
de pyflakes y de pep8. Así, además de los errores que captura pyflakes,
flake8 detecta violaciones de la recomendación de la guía de estilo `PEP8 
<http://www.python.org/dev/peps/pep-0008/>`_ .

La integración de pyflakes (o flake8) en tu editor o IDE sería altamente recomendable.
**Provoca que aumente tu productividad**.

Corriendo pyflakes en el fichero que estamos editando
.....................................................

Puedes crear una forma rápida (acceso rápido) para correr pyflakes en el
fichero que estemos editando.

* **En kate**
  Menu: 'settings -> configure kate 
  
    * En plugins hay que habilitar 'external tools'

    * En 'external Tools', añadir `pyflakes`::

        kdialog --title "pyflakes %filename" --msgbox "$(pyflakes %filename)"

* **En TextMate**

  Menu: TextMate -> Preferences -> Advanced -> Shell variables, añade una
  'shell variable'::

    TM_PYCHECKER=/Library/Frameworks/Python.framework/Versions/Current/bin/pyflakes

  A partir de entonces `Ctrl-Shift-V` lanzará un `report` pyflakes 


* **En vim**
  En tu `.vimrc` (conectará la tecla F5 a `pyflakes`)::

    autocmd FileType python let &mp = 'echo "*** running % ***" ; pyflakes %'
    autocmd FileType tex,mp,rst,python imap <Esc>[15~ <C-O>:make!^M
    autocmd FileType tex,mp,rst,python map  <Esc>[15~ :make!^M
    autocmd FileType tex,mp,rst,python set autowrite

* **En emacs**
  En tu `.emacs` (conectará la tecla F5 a `pyflakes`)::

    (defun pyflakes-thisfile () (interactive)
           (compile (format "pyflakes %s" (buffer-file-name)))
    )
    
    (define-minor-mode pyflakes-mode
        "Toggle pyflakes mode.
        With no argument, this command toggles the mode.
        Non-null prefix argument turns on the mode.
        Null prefix argument turns off the mode."
        ;; The initial value.
        nil
        ;; The indicator for the mode line.
        " Pyflakes"
        ;; The minor mode bindings.
        '( ([f5] . pyflakes-thisfile) )
    )
    
    (add-hook 'python-mode-hook (lambda () (pyflakes-mode t)))

A type-as-go spell-checker like integration (integración de un comprobador de código a medida que tecleas)
............................................

* **En vim**
  * Usa el plugin pyflakes.vim: 
  
    #. descarga el fichero zip desde
      http://www.vim.org/scripts/script.php?script_id=2441
  
    #. extrae los ficheros en ``~/.vim/ftplugin/python``

    #. comprueba que tu vimrc tiene ``filetype plugin indent on``

    .. image:: vim_pyflakes.png

  * De forma alternativa: usa el plugin `syntastic 
    <https://github.com/scrooloose/syntastic>`. Puede ser configurado para usar
    también ``flake8`` y además maneja la comprobación 'al vuelo' para muchos
    otros lenguajes.

    .. image:: vim_syntastic.png

* **En emacs**
  Usa el modo flymake con pyflakes, documentado en
  http://www.plope.com/Members/chrism/flymake-mode : añade lo siguiente a tu
  fichero .emacs::
  
    (when (load "flymake" t) 
            (defun flymake-pyflakes-init () 
            (let* ((temp-file (flymake-init-create-temp-buffer-copy 
                                'flymake-create-temp-inplace)) 
                (local-file (file-relative-name 
                            temp-file 
                            (file-name-directory buffer-file-name)))) 
                (list "pyflakes" (list local-file)))) 

            (add-to-list 'flymake-allowed-file-name-masks 
                    '("\\.py\\'" flymake-pyflakes-init))) 

    (add-hook 'find-file-hook 'flymake-find-file-hook)

Flujo de trabajo para depuración de código
===================

Si te encuentras ante un error (`bug`) que no es trivial es cuando las estrategias
para depurar código se hacen notar. No existe una forma inequívoca de hacer las cosas, por ello, las estrategias te pueden ayudar:

   **Para depurar un problema dado, la situación favorable se produce cuando
   el problema se aisla en un pequeño número de líneas de código, fuera
   del código de la aplicación completa con pequeños ciclos
   modificar-ejecutar-fallar**

#. Hazlo fallar de forma confiable.  Encuentra un caso de prueba que provoque
   que el código falle cada vez que se ejecute.
#. Divide y ganarás.  Una vez que tienes un caso de prueba fallando, 
   aisla el código con errores.

   * Qué módulo.
   * Qué función.
   * Qué línea de código.

   => aisla un fallo pequeño y reproducible: un caso de prueba

#. Cambia una cosa cada vez y vuelve a ejecutar el caso de prueba que está fallando.
#. Usa el depurador para entender qué es lo que está fallando.
#. Toma notas y sé paciente.  Podría llevar un rato.

.. note::

   Una vez que has pasado por este proceso: aislar la pequeña porción de código
   que reproduce el `bug` y reparar el `bug` usando esta pieza de código
   añade el código correspondiente a tu conjunto de pruebas.

Usando el depurador Python 
==========================

El depurador python, ``pdb``: http://docs.python.org/library/pdb.html,
te permite inspeccionar tu código de forma interactiva.

Te permite:

  * Ver el código.
  * Ir hacia arriba y hacia abajo del punto donde se ha producido un error.
  * Inspeccionar valores de variables.
  * Modificar valores de variables.
  * Establecer `breakpoints` (punto de parada del proceso).

.. topic:: **print**

    Sí, las declaraciones ``print`` sirven como herramienta de depuración. 
    Sin embargo, para inspeccionar en tiempo de ejecución es más
    eficiente usar el depurador.

Invocando al depurador
-----------------------

Formas de lanzar el depurador:

#. Postmortem, lanza el depurador después de que se hayan producido errores.
#. Lanza el módulo con el depurador.
#. Llama al depurador desde dentro del módulo.

Postmortem
...........

**Situación**: Estás trabajando en ipython y obtienes un error (`traceback`).

En este caso estamos depurando el fichero :download:`index_error.py`. Cuando lo ejecutes verás como se lanza un :class:`IndexError`. Escribe ``%debug`` y entrarás en el depurador.

.. sourcecode:: ipython

    In [1]: %run index_error.py
    ---------------------------------------------------------------------------
    IndexError                                Traceback (most recent call last)
    /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/index_error.py in <module>()
          6 
          7 if __name__ == '__main__':
    ----> 8     index_error()
          9 

    /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/index_error.py in index_error()
          3 def index_error():
          4     lst = list('foobar')
    ----> 5     print lst[len(lst)]
          6 
          7 if __name__ == '__main__':

    IndexError: list index out of range

    In [2]: %debug
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/index_error.py(5)index_error()
          4     lst = list('foobar')
    ----> 5     print lst[len(lst)]
          6 

    ipdb> list
          1 """Small snippet to raise an IndexError."""
          2 
          3 def index_error():
          4     lst = list('foobar')
    ----> 5     print lst[len(lst)]
          6 
          7 if __name__ == '__main__':
          8     index_error()
          9 

    ipdb> len(lst)
    6
    ipdb> print lst[len(lst)-1]
    r
    ipdb> quit

    In [3]: 

.. topic:: Depuración post-mortem sin ipython

   En algunas situaciones no podrás usar IPython, por ejemplo para depurar
   un `script` que ha sido llamado desde la línea de comandos. En este caso,
   puedes ejecutar el `script` de la siguiente forma 
   ``python -m pdb script.py``::

    $ python -m pdb index_error.py
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/index_error.py(1)<module>()
    -> """Small snippet to raise an IndexError."""
    (Pdb) continue
    Traceback (most recent call last):
    File "/usr/lib/python2.6/pdb.py", line 1296, in main
        pdb._runscript(mainpyfile)
    File "/usr/lib/python2.6/pdb.py", line 1215, in _runscript
        self.run(statement)
    File "/usr/lib/python2.6/bdb.py", line 372, in run
        exec cmd in globals, locals
    File "<string>", line 1, in <module>
    File "index_error.py", line 8, in <module>
        index_error()
    File "index_error.py", line 5, in index_error
        print lst[len(lst)]
    IndexError: list index out of range
    Uncaught exception. Entering post mortem debugging
    Running 'cont' or 'step' will restart the program
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/index_error.py(5)index_error()
    -> print lst[len(lst)]
    (Pdb) 
 
Ejecución paso a paso
.......................

**Situación**: Crees que existe un error en un módulo pero no estás seguro donde.

Por ejemplo, estamos intentado depurar :download:`wiener_filtering.py`.
A pesar de que el código se ejecuta, observamos que el filtrado no se
está haciendo correctamente.

* Ejecuta el `script` en IPython con el depurador usando ``%run -d
  wiener_filtering.py``:

  .. sourcecode:: ipython

    In [1]: %run -d wiener_filtering.py
    *** Blank or comment
    *** Blank or comment
    *** Blank or comment
    Breakpoint 1 at /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py:4
    NOTE: Enter 'c' at the ipdb>  prompt to start your script.
    > <string>(1)<module>()

* Coloca un *breakpoint* en la línea 34 usando ``b 34``:

  .. sourcecode:: ipython

    ipdb> n
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py(4)<module>()
          3 
    1---> 4 import numpy as np
          5 import scipy as sp

    ipdb> b 34
    Breakpoint 2 at /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py:34

* Continua la ejecución hasta el siguiente `breakpoint` con ``c(ont(inue))``:

  .. sourcecode:: ipython

    ipdb> c
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py(34)iterated_wiener()
         33     """
    2--> 34     noisy_img = noisy_img
         35     denoised_img = local_mean(noisy_img, size=size)

* Da pasos hacia adelante y detrás del código con ``n(ext)`` y
 ``s(tep)``. ``next`` salta hasta la siguiente declaración en el actual
  contexto de ejecución mientras que ``step`` se moverá entre los contextos
  en ejecución, i.e. permitiendo explorar dentro de llamadas a funciones:

  .. sourcecode:: ipython

    ipdb> s
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py(35)iterated_wiener()
    2    34     noisy_img = noisy_img
    ---> 35     denoised_img = local_mean(noisy_img, size=size)
         36     l_var = local_var(noisy_img, size=size)

    ipdb> n
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py(36)iterated_wiener()
         35     denoised_img = local_mean(noisy_img, size=size)
    ---> 36     l_var = local_var(noisy_img, size=size)
         37     for i in range(3):


* Muévete unas pocas líneas y explora las variables locales:

  .. sourcecode:: ipython

    ipdb> n
    > /home/varoquau/dev/scipy-lecture-notes/advanced/debugging_optimizing/wiener_filtering.py(37)iterated_wiener()
         36     l_var = local_var(noisy_img, size=size)
    ---> 37     for i in range(3):
         38         res = noisy_img - denoised_img
    ipdb> print l_var
    [[5868 5379 5316 ..., 5071 4799 5149]
     [5013  363  437 ...,  346  262 4355]
     [5379  410  344 ...,  392  604 3377]
     ..., 
     [ 435  362  308 ...,  275  198 1632]
     [ 548  392  290 ...,  248  263 1653]
     [ 466  789  736 ..., 1835 1725 1940]]
    ipdb> print l_var.min()
    0

*Oh dear*, solo vemos enteror y variación 0. Aquí está nuestro error,
estamos haciendo aritmética con enteros.

.. topic:: Lanzando excepciones en errores numéricos

    Cuando ejecutamos el fichero :download:`wiener_filtering.py`, se lanzarán
    los siguientes avisos:

    .. sourcecode:: ipython

        In [2]: %run wiener_filtering.py
        wiener_filtering.py:40: RuntimeWarning: divide by zero encountered in divide
            noise_level = (1 - noise/l_var )

    Podemos convertir estos avisos a excepciones, lo que nos permitiría
    hacer una depuración post-mortem sobre ellos y encontrar el problema
    de manera más rápida:

    .. sourcecode:: ipython

        In [3]: np.seterr(all='raise')
        Out[3]: {'divide': 'print', 'invalid': 'print', 'over': 'print', 'under': 'ignore'}
        In [4]: %run wiener_filtering.py
        ---------------------------------------------------------------------------
        FloatingPointError                        Traceback (most recent call last)
        /home/esc/anaconda/lib/python2.7/site-packages/IPython/utils/py3compat.pyc in execfile(fname, *where)
            176             else:
            177                 filename = fname
        --> 178             __builtin__.execfile(filename, *where)

        /home/esc/physique-cuso-python-2013/scipy-lecture-notes/advanced/debugging/wiener_filtering.py in <module>()
             55 pl.matshow(noisy_lena[cut], cmap=pl.cm.gray)
             56 
        ---> 57 denoised_lena = iterated_wiener(noisy_lena)
             58 pl.matshow(denoised_lena[cut], cmap=pl.cm.gray)
             59 

        /home/esc/physique-cuso-python-2013/scipy-lecture-notes/advanced/debugging/wiener_filtering.py in iterated_wiener(noisy_img, size)
             38         res = noisy_img - denoised_img
             39         noise = (res**2).sum()/res.size
        ---> 40         noise_level = (1 - noise/l_var )
             41         noise_level[noise_level<0] = 0
             42         denoised_img += noise_level*res
        FloatingPointError: divide by zero encountered in divide

Otras formas de comenzar una depuración
.......................................

* **Lanzar una excepción *break point* a lo pobre**

  Si encuentras tedioso el tener que anotar el número de línea para colocar
  un *break point*, puedes lanzar una excepción en el punto que quieres 
  inspeccionar y usar la 'magia' ``%debug`` de ipython. Destacar que en este
  caso no puedes moverte por el código y continuar después la ejecución.

* **Depurando fallos de pruebas usando nosetests**

  Podemos ejecutar ``nosetests --pdb`` para saltar a la depuración
  post-mortem de excepciones y ``nosetests --pdb-failure`` para inspeccionar
  los fallos de pruebas usando el depurador.

  Además, puedes usar la interfaz IPython para el depurador en **nose**
  usando el plugin  de **nose**
  `ipdbplugin <http://pypi.python.org/pypi/ipdbplugin>`_. Podremos, entonces,
  pasar las opciones ``--ipdb`` y ``--ipdb-failure`` a los *nosetests*.

* **Llamando explícitamente al depurador**

  Inserta la siguiente línea donde quieres que salte el depurador::

    import pdb; pdb.set_trace()

.. warning::

    Cuandos e ejecutan ``nosetests``, se captura la salida y parecerá
    que el depurador no está funcionando. Para evitar esto simplemente ejecuta
    los ``nosetests`` con la etiqueta ``-s``.


.. topic:: Depuradores gráficos y alternativas

    * Quizá encuentres más conveniente usar un depurador gráfico como  
      `winpdb <http://winpdb.org/>`_. para inspeccionar saltas a través del 
      código e inspeccionar las variables

    * De forma alternativa, `pudb <http://pypi.python.org/pypi/pudb>`_ es un 
      buen depurador semi-gráfico con una interfaz de texto en la consola.

    * También, estaría bien echarle un ojo al proyecto 
      `pydbgr <http://code.google.com/p/pydbgr/>`_

Comandos del depurador e interacciones
--------------------------------------

============ ======================================================================
``l(list)``   Lista el código en la posición actual
``u(p)``      Paso arriba de la llamada a la pila (*call stack*)
``d(own)``    Paso abajo de la llamada a la pila ((*call stack*)
``n(ext)``    Ejecuta la siguiente línea (no va hacia abajo en funciones nuevas)
``s(tep)``    Ejecuta la siguiente declaración (va hacia abajo en las nuevas funciones)
``bt``        Muestra el *call stack*
``a``         Muestra las variables locales
``!command``  Ejecuta el comando **Python** proporcionado (en oposición a comandos pdb)
============ ======================================================================

.. warning:: **Los comandos de depuración no son código Python**

    No puedes nombrar a las variables de la forma que quieras. Por ejemplo,
    si estamos dentro del depurador no podremos sobreescribir a las variables 
    con el mismo y, por tanto, **habrá que usar diferentes nombres para las
    variables cuando estemos teclenado código en el depurador**.

Obteniendo ayuda dentro del depurador
.....................................

Teclea ``h`` o ``help`` para acceder a la ayuda interactiva:

.. sourcecode:: pycon

    ipdb> help

    Documented commands (type help <topic>):
    ========================================
    EOF    bt         cont      enable  jump  pdef   r        tbreak   w
    a      c          continue  exit    l     pdoc   restart  u        whatis
    alias  cl         d         h       list  pinfo  return   unalias  where
    args   clear      debug     help    n     pp     run      unt
    b      commands   disable   ignore  next  q      s        until
    break  condition  down      j       p     quit   step     up

    Miscellaneous help topics:
    ==========================
    exec  pdb

    Undocumented commands:
    ======================
    retval  rv

Depurando errores de segmentación usando gdb 
============================================

Si te encuentras con un error de segmentación no podrás depurarlo con
pdb ya que se 'romperá' antes de que se pueda lanzar el depurador.
De la misma forma, si tienes un *bug* en un códico C empotrado en
Python, pdb será poco útil. Por estas razones tendremos que usar el 
gnu debugger, 
`gdb <http://www.gnu.org/s/gdb/>`_, disponible en Linux.

Antes de comenzar con gdb, permitidnos añadirle unas pocas herramientas
específicas para Python. Para ello, añadimos unas pocas macros a nuestro
`~/.gbdinit`. La opción óptima dependerá de tu versión de Python y de tu
versión de gdb. Hemos añadido una versión simplificada en:download:`gdbinit`, 
pero eres libre de leer 
`DebuggingWithGdb <http://wiki.python.org/moin/DebuggingWithGdb>`_.

Para depurar con gdb el 'script' Python :download:`segfault.py`, podemos
ejecutar el 'script' en gdb como sigue:

.. sourcecode:: console

    $ gdb python
    ...
    (gdb) run segfault.py
    Starting program: /usr/bin/python segfault.py
    [Thread debugging using libthread_db enabled]

    Program received signal SIGSEGV, Segmentation fault.
    _strided_byte_copy (dst=0x8537478 "\360\343G", outstrides=4, src=
        0x86c0690 <Address 0x86c0690 out of bounds>, instrides=32, N=3,
        elsize=4)
            at numpy/core/src/multiarray/ctors.c:365
    365            _FAST_MOVE(Int32);
    (gdb)

Obtenemos un error de segmentación y gdb lo captura para depurarlo post-mortem
en el nivel C de la pila (*stack*) (no en el *Python call stack*). Podemos 
depurar la llamada a la pila en C usando comandos gdb:

.. sourcecode:: console

    (gdb) up
    #1  0x004af4f5 in _copy_from_same_shape (dest=<value optimized out>, 
        src=<value optimized out>, myfunc=0x496780 <_strided_byte_copy>,
        swap=0)
    at numpy/core/src/multiarray/ctors.c:748
    748         myfunc(dit->dataptr, dest->strides[maxaxis],

Como puedes ver, ahora mismo estamos en el código C de numpy. Nos gustaría
saber cuál es el código python que desencadena este error de segmentación, 
así que vamos hacia arriba de la pila hasta que demos con el código Python
que buscamos:

.. sourcecode:: console

    (gdb) up
    #8  0x080ddd23 in call_function (f=
        Frame 0x85371ec, for file /home/varoquau/usr/lib/python2.6/site-packages/numpy/core/arrayprint.py, line 156, in _leading_trailing (a=<numpy.ndarray at remote 0x85371b0>, _nc=<module at remote 0xb7f93a64>), throwflag=0)
        at ../Python/ceval.c:3750
    3750    ../Python/ceval.c: No such file or directory.
            in ../Python/ceval.c

    (gdb) up
    #9  PyEval_EvalFrameEx (f=
        Frame 0x85371ec, for file /home/varoquau/usr/lib/python2.6/site-packages/numpy/core/arrayprint.py, line 156, in _leading_trailing (a=<numpy.ndarray at remote 0x85371b0>, _nc=<module at remote 0xb7f93a64>), throwflag=0)
        at ../Python/ceval.c:2412
    2412    in ../Python/ceval.c
    (gdb) 

Una vez que estamos en el bucle de ejecución Python, podríamos usar nuestra función especial de ayuda Python. Por ejemplo, podemos encontrar el correspondiente
código Python:

.. sourcecode:: console

    (gdb) pyframe
    /home/varoquau/usr/lib/python2.6/site-packages/numpy/core/arrayprint.py (158): _leading_trailing
    (gdb) 

Este es el código numpy, tenemos que ir hacia arriba hasta que encomntremos 
el código que hemos escrito:

.. sourcecode:: console

    (gdb) up
    ...
    (gdb) up
    #34 0x080dc97a in PyEval_EvalFrameEx (f=
        Frame 0x82f064c, for file segfault.py, line 11, in print_big_array (small_array=<numpy.ndarray at remote 0x853ecf0>, big_array=<numpy.ndarray at remote 0x853ed20>), throwflag=0) at ../Python/ceval.c:1630
    1630    ../Python/ceval.c: No such file or directory.
            in ../Python/ceval.c
    (gdb) pyframe
    segfault.py (12): print_big_array

El código correspondiente es:

.. literalinclude:: segfault.py
    :language:py
    :lines: 8-14

Entonces, el error de segmentación ocurre cuando mostramos `big_array[-10:]`. 
La razón es que ``big_array`` ha sido localizado con su final fuera de la 
memoria del programa.

.. note:: 
   
    Para ver una lista de los comandos Python específicos definidos en el
    `gdbinit` podéis leer el código fuente de este fichero.


____

.. topic:: **Ejercicio final**
    :class: green
    
    El siguiente *script* está bien documentado y es legible. Busca responder
    un problema de interés en el mundo de la computación numérica pero no
    funciona... ¿Lo podrías depurar?

    **Código fuente Python:** :download:`to_debug.py <to_debug.py>`

    .. only:: html

        .. literalinclude:: to_debug.py

