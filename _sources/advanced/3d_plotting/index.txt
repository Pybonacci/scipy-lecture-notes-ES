.. _mayavi-label:

========================
Gráficos 3D con Mayavi
========================

.. only:: html

    .. image:: mayavi-logo.png
        :align: right

:autor: Gaël Varoquaux

.. contents:: Contenidos de los capítulos
    :local:
    :depth: 3


Mlab: la interfaz de scripting
==============================

El módulo :mod:`mayavi.mlab` proporciona funciones simples para hacer gráficas
aplicables a arrays numpy. Puedes probar las funciones usando IPython inicializando IPython con la
opción ``--gui=wx``.

Funciones para gráficos 3D
---------------------------

Puntos
......
 
.. image:: examples/points3d.png
    :align: right
    :scale: 50

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin points3d example
    :end-before: ### end points3d example

Líneas
......

.. image:: examples/plot3d.png
    :align: right
    :scale: 50

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin plot3d example
    :end-before: ### end plot3d example

Superficies de elevación
........................

.. image:: examples/surf.png
    :align: right
    :scale: 50

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin surf example
    :end-before: ### end surf example

Mallas arbitrariamente irregulares
..................................

.. image:: examples/mesh.png
    :align: right
    :scale: 50

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin mesh example
    :end-before: ### end mesh example

.. note:: 

    Una superficie se encuentra definida por puntos **connectados** formando triángulos o
    polígonos. En `mlab.surf` y `mlab.mesh`, el layout de los arrays proporciona de
    forma implícita las conexiones y la conectividad. Ver también
    `mlab.triangular_mesh`.

**Nuestros datos suelen ser algo más que puntos y valores: necesitan información de la 
conectividad**

Datos volumétricos
................

.. _mayavi-voldata-label: 

.. image:: examples/contour3d.png
    :align: right
    :scale: 50

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin contour3d example
    :end-before: ### end contour3d example

.. image:: examples/viz_volume_structure.png
    :align: right
    :scale: 50

**Esta función funciona con un grid ortogonal regular:** El array de `valores` es un array
3D que proporciona la forma del grid.

Figuras y decoraciones
-------------------------

Gestión de figuras
..................

.. only:: latex

    Aquí se puede ver una lista de funciones útiles para el control de la figura en uso


====================================== ==============================================================
Obtener la figura en uso:		       `mlab.gcf()`
-------------------------------------- --------------------------------------------------------------
Limpiar la figura en uso:	            `mlab.clf()`
-------------------------------------- --------------------------------------------------------------
Establecer la figura a usar:		    `mlab.figure(1, bgcolor=(1, 1, 1), fgcolor=(0.5, 0.5, 0.5)`
-------------------------------------- --------------------------------------------------------------
Guardar la figura a un fichero imagen: `mlab.savefig('foo.png', size=(300, 300))`
-------------------------------------- --------------------------------------------------------------
Cambiar la vista:		               `mlab.view(azimuth=45, elevation=54, distance=1.)`
====================================== ==============================================================

Cambiando las propiedades del gráfico
.........................

.. only:: latex

    En general, muchas propiedades de los distintos objetos presentes en la figura
    podrían cambiarse. Si las visualizaciones son creadas via funciones presentes en el módulo `mlab`, 
    la forma más sencilla de modificar estas visualizaciones sería modificando los argumentos de las
    palabras clave (`keywords`) de cada función usada tal como se describe en la documentación (`docstrings`).

.. topic:: **Ejemplo de documentación (`docstring`, lo dejamos en inglés puesto que es
             como te aparecerá cuando lo veas en el código):** `mlab.mesh`

    Plots a surface using grid-spaced data supplied as 2D arrays.
    
    **Function signatures**::
    
        mesh(x, y, z, ...)
    
    x, y, z are 2D arrays, all of the same shape, giving the positions of
    the vertices of the surface. The connectivity between these points is
    implied by the connectivity on the arrays.
    
    For simple structures (such as orthogonal grids) prefer the surf function,
    as it will create more efficient data structures.
    
    **Keyword arguments:**
    
        :color: the color of the vtk object. Overides the colormap,
                if any, when specified. This is specified as a
                triplet of float ranging from 0 to 1, eg (1, 1,
                1) for white.
                
        :colormap: type of colormap to use.
                   
        :extent: [xmin, xmax, ymin, ymax, zmin, zmax]
                 Default is the x, y, z arrays extents. Use
                 this to change the extent of the object
                 created.
                 
        :figure: Figure to populate.
                 
        :line_width:  The with of the lines, if any used. Must be a float.
                     Default: 2.0
                     
        :mask: boolean mask array to suppress some data points.
               
        :mask_points: If supplied, only one out of 'mask_points' data point is
                      displayed. This option is usefull to reduce the number
                      of points displayed on large datasets Must be an integer
                      or None.
                      
        :mode: the mode of the glyphs. Must be '2darrow' or '2dcircle' or
               '2dcross' or '2ddash' or '2ddiamond' or '2dhooked_arrow' or
               '2dsquare' or '2dthick_arrow' or '2dthick_cross' or
               '2dtriangle' or '2dvertex' or 'arrow' or 'cone' or 'cube' or
               'cylinder' or 'point' or 'sphere'. Default: sphere
               
        :name: the name of the vtk object created.

        :representation: the representation type used for the surface. Must be
                         'surface' or 'wireframe' or 'points' or 'mesh' or
                         'fancymesh'. Default: surface
                         
        :resolution: The resolution of the glyph created. For spheres, for
                     instance, this is the number of divisions along theta and
                     phi. Must be an integer. Default: 8
                     
        :scalars: optional scalar data.
                  
        :scale_factor: scale factor of the glyphs used to represent
                       the vertices, in fancy_mesh mode. Must be a float.
                       Default: 0.05
                       
        :scale_mode: the scaling mode for the glyphs
                     ('vector', 'scalar', or 'none').
                     
        :transparent: make the opacity of the actor depend on the
                      scalar.
                      
        :tube_radius: radius of the tubes used to represent the
                      lines, in mesh mode. If None, simple lines are used.
                      
        :tube_sides: number of sides of the tubes used to
                     represent the lines. Must be an integer. Default: 6
                     
        :vmax: vmax is used to scale the colormap
               If None, the max of the data will be used
               
        :vmin: vmin is used to scale the colormap
               If None, the min of the data will be used
    

Ejemplo:

.. sourcecode:: ipython

    In [1]: import numpy as np

    In [2]: r, theta = np.mgrid[0:10, -np.pi:np.pi:10j]

    In [3]: x = r*np.cos(theta)

    In [4]: y = r*np.sin(theta)

    In [5]: z = np.sin(r)/r

    In [6]: from enthought.mayavi import mlab

    In [7]: mlab.mesh(x, y, z, colormap='gist_earth', extent=[0, 1, 0, 1, 0, 1])
    Out[7]: <enthought.mayavi.modules.surface.Surface object at 0xde6f08c>

    In [8]: mlab.mesh(x, y, z, extent=[0, 1, 0, 1, 0, 1], 
       ...: representation='wireframe', line_width=1, color=(0.5, 0.5, 0.5))
    Out[8]: <enthought.mayavi.modules.surface.Surface object at 0xdd6a71c>

.. image:: polar_mesh.png
    :align: center
    :scale: 70

Decoraciones
............

.. only:: latex

    Se pueden añadir piezas extra en la figura para mostrar información de interés,
    como una barra de colores o un título.

.. sourcecode:: ipython

    In [9]: mlab.colorbar(Out[7], orientation='vertical')
    Out[9]: <tvtk_classes.scalar_bar_actor.ScalarBarActor object at 0xd897f8c>

    In [10]: mlab.title('Malla polar')
    Out[10]: <enthought.mayavi.modules.text.Text object at 0xd8ed38c>

    In [11]: mlab.outline(Out[7])
    Out[11]: <enthought.mayavi.modules.outline.Outline object at 0xdd21b6c>

    In [12]: mlab.axes(Out[7])
    Out[12]: <enthought.mayavi.modules.axes.Axes object at 0xd2e4bcc>

.. image:: decorations.png
    :align: center
    :scale: 80

.. Advertencia:: 

    **extensión:** Si especificamos la extensión de un objeto gráfico,  
    `mlab.outline' y `mlab.axes` no tendrán esa extensión por defecto y habría que especificarla.


    mlab_scripting_interface.rst
    interaction.rst


Trabajo interactivo
=================

.. only:: latex

    Probablemente, la manera más rápida y efectiva de crear visualizaciones bonitas con Mayavi sería 
    interactuando y modificando los distintos ajustes de forma interactiva.

El "pipeline dialog"
----------------------

Pulsa en el botón 'Mayavi' que se encuentra en la figura para poder controlar las propiedades
de los objetos con la ayuda de ventanas de diálogos.

.. image:: pipeline.png
    :align: center
    :scale: 80

* Establece el fondo de la figura en el nodo de las `Mayavi Scene`
* Establece el mapa de colores en el nodo `Colors and legends`
* Se pueden añadir módulos y/o filtras pulsando en el botón derecho del ratón

El botón de grabación del script
-----------------------------

Para descubrir con qué códifo podríamos modificar los cambios realizados de forma interactiva
podemos pulsar sobre el botón rojo cuando vayamos a realizar las modificaciones y se generarán
las correspondientes líneas de código.
