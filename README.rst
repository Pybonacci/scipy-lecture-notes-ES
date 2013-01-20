En este repositorio se encuentra una traducción (work in progress) de
las scipy-lecture-notes (https://github.com/scipy-lectures/scipy-lecture-notes). 
¿Qué son las scipy-lecture-notes? Son algunos apuntes de charlas y/o conferencias
sobre el ecosistema científico en python que pueden ser usadas para un curso
completo de computación científica en python.

Estos documentos han sido escritos con el lenguaje de marcado rest (extensión 
.rst) y han sido creados usando Sphinx: http://sphinx.pocoo.org/.

Modificando
===========

Reusando y distribuyendo
------------------------

Como se ha expuesto en el fichero LICENSE.txt, este material ha sido creado
sin ningún tipo de restricciones. Siéntete libre para reusarlo y modificarlo para
tus propias necesidades de enseñanza.

Sin embargo, nos gustaría que este material de referencia fuera mejorado a
lo largo del tiempo, es por ello que animamos a cualquiera a contribuir con
los cambios o mejoras que consideren al repositorio oficial en inglés 
(https://github.com/scipy-lectures/scipy-lecture-notes) para que sean revisadas
y editadas por los autores originales y posteriormente puedan ser 
traducidas al español u otros idiomas.

Instrucciones para crear la documentación
-----------------------------------------

Para crear la salida en html y que pueda ser mostrada en la pantalla, Escribe::

    make html

Los ficheros html generados se pueden encontrar en ``build/html``

La primera vez que se cree la documentación puede tomar un tiempo considerable, 
pero las posteriores creaciones de la documentación deberían ser más rápidas
puesto que la información se encontrará cacheada.

Para generar el fichero pdf para imprimir::

    make pdf

El constructor pdf es un poco 'peculiar' y podrías encontrar errores TeX. 
Modificaciones al formato en los ficheros rst deberían ser suficiente para
evitar estos problemas.

Requerimientos
..............

*probablemente incompleto*

* make
* sphinx (>= 1.0)
* pdflatex
* pdfjam
* matplotlib
* scikit-learn (>= 0.8)

Para contribuir
---------------

Política editorial
..................

El objetivo de este material es proporcionar un texto conciso para aprender
las principales características del ecosistema scipy. Si quieres contribuir
a la traducción del material de referencia te sugerimos que contribuyas a la
documentación de los paquetes específicos en los que estés interesado.

La salida en HTML puede ser usada para mostrar en una pantalla mientras 
se está enseñando. El objetivo es disponer del mismo material tanto en
pantalla como en los apuntes. Esta es la razón por la que la versión en
HTML debería mantenerse concisa, con listas cortas de cosas en lugar 
de párrafos y frases largas. Para la creación más elaborada de la documentación
nos gustaría incluir discusiones más elaboradas. Para estas partes, la política
es usar la directiva sphinx::

   .. only:: pdf

Cada capítulo debería mantenerse razonablemente corto: de 1 a 2 horas de tutorial.
La razón es doble. Primero, estos capítulos son átomos que pueden ser combinados
para crear un curso de computación científica con python. Segundo, la capacidad
de atención de la gente no suele durar más allá de una o dos horas, independientemente
de que estén leyendo un tutorial o estén atendiendo en una clase.

Modificar
.........

La forma más fácil de hacer tu propia versión de este material de enseñanza
es 'forkear' (crear una nueva forja) en Github y usar el sistema de control
de versiones git para mantener tu propio fork (forja). Para ello, lo único que
debes hacer es crear una cuenta en github (este sitio) y pulsar el botón
*fork*, arriba a la derecha de esta página. Puedes usar git para hacer *pull* 
desde tu *fork*, y hacer *push* para introducir los cambios. Si quieres deshacer
tu contribución simplemente rellena un *pull request*, usando el botón *pull request*
arriba de la página de tu fork.

Por favor, abstenerse a modificar el Makefile a no ser que sea absolutamente
necesario.

Gráficas y ejemplos de código
.............................

Las figuras deberían ser generadas a partir del código fuente python. La
política es crear un directorio ``examples`` en el cual colocar los
correspondientes ficheros python. Cualquier fichero cuyo nombre empiece con
``plot_`` será procesado durante la creación de la documentación y las
figuras creadas con matplotlib serán guardadas como imágenes en el directorio
``auto_examples``. Puedes usar lo anterior para incluir figuras en el 
documento. Para mostrar fragmentos de código puedes usar la directiva
``literal-include``. Cualquier dato adicional necesario en el script que
genera la/s figura/s debería encontrarse en el mismo directorio que el script. 
NB: el código para proporcionar este estilo de inclusión de figuras ha sido
adoptado del proyecto scikits.learn y puede ser encontrado en ``sphinxext/gen_rst.py``.

.. topic:: Guía de contribución y capítulo ejemplo

   El directorio `guide` contiene un capítulo de ejemplo con instrucciones específicas
   sobre como contribuir:

   .. toctree::

      guide/index.rst
