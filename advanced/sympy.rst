.. TODO: bench and fit in 1:30

.. TODO: plotting <- broken in OSX

.. _sympy:

======================================
Sympy : Matemáticas simbólicas en Python
======================================

:autor: Fabian Pedregosa

.. topic:: Objetivos

    1. Evaluar expresiones con precisión arbitraria.
    2. Ejecutar manipulaciones algebráicas en expresiones simbólicas.
    3. Ejecutar tareas básicas de cálculo (límites, diferenciación e integración) 
       con expresiones simbólicas.
    4. Resolver ecuaciones polinómicas y transcendentales.
    5. Resolver algunas ecuaciones diferenciales.

.. role:: input(strong)

**¿Qué es SymPy?** SymPy es una biblioteca Python para matemática simbólica. Su
propósito es convertirse en un completo sistema de álgebra computacional que pueda
competir directamente con alternativas comerciales (Mathematica, Maple) manteniendo,
a la vez, el código tan simple como sea posible para hacerlo extensible de manera fácil
e integral. SymPy está escrito completamente en Python y no necesita usar otras bibliotecas.

La documentación y los paquetes Sympy para instalarlo pueden encontrarse en:
http://sympy.org/

.. contents:: Contenidos de los capítulos
   :local:
   :depth: 4


Primeros pasos con SymPy
========================


Usando SymPy como una calculadora
---------------------------------

SymPy define tres tipos numéricos: Real, Racional y Entero.

La clase Racional representa un número racional como una pareja de números
Enteros: el numerador y el denominador, de esta forma Rational(1,2)
representa 1/2, Rational(5,2) 5/2, etcétera::

    >>> from sympy import *
    >>> a = Rational(1,2)

    >>> a
    1/2

    >>> a*2
    1

SymPy usa mpmath en segundo plano (background), lo que hace posible
ejecutar cálculos usando aritmética con precisión arbitraria. De
esta forma, algunas constantes especiales como e, pi, oo (Infinito), son tratadas como
símbolos y pueden ser evaluadas con precisión arbitraria::

    >>> pi**2
    pi**2

    >>> pi.evalf()
    3.14159265358979

    >>> (pi+exp(1)).evalf()
    5.85987448204884

como puedes ver, evalf evalua la expresión como un número decimal.

También existe una clase para representar el infinito (matemático) llamada
``oo``::

    >>> oo > 99999
    True
    >>> oo + 1
    oo


Ejercicios
----------

1. Calcular :math:`\sqrt{2}` con 100 decimales.
2. Calcular :math:`1/2 + 1/3` en aritmética racional.


Símbolos
--------

En contraste a otros sistemas de álgebra computacional, en SymPy hay que declarar
las variables simbólicas de forma explícita::

    >>> from sympy import *
    >>> x = Symbol('x')
    >>> y = Symbol('y')

Después de declararlas podrán ser usadas::

    >>> x+y+x-y
    2*x

    >>> (x+y)**2
    (x + y)**2

Los símbolos, ahora, pueden ser manipulados usando algunos de los operadores python: +, -, \*, \*\* 
(arithmetic), &, |, ~ , >>, << (boolean).



Manipulaciones algebráicas
==========================

SymPy es capaz de ejecutar potentes manipulaciones algebráicas. Echaremos un vistazo
a algunas de las más usadas: expandir y simplificar.

Expandir
--------

Usa lo siguiente para expandir una expresión algebráica. Tratará de expandir
las potencias y multiplicaciones::

    In [23]: expand((x+y)**3)
    Out[23]: 3*x*y**2 + 3*y*x**2 + x**3 + y**3

Se pueden usar diferentes opciones a partir de palabras clave (keywords)::

    In [28]: expand(x+y, complex=True)
    Out[28]: I*im(x) + I*im(y) + re(x) + re(y)

    In [30]: expand(cos(x+y), trig=True)
    Out[30]: cos(x)*cos(y) - sin(x)*sin(y)


Simplificación
--------------

Usa simplify si quieres transformar una expresión en algo más sencillo::

    In [19]: simplify((x+x*y)/x)
    Out[19]: 1 + y


Simplificación es un término vago, es por ello que existen alternativas
más precisas que simplify: powsimp (simplificación de
exponentes), trigsimp (para expresiones trigonométricas) , logcombine,
radsimp, together.

Ejercicios
----------

1. Calcular la forma expandida de :math:`(x+y)^6`.
2. Simplificar la expresión trigonométrica sin(x) / cos(x)

  
Cálculo
=======

Límites
-------

Los límites son fáciles de usar en SymPy, siguen la sintáxis limit(función,
variable, punto). Así, para calcular el límite de f(x) como x -> 0, deberías
usar la siguiente expresión limit(f, x, 0)::

   >>> limit(sin(x)/x, x, 0)
   1

también puedes calcular el límite en el infinito::

   >>> limit(x, x, oo)
   oo

   >>> limit(1/x, x, oo)
   0

   >>> limit(x**x, x, 0)
   1


.. index:: differentiation, diff

Diferenciación/Derivación
-------------------------

You can differentiate any SymPy expression using ``diff(func,
var)``. Examples::

    >>> diff(sin(x), x)
    cos(x)
    >>> diff(sin(2*x), x)
    2*cos(2*x)

    >>> diff(tan(x), x)
    1 + tan(x)**2

You can check, that it is correct by::

    >>> limit((tan(x+y)-tan(x))/y, y, 0)
    1 + tan(x)**2

Higher derivatives can be calculated using the ``diff(func, var, n)`` method::

    >>> diff(sin(2*x), x, 1)
    2*cos(2*x)

    >>> diff(sin(2*x), x, 2)
    -4*sin(2*x)

    >>> diff(sin(2*x), x, 3)
    -8*cos(2*x)


Series expansion
----------------

SymPy also knows how to compute the Taylor series of an expression at
a point. Use ``series(expr, var)``::

    >>> series(cos(x), x)
    1 - x**2/2 + x**4/24 + O(x**6)
    >>> series(1/cos(x), x)
    1 + x**2/2 + 5*x**4/24 + O(x**6)


Exercises
---------

1. Calculate :math:`\lim{x->0, sin(x)/x}`
2. Calulate the derivative of log(x) for x.

.. index:: integration

Integration
-----------

SymPy has support for indefinite and definite integration of transcendental
elementary and special functions via `integrate()` facility, which uses
powerful extended Risch-Norman algorithm and some heuristics and pattern
matching. You can integrate elementary functions::

    >>> integrate(6*x**5, x)
    x**6
    >>> integrate(sin(x), x)
    -cos(x)
    >>> integrate(log(x), x)
    -x + x*log(x)
    >>> integrate(2*x + sinh(x), x)
    cosh(x) + x**2

Also special functions are handled easily::

    >>> integrate(exp(-x**2)*erf(x), x)
    pi**(1/2)*erf(x)**2/4

It is possible to compute definite integral::

    >>> integrate(x**3, (x, -1, 1))
    0
    >>> integrate(sin(x), (x, 0, pi/2))
    1
    >>> integrate(cos(x), (x, -pi/2, pi/2))
    2

Also improper integrals are supported as well::

    >>> integrate(exp(-x), (x, 0, oo))
    1
    >>> integrate(exp(-x**2), (x, -oo, oo))
    pi**(1/2)


.. index:: equations; algebraic, solve


Exercises
---------

  

Equation solving
================

SymPy is able to solve algebraic equations, in one and several
variables::

    In [7]: solve(x**4 - 1, x)
    Out[7]: [I, 1, -1, -I]

As you can see it takes as first argument an expression that is
supposed to be equaled to 0. It is able to solve a large part of
polynomial equations, and is also capable of solving multiple
equations with respect to multiple variables giving a tuple as second
argument::

    In [8]: solve([x + 5*y - 2, -3*x + 6*y - 15], [x, y])
    Out[8]: {y: 1, x: -3}

It also has (limited) support for trascendental equations::

   In [9]: solve(exp(x) + 1, x)
   Out[9]: [pi*I]

Another alternative in the case of polynomial equations is
`factor`. `factor` returns the polynomial factorized into irreducible
terms, and is capable of computing the factorization over various
domains::

   In [10]: f = x**4 - 3*x**2 + 1
   In [11]: factor(f)
   Out[11]: (1 + x - x**2)*(1 - x - x**2)

   In [12]: factor(f, modulus=5)
   Out[12]: (2 + x)**2*(2 - x)**2



SymPy is also able to solve boolean equations, that is, to decide if a
certain boolean expression is satisfiable or not. For this, we use the
function satisfiable::

   In [13]: satisfiable(x & y)
   Out[13]: {x: True, y: True}

This tells us that (x & y) is True whenever x and y are both True. If
an expression cannot be true, i.e. no values of its arguments can make
the expression True, it will return False::

   In [14]: satisfiable(x & ~x)
   Out[14]: False


Exercises
---------

1. Solve the system of equations :math:`x + y = 2`, :math:`2\cdot x + y = 0`
2. Are there boolean values ``x``, ``y`` that make ``(~x | y) & (~y | x)`` true?


.. Polynomial computations
.. =======================

.. SymPy has a rich module of efficient polynomial routines. Some of the
.. most commonly used methods are factor, gcd


Linear Algebra
==============

.. index:: Matrix

Matrices
--------

Matrices are created as instances from the Matrix class::

    >>> from sympy import Matrix
    >>> Matrix([[1,0], [0,1]])
    [1, 0]
    [0, 1]

unlike a NumPy array, you can also put Symbols in it::

    >>> x = Symbol('x')
    >>> y = Symbol('y')
    >>> A = Matrix([[1,x], [y,1]])
    >>> A
    [1, x]
    [y, 1]

    >>> A**2
    [1 + x*y,     2*x]
    [    2*y, 1 + x*y]


.. index:: equations; differential, diff, dsolve

Differential Equations
----------------------

SymPy is capable of solving (some) Ordinary Differential
Equations. sympy.ode.dsolve works like this::

    In [4]: f(x).diff(x, x) + f(x)
    Out[4]:
       2
      d
    ─────(f(x)) + f(x)
    dx dx

    In [5]: dsolve(f(x).diff(x, x) + f(x), f(x))
    Out[5]: C₁*sin(x) + C₂*cos(x)

Keyword arguments can be given to this function in order to help if
find the best possible resolution system. For example, if you know
that it is a separable equations, you can use keyword hint='separable'
to force dsolve to resolve it as a separable equation.

   In [6]: dsolve(sin(x)*cos(f(x)) + cos(x)*sin(f(x))*f(x).diff(x), f(x), hint='separable')
   Out[6]: -log(1 - sin(f(x))**2)/2 == C1 + log(1 - sin(x)**2)/2


Exercises
---------

1. Solve the Bernoulli differential equation x*f(x).diff(x) + f(x) - f(x)**2

.. warning::

   TODO: correct this equation and convert to math directive!

2. Solve the same equation using hint='Bernoulli'. What do you observe ?
