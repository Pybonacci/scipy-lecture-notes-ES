===========
 Soluciones
===========


.. _pi_wallis:

La solución Pi Wallis
----------------------

Calcula los decimales de Pi usando la fórmula de Wallis:

.. literalinclude:: solutions/pi_wallis.py

.. _quick_sort:

La solución Quicksort
----------------------

Implementa el algoritmo quicksort tal como viene definido en la wikipedia:

::

	function quicksort(array)
	    var list less, greater
	    if length(array) ≤ 1  
		return array  
	    select and remove a pivot value pivot from array
	    for each x in array
		if x ≤ pivot then append x to less
		else append x to greater
	    return concatenate(quicksort(less), pivot, quicksort(greater))

.. literalinclude:: solutions/quick_sort.py

.. _fibonacci:

Secuencia de Fibonacci
----------------------

Escribe una función que muestre los ``n`` primeros términos de la secuencia
de Fibonacci, definidad por:

* ``u_0 = 1; u_1 = 1``
* ``u_(n+2) = u_(n+1) + u_n``

::

    >>> def fib(n):    
    ...     """Display the n first terms of Fibonacci sequence"""
    ...     a, b = 0, 1
    ...     i = 0
    ...     while i < n:
    ...         print b
    ...         a, b = b, a+b
    ...         i +=1
    ...
    >>> fib(10)
    1
    1
    2
    3
    5
    8
    13
    21
    34
    55

.. _dir_sort:

La solución del listado de un directorio
----------------------------------------

Implementa un script que usa el nombre de un directorio como argumento
y devuelva la lista de los ficheros '.py' files ordenados por la longitud del nombre.

**Truco:** intenta entender el docstring de list.sort

.. literalinclude:: solutions/dir_sort.py

.. _data_file:

La solución del fichero E/S de datos
------------------------------------

Escribe una función que lea la columna de números en ``data.txt``
y calcule el máximo, el mínimo y la suma de los valores.

Fichero de datos:

.. literalinclude:: solutions/data.txt

Solución:

.. literalinclude:: solutions/data_file.py

.. _path_site:

La solución de búsqueda en el PYTHONPATH
----------------------------------------

Escribe un programa que busque el módulo ``site.py`` en tu PYTHONPATH.

.. literalinclude:: solutions/path_site.py

