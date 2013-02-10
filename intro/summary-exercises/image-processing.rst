.. _summary_exercise_image_processing:

Aplicación de proceso de imagen: contando burbujas y granos no fundidos
------------------------------------------------------------------

.. image:: ../image_processing/MV_HFV_012.jpg
   :align: center
   :scale: 70

.. only:: latex

Definición del problema
..........................

1. Abre la imagen file MV_HFV_012.jpg y muéstrala en pantalla. Navega
a través de los argumentos del `docstring` de ``imshow`` para mostrar
la imagen con la orientación correctato display the image (origen en la
esquina inferior izquierda y no en la esquina superior izquierda como
en un array estándar).

Esta imagen `Scanning Element Microscopy` representa una muestra de cristal 
(matriz gris clara) con algunas burbujass (en negro) y granos de arena sin fundir (gris oscuro). 
Deseamos determinar la fración de la muestra cubierta por cada una de estas tres
fases y estimar el tamaño típico de los granos de arena, de las burbujas, sus
tamaños, etc.

2. Recorta la imagen para eliminar el panel inferior con la información de la medida.

3. Filtra ligeramente la imagen con un filtro de mediana para refinar su histograma.
Comprueba como cambia el histograma.

4. Usando el histograma de la imagen filtrada determina umbrales que permitan definir
máscaras para los píxeles de arena, los píxeles de cristal y los píxeles de burbuja.
Otra opción (deberes para casa): escribe una función que determine automáticamente
los umbrales a partir de la mínima del histograma.

5. Muestra en pantalla una imagen en la cual las tres fases (cristal, burbuja, arena) se
muestre con tres colores diferentes.

6. Usa morfología matemática para limpiar las diferentes fases.

7. Creo etiquetas de atributos para todas las burbujas y los granos de arena
 y elimina granos de arena que sean menores a 10 píxeles. Para hacer esto 
último puedes usar ``ndimage.sum`` o ``np.bincount`` para calcular el tamaño de los granos.

8. Calcula el tamaño medio de las burbujas.

.. only:: latex

   .. _image-answers:

Solución propuesta
....................

