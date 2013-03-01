Mlab: La interfaz de scripting
==============================

El módulo :mod:`mayavi.mlab` proporciona simples funciones de dibujo que se aplican sobre 
numpy arrays. Pruébalo usando IPython, arrancando IPython con el switch ``--gui=wx``.

Funciones de gráficos 3D 
------------------------

.. image:: examples/points3d.png
    :align: right
    :scale: 50

Puntos
......
 
.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin points3d example
    :end-before: ### end points3d example

|clear-floats|

.. image:: examples/plot3d.png
    :align: right
    :scale: 50

Líneas
......

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin plot3d example
    :end-before: ### end plot3d example

|clear-floats|

.. image:: examples/surf.png
    :align: right
    :scale: 50

Superficie de elevaciones
.........................

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin surf example
    :end-before: ### end surf example

|clear-floats|

.. image:: examples/mesh.png
    :align: right
    :scale: 50

Mallas arbitrarias regulares
............................

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin mesh example
    :end-before: ### end mesh example

.. note:: 

    Una superficie está definida por puntos **conectados** para formar triángulos o
    polígonos. En `mlab.surf` y `mlab.mesh`, la conectividad se encuentra dada de forma implícita
    por el layout de los arrays. Ver también `mlab.triangular_mesh`.

**A menudo, nuestros datos son más que puntos y valores: necesitará alguna información para 
la conectividad**

.. _mayavi-voldata-label: 

.. image:: examples/contour3d.png
    :align: right
    :scale: 50

Datos volumétricos
..................

.. literalinclude:: examples/generate_figures.py
    :start-after: ### begin contour3d example
    :end-before: ### end contour3d example

.. image:: examples/viz_volume_structure.png
    :align: right
    :scale: 50

**Esta función trabaja con un grid regular ortogonal:** el `valor` array
es un array 3D que proporciona la forma del grid.

|clear-floats|

Figuras y decoraciones
----------------------

Gestión de figuras
..................

.. only:: latex

    Aquí se encuentra una lista de funciones útiles para controlar la figura que se encuentra activa


================================ ==============================================================
Obtener la figura activa:		  `mlab.gcf()`
-------------------------------- --------------------------------------------------------------
Limpiar la figura activa:	  `mlab.clf()`
-------------------------------- --------------------------------------------------------------
Establecer la figura activa:		  `mlab.figure(1, bgcolor=(1, 1, 1), fgcolor=(0.5, 0.5, 0.5)`
-------------------------------- --------------------------------------------------------------
Guardar la figura en un fichero:	  `mlab.savefig('foo.png', size=(300, 300))`
-------------------------------- --------------------------------------------------------------
Cambiar la vista:		  mlab.view(azimuth=45, elevation=54, distance=1.)
================================ ==============================================================

Cambiando las propiedades del gráfico
.....................................

.. only:: latex

    En general, muchas propiedades de los distintos objetos que se encuentran en la figura se
    pueden cambiar. Si estas visualizaciones se crean funciones via `mlab`, 
    la forma más fácil de cambiarlas es mediante el uso de argumentos en las keywords (palabras clave)
    de esas funciones, como se describe en los docstrings.

.. topic:: **Ejemplo docstring:** `mlab.mesh`

    Dibuja una superficie usando datos grid-espaciados suministrados con arrays 2D.
    
    **Function signatures**::
    
        mesh(x, y, z, ...)
    
    x, y, z son arrays 2D, todos con la misma forma, proporcionando las posiciones
    de los vértices de las superficies. La conectividad entre los puntos se deriva
    a partir de la conectividad en los arrays.
    
    Para estructuras simples (como grids ortogonales) es preferible usar la función surf,
    ya que creará estructuras de datos más eficientes.
    
    **Keyword arguments:**
    
        :color: el color del objeto vtk. sobreescribe el colormap,
                si existe, cuando se especifica. Se especifica mediante un
                triplete de decimales en el intervalo que va desde 0 hasta 1, eg (1, 1,
                1) para el blanco.
                
        :colormap: tipo de colormap a usar.
                   
        :extent: [xmin, xmax, ymin, ymax, zmin, zmax]
                 Por defecto usa la extensión de los arrays x, y, z. Usar
                 esto para cambiar la extensión de un objeto creado.
                 
        :figure: Figura a rellenar.
                 
        :line_width:  Especifica el ancho de las líneas, en el caso de que se usen líneas. 
                     Debe ser un valor decimal. Valor por defecto: 2.0
                     
        :mask: array de máscara booleana para suprimir algunos puntos de datos.
               
        :mask_points: Si se proporciona, solo uno de los puntos de datos 'mask_points' será
                      mostrado. Esta opción es útil para reducir el número de puntos
                      mostrados en grandes grupos de datos. Debe ser un entero o None.
                      
        :mode: El modelo de los glyphs. Debe ser '2darrow' o '2dcircle' o
               '2dcross' o '2ddash' o '2ddiamond' o '2dhooked_arrow' o
               '2dsquare' o '2dthick_arrow' o '2dthick_cross' o
               '2dtriangle' o '2dvertex' o 'arrow' o 'cone' o 'cube' o
               'cylinder' o 'point' o 'sphere'. Valor por defecto: sphere
               
        :name: el nombre del objeto vtk creado.

        :representation: El tipo de representación usado para la superficie. Debe ser
                         'surface' o 'wireframe' o 'points' o 'mesh' o
                         'fancymesh'. Valor por defecto: surface
                         
        :resolution: La resolución del glyph creado. Para esferas, por
                     ejemplo, este es el número de divisiones a lo largo de theta y
                     de phi. Debe ser un entero. Valor por defecto: 8
                     
        :scalars: datos escalares opcionales.
                  
        :scale_factor: factor de escala de los glyphs usados para representar
                       los vértices, en modo fancy_mesh. Debe ser un valor decimal.
                       Valor por defecto: 0.05
                       
        :scale_mode: el modo de escalado de los glyphs
                     ('vector', 'scalar' o 'none').
                     
        :transparent: hace que la transparencia del actor dependa de un escalar.
                      
        :tube_radius: radio de los tubos usados para representar las
                      líneas, en modo mesh. Si se usa None, se usarán líneas simples.
                      
        :tube_sides: número de caras de los tubos usados para representar las líneas.
                     Debe ser un entero. Valor por defecto: 6
                     
        :vmax: vmax se usa para escalar el colormap
               Si se usa None, El valor máximo de los datos será usado
               
        :vmin: vmin se usa para escalar el colormap
               Si se usa None, El valor mínimo de los datos será usado
    

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

    Diferentes items pueden ser añadidos a la figura para aportar
    información extra, como una colorbar o un título.

.. sourcecode:: ipython

    In [9]: mlab.colorbar(Out[7], orientation='vertical')
    Out[9]: <tvtk_classes.scalar_bar_actor.ScalarBarActor object at 0xd897f8c>

    In [10]: mlab.title('polar mesh')
    Out[10]: <enthought.mayavi.modules.text.Text object at 0xd8ed38c>

    In [11]: mlab.outline(Out[7])
    Out[11]: <enthought.mayavi.modules.outline.Outline object at 0xdd21b6c>

    In [12]: mlab.axes(Out[7])
    Out[12]: <enthought.mayavi.modules.axes.Axes object at 0xd2e4bcc>

.. image:: decorations.png
    :align: center
    :scale: 80

.. warning:: 

    **extent (extensión):** Si se especifican extensiones para un objeto gráfico
    `mlab.outline' y `mlab.axes` no los obtienen por defecto.


