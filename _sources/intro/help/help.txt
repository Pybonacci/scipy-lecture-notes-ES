.. _help:

Obteniendo ayuda y buscando en la documentación
===============================================

.. only:: latex

    :autor: Emmanuelle Gouillart

Mejor que conocer todas las funciones existentes en Numpy y en Scipy sería más importante
e interesante encontrar de forma rápida a través de la documentación y de la ayuda
disponible. Aquí encontrarás algunas formas de encontrar información:

* En Ipython, ``help function`` abre el `docstring` de la función. Solo has de teclear el
  principio del nombre de la función y después pulsar tab para que se muestre en pantalla
  las posibles funciones que estamos buscando.

  .. sourcecode:: ipython
  
      In [204]: help np.v
      np.vander     np.vdot       np.version    np.void0      np.vstack
      np.var        np.vectorize  np.void       np.vsplit     
      
      In [204]: help np.vander
	
En Ipython no es posible abrir una ventana aparte para mostrar la ayuda y la
documentación; sin embargo siempre podríamos abrir una segunda consola ``Ipython``
para mostrar únicamente la ayuda y `docstrings`...

* Se puede navegar online en las documentaciones de Numpy y de Scipy en
  http://docs.scipy.org/doc. El botón ``search`` localizado en la documentación de refencia
  de ambos paquetes es muy útil
  (http://docs.scipy.org/doc/numpy/reference/ y
  http://docs.scipy.org/doc/scipy/reference/). 

  Tutoriales sobre varios temas además de la API completa con todos los
  `docstrings` se puede encontrar en el siguiente sitio web.


  .. image:: scipy_doc.png
     :align: center
     :scale: 80

* La socumentación de Numpy y de Scipy se completa y actualiza de forma regular por 
  los usuarios en la wiki http://docs.scipy.org/numpy/. Como resultado,
  algunos `docstrings` están más claros y detallados en la wiki y podrías leer
  directamente la documentación en la wiki en lugar de usar la documentación oficial. 
  Hay que tener en cuenta que cualquiera puede crearuna cuenta en la wiki y escribir
  mejor documentación; ¡esta es una forma sencilla de contribuir a un proyecto
  de código libre y ayudar a mejorar las herramientas que usas!

  .. image:: docwiki.png
     :align: center
     :scale: 80

* El recetario (`cookbook`) de Scipy http://www.scipy.org/Cookbook proporciona recetas
  para muchos de los problemas que nos encontramos a menudo, como el ajuste de datos,
  resolver ecuaciones diferenciales ordinarias, etc. 


* La web de Matplotlib http://matplotlib.sourceforge.net/ dispone de una
  **galeria* con un gran número de gráficos y ejemplos con el código fuente y el
  gráfico resultante. Esto es muy útil aprender mediante ejemplos. También se encuentra 
  disponible la documentación estándar. 


  .. image:: matplotlib.png
     :align: center
     :scale: 80

* El sitio web de Mayavi
  http://code.enthought.com/projects/mayavi/docs/development/html/mayavi/
  también dispone de una galería de ejemplos
  http://code.enthought.com/projects/mayavi/docs/development/html/mayavi/auto/examples.html
  en la cual podemos navegar a través de diferentes soluciones de visualización.

  .. image:: mayavi_website.png
     :align: center
     :scale: 80

Finalmente, también serían útiles dos posibilidades más "técnicas":

* En Ipython, la función mágica ``%psearch`` busca objetos que coincidan
  con los patrones de búsqueda. Esto es útil, por ejemplo, cuando desconocemos
  el nombre exacto de una función.


  .. sourcecode:: ipython
  
      In [3]: import numpy as np
      In [4]: %psearch np.diag*
      np.diag
      np.diagflat
      np.diagonal

* numpy.lookfor busca por palabras clave dentro de los `docstrings` de módulos específicos.

  .. sourcecode:: ipython
  
      In [45]: numpy.lookfor('convolution')
      Search results for 'convolution'
      --------------------------------
      numpy.convolve
          Returns the discrete, linear convolution of two one-dimensional
      sequences.
      numpy.bartlett
          Return the Bartlett window.
      numpy.correlate
          Discrete, linear correlation of two 1-dimensional sequences.
      In [46]: numpy.lookfor('remove', module='os')
      Search results for 'remove'
      ---------------------------
      os.remove
          remove(path)
      os.removedirs
          removedirs(path)
      os.rmdir
          rmdir(path)
      os.unlink
          unlink(path)
      os.walk
          Directory tree generator.



* Si todo lo anterior falla (y Google no tiene la respuesta)... 
  ¡no hay que desesperarse! Escribe a la lista de correo que se 
  ajuste a tu problema: obtendrás una rápida respuesta si defines correctamente
  tu problema. A menudo, expertos en `python científico` ofrecen explicaciones
  muy clarificadoras en las listas de correo.

    * **Numpy discussion** (numpy-discussion@scipy.org): todo acerca de arrays
      numpy, como manipularlos, preguntas sobre indexación, etc.


    * **SciPy Users List** (scipy-user@scipy.org): cálculos científicos con Python, 
      procesamiento de datos de alto nivel, en particular con el paquete scipy.

    * matplotlib-users@lists.sourceforge.net para hacer gráficas con
      matplotlib.                               
                                             
