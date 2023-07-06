## Initial Porosity Profile

The initial porosity profile is usually modelled based on an exponential
decrease based on the equation

Where *d* is depth, *Φ*(*d*) is porosity at depth *d*, and
*a*<sub>*p**o**r*</sub>,*b*<sub>*p**o**r*</sub>, and
*c*<sub>*p**o**r*</sub> are empirical parameters with the following
interpretations:

-   *c*<sub>*p**o**r*</sub> is the limit porosity
-   *b*<sub>*p**o**r*</sub> is a “Compressibility constant”that
    determines how fast porosity approaches *c*<sub>*p**o**r*</sub> with
    depth
-   *a*<sub>*p**o**r*</sub> + *c*<sub>*p**o**r*</sub> is the porosity at
    the sediment surface

The old parametrization can be obtained by setting
*a*<sub>*p**o**r*</sub> = *b*<sub>*p**o**r*</sub> = 0 and
*c*<sub>*p**o**r*</sub> = *Φ*<sub>*i**n**i*</sub>

This is an empirical equation. Parametrizations might deviate,
compilations of parameters are available in the Literature.

Open Questions:

1.  Which parameter values do we use as benchmarks for comparisons
    between implementations?
2.  Should they be empirically realistic?
3.  *b*<sub>*p**o**r*</sub> needs to be adjusted when resaling the
    system to make it dimensionless. This needs to be specified (see GH
    issue [here](https://github.com/MindTheGap-ERC/LMA-Matlab/issues/4))

## Continuous ADZ

### Continuous ADZ

We are currently working with a step function that turns on the
aragonite dissolution. In the parametrization og LHeureux (2018), it is
given by

Where *d* ≥ 0 is depth below sediment surface. For simplicity, I use the
modified parameters
*x*<sub>*s**h**a**l**l**o**w*</sub> = *x*<sub>*d*</sub> and
*x*<sub>*d**e**e**p*</sub> = *x*<sub>*d*</sub> + *h*<sub>*d*</sub>.

We are looking for a new function *f*<sub>1</sub> that slowly turns on
Aragonite dissolution (i.e., is at least continuous). Hanno pointed out
that it should also have equal contributions to Aragonite dissolution as
the old formulation in the sense that

We define *f*<sub>1</sub> using 2 parameters:

-   *a*<sub>*o**n**s**e**t*</sub> ≥ 0, the length on which aragonite
    dissolutino is going from 0 to 1 (linearly)
-   *a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub> ≥ 0 the length over
    which aragonite dissolution goes from 1 to 0 (linearly) With the
    additional requirements that
    *f*<sub>1</sub>(*x*<sub>*s**h**a**l**l**o**w*</sub>) = *f*<sub>1</sub>(*x*<sub>*d**e**e**p*</sub>) = 0.5

This leads to the formulation

Additional assumptinos made on the parameters are:

-   *x*<sub>*s**h**a**l**l**o**w*</sub> − 0.5*a*<sub>*o**n**s**e**t*</sub> ≥ 0:
    Aragonite dissolution is zero at the sediment surface
-   *x*<sub>*d**e**e**p*</sub> + 0.5*a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub> ≤ *L*
    where *L* is the length of the system: Aragonite dissolution is zero
    at the bottom of the system
-   *x*<sub>*s**h**a**l**l**o**w*</sub> + 0.5*a*<sub>*o**n**s**e**t*</sub> \< *x*<sub>*d**e**e**p*</sub> − 0.5*a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub>:
    Aragonite dissolution is fully turned on at least once

The old parametrization can be recovered as limiting case with
*a*<sub>*o**n**s**e**t*</sub> = *a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub> → 0

#### REMAINING QUESTION:

-   The numerical instabilities are mainly occurring at the top of the
    ADZ. Do we need to turn off Aragonite slowly at the bottom ( choose
    *a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub> \> 0)?
-   If we turn off Aragonite dissolution slowly, we need to make sure
    all reactants can react before leaving the bottom of the system. How
    do we do that? A simple way would be to increase the size of the
    system from *L* to
    *L* + *a*<sub>*t**e**r**m**i**n**a**t**i**o**n*</sub>. However this
    changes our discretisation (and as a potential result, the numerical
    properties of the implementation).
-   Is the model assumption that Aragonite turned on fully at least once
    empirically realistic (or necessary)?

More general, what are reasonable assumptions on the shape of the ADZ?
Theresa collected some info on that from empirical studies. Emulating
those shapes leads to a lot of follow up questions on the model
(e.g. the ADZ irl is much more massive and deeper than in the model)

### Smooth ADZ (Not relevant anymore)

A smooth ADZ can be constructed the following way: 1. Start with the
function

here, *a* \> 0 is a parameter. It determines how fast the onset of the
ADZ is. In standard constructions, *a* = 1.

1.  Define the two cutoff functions

and

Then the smooth ADZ is given by the equation

Parameters are
*r*<sub>4</sub> \< *r*<sub>3</sub> \< *r*<sub>1</sub> \< *r*2 where \*
*r*<sub>4</sub> is where first aragonite dissolution starts \*
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
