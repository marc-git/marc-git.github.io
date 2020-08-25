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
  
We will be working with normalised Legendre Polynomials (Monomials).  That means that the value each polynomial is one at x=1.  This has a number of consequences but the most useful one is that we can construct the sequence of polynomials up to degree n simply by applying the orthogonality condition. 

The first Legendre polynomial is the function equal to 1 everywhere on the interval [-1,1]. Then the next polynomial of one degree higher must be equal f(x)=x.  In fact because of the first polynomial, all of the following polynomials will integrate to zero over the domain. 

### Rodrigues' Generator
We will use the Rodrigues' generating function in the following form

$$P_n(x)=\sum_{k=0}^n \binom{n}{k} \binom{n+k}{k}\left(\frac{x-1}{2} \right)^k
$$
