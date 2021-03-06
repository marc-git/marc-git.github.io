---
layout: post
title: "Simplex Quadrature"
date: 2020-08-23
---

# Gaussian Quadrature on Simplices 

It isn't easy to find reliable [Gaussian Quadrature](https://en.wikipedia.org/wiki/Gaussian_quadrature) points for multiple dimensional domains with strictly positive weights.  If one finds them, the uncomfortable risk of typing errors when transcribing them is annoying. 

This article describes a procedure of producing Gaussian Quadrature points from the roots of [Legendre Polynomials](https://en.wikipedia.org/wiki/Legendre_polynomials) in 1D and then applying a [Cartesian Product](https://en.wikipedia.org/wiki/Cartesian_product) to expand this out to more dimensions. This takes us to the [square](https://en.wikipedia.org/wiki/Square), the [cube](https://en.wikipedia.org/wiki/Cube), the [Tesseract](https://en.wikipedia.org/wiki/Tesseract) or other [Hypercubes](https://en.wikipedia.org/wiki/Hypercube).

Following that a simple mapping from the hypercube to the n-[simplex](https://en.wikipedia.org/wiki/Simplex) is made algebraically.  The mapped points on the hypercube can then be applied to the n-simplex, which is useful for numerical integration on Finite Elements.

This article will go through the basics of:
 - Finding roots of Lagrange Polynomials
 - Cartesian Product Calculation
 - Mapping to the Simplex
 - PyQuadrat
 
## Legendre Polynomials
Why bother with Legendre Polynomials? They have a number of great properties that makes them useful:
  * They are orthogonal on bounded intervals.
  * They are a complete system (have good approximation properties for functions of lesser degree).
  * They exclude the end points (sometimes a negative).
  
We will be working with normalised Legendre Polynomials (monic polynomials).  That means that the value each polynomial is one at x=1.  This has a number of consequences but the most useful one is that we can construct the sequence of polynomials up to degree n simply by applying the orthogonality condition. 

The first Legendre polynomial is the function equal to 1 everywhere on the interval [-1,1]. Then the next polynomial of one degree higher must be equal f(x)=x.  In fact because of the first polynomial, all of the following polynomials will integrate to zero over the domain. 

### Rodrigues' Generator
We will use the Rodrigues' generating function in the following form

$$P_n(x)=\sum_{k=0}^n \binom{n}{k} \binom{n+k}{k}\left(\frac{x-1}{2} \right)^k
$$


### Derivatives of Legendre Polynomials
Actually what we need is the derivative of a Legendre Polynomial.  For that we use Bonnet's Recursion Formula which has :

$$(n+1)P_{n+1}(x) = (2n+1)xP_n(x)-nP_{n-1}(x)
$$
and 
$$\frac{x^2 -1}{n}\frac{d}{dx}P_n(x)=xP_n(x)-P_{n-1}(x)
$$

## Gauss Legendre Quadrature
### Weights
As per the details in the [Wikipedia Article](https://en.wikipedia.org/wiki/Gaussian_quadrature#Gauss%E2%80%93Legendre_quadrature) the weights of the integration points are calculated as 
$$w_i = \frac{2}{\left(1-x_i^2\right)\left(P_n'(x_i)\right)}
$$
where the x_i is the quadrature node point in the interval. The locations of those points correspond to the roots of the polynomial. 

### Nodes
See (Day and Romero, 2005) for a complete description.  In short, the Legendre Polynomials satsify a three-term recurrence with :
$$\gamma_{n+1}P_{n+1}(x)=xP_n(x)-\gamma_nP_{n-1}(x)
$$
with 
$$\gamma_n = \frac{n}{\sqrt{4n^2-1}}
$$
It follows then that the companion matrix has the form
$$h_{i+1,i}=h_{i,i+1}=\gamma_n 
$$
and the eigenvalues of that matrix are the roots of the polynomial of degree n. This can be readily solved with e.g. the QR-algorithm. 

## Cartesian product
The n-cartesian product of the roots gives the integration points in the interval [-1,1]^n. The product of the corresponding weights also gives the final weight for a given integration point. 

In the Python module [PyQuadrat](https://github.com/marc-git/pyquadrat) this is performed by taking the return of the function `gauss_points_box`.  The first argument is the degree of the polynomial; the second is the number of space dimensions.  The roots of the one-dimensional Legendre polynomial to the specified degree are first calculated using `calculate_legendre_roots` and the returned vector is converted into a list of points with the associated weight as the last column of the list. 

## Mapping to the simplex
The n-simplex has a vertex at each euclidean vector and at the origin.  The [-1,1]^n hypercube can be mapped to the n-simplex by first shrinking the hypercube to the [0,1]^n form and then mapping each vertex to its nearest vertex, or, if more than one vertex is equally near, taking the average.  In the case of mapping the square to the triangle, this implies mapping the point (1,1) to (0.5,0.5).  It is analagous in higher dimensions however some vertices will map to lines and others to surfaces or hypersurfaces. 

This simple mapping is the reason the point density tends to bunch at this point.  The determinant of the jacobian is not constant.

In [PyQuadrat](https://github.com/marc-git/pyquadrat) this is done by putting the output of `gauss_points_box` into the function `gauss_map_to_simplex`.  The latter relies heavily on the function `build_mapping` which does what was described above. Note that all the weights are strictly positive.

![Sample](https://github.com/marc-git/pyquadrat/blob/master/gauss_int_12.png)
