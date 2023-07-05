## Initial Porosity Profile

The initial porosity profile is usually modelled based on an exponential
decrease based on the equation

Where *d* is depth, *Φ*(*d*) is porosity at dpeth *d*, and *a*,*b*, and
*c* are empirical parameters with the following interpretations:

-   *c* is the limit porosity
-   *b* is a “Compressibility constant”that determines how fast porosity
    approaches *c* with depth
-   *a* + *c* is the porosity at the sediment surface

This is an empirical equation. Parametrizations might deviate,
compilations of parameters are available in the Literature

## Continuous ADZ

A smooth ADZ can be constructed the following way: 1. Start with the
function

here, *a* &gt; 0 is a parameter. It determines how fast the onset of the
ADZ is. In standard constructions, *a* = 1.

1.  Define the two cutoff functions

and

Then the smooth ADZ is given by the equation

Parameters are
*r*<sub>4</sub> &lt; *r*<sub>3</sub> &lt; *r*<sub>1</sub> &lt; *r*2
where \* *r*<sub>4</sub> is where first aragonite dissolution starts \*
*r*<sub>3</sub> depth where Aragonite dissolution reaches its maximum
for the first time \* *r*<sub>1</sub> deth where Aragomnite dissolution
decreases again for the forst time \* *r*<sub>2</sub> depth where
Aragonite dissolution drops to zero again

(NOTE: Reorder parameter indices, check implementations - @ Niklas)

This formulation of the ADZ id based on [this
discussion](https://math.stackexchange.com/questions/101480/are-there-other-kinds-of-bump-functions-than-e-frac1x2-1)
and [this book, p. 41 - 43](https://doi.org/10.1007/978-1-4419-9982-5).
By construction, it has continuous derivatives of all orders. Open
questions are:

-   Do we need an expression for the (n-th) derivative of this equation
    for the implementation?
-   Is it necessary to calculate function values every iteration? If no,
    can we store function values in an array before iteration and reuse
    them?
-   If function values need to be calculated ever iteration, is the
    expression numerically stable and sufficiently fast? (I have some
    concerns about the exp (−*x*) terms and division by small numbers
    here - Niklas)
-   If not, look for alternative expressions with potentially lower
    order of continuous differentiability. (Comsol uses 2nd order
    continuous bump functions, this might be an option -Niklas)
