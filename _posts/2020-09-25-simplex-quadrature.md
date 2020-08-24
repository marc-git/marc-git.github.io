# Gaussian Quadrature on Simplexes 

It isn't easy to find reliable [Gaussian Quadrature](https://en.wikipedia.org/wiki/Gaussian_quadrature) points 
for multiple dimensional domains with strictly positive weights.  If one finds them, the uncomfortable risk  
of typing errors when transcribing them is annoying. 

This article describes a procedure of producing Gaussian Quadrature points from the roots of 
[Lagrange Polynomials](https://en.wikipedia.org/wiki/Lagrange_polynomial) in 1D and then applying a 
[Cartesian Product](https://en.wikipedia.org/wiki/Cartesian_product) to expand this out to more dimensions. This
takes us to the [square](https://en.wikipedia.org/wiki/Square), the [cube](https://en.wikipedia.org/wiki/Cube),
the [Tesseract](https://en.wikipedia.org/wiki/Tesseract) or other [Hypercubes](https://en.wikipedia.org/wiki/Hypercube).

Following that a simple mapping from the hypercube to the n-[simplex](https://en.wikipedia.org/wiki/Simplex) is made 
algebraically.  The mapped points on the hypercube can then be applied to the n-simplex, which is useful for numerical
integration on Finite Elements.

This article will go through the basics of:
 - Finding roots of Lagrange Polynomials
 - Cartesian Product Calculation
 - Mapping to the Simplex
 - PyQuadrat
 
## Lagrange Polynomials
Why bother with Lagrange Polynomials? They have a number of great properties that makes them useful:
  * They are orthogonal on bounded intervals.
  * They have good approximation properties for functions of lesser degree.
  * They exclude the end points (sometimes a negative).
  
We will be working with normalised Lagrangian Polynomials.  That means that the value $f_n(1) = 1$
