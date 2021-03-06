# Summaries of lectures: 18.330 (spring 2020)

## Lecture 1: Invitation (3 Feb)

We started off discussing the logistics of the course and advertised
Steven Johnson's Julia tutorial on Friday Feb 7 from 5-7pm in 32-141.

Then we turned to discuss what the course is about with the example
of dropping a ball with air resistance and asking when it will hit the floor.
This will be modelled by some kind of ordinary differential equation $\dot{y} = f(y)$
that we would like to *solve*, i.e. find a solution $y(t)$ as a function of time,
and then find a *root*, i.e. the time $t^*$ when $y(t^*) = 0$.

In general it will be *impossible* to solve this problem exactly; the course is
about developing ways to find *approximate* solutions, and indeed to be able
to approximate the true solution as closely as we would like.

As a concrete example we discussed the collision of two discs modelling particles
in a fluid. If we take small time steps $\delta t$, after every step we will check an
overlap condition by evaluating a function $f(t)$ which calculates the current distance
between the two discs at time $t$. We have $f > 0$ if the discs do not overlap and
$f < 0$ if they do overlap. We are interested in finding the root $t^*$ for which
$f(t^*) = 0$, when they will just touch.

As an example of a root-finding algorithm, we implemented the bisection algorithm
including a tolerance $\epsilon$, and showed that it worked with `BigFloat`.

## Lecture 2: Representing numbers (5 Feb)

We began by looking at a visualization of a hard sphere fluid (using the Julia
package https://github.com/JuliaDynamics/HardSphereDynamics.jl that I wrote).

Then we took the bisection algorithm from last time and added the possibility to
store intermediate values in a Julia **vector** (with type `Array{Float64,1}` -- a *1*-dimensional array that stores numbers of type `Float64`). Then we plotted the data using the `Plots.jl` package, and saw that a straight line on a semi-log
graph with a logarithmic $y$ axis corresponds to an *exponential* relationship between the $y$ and $x$ data:

$$y \sim C \exp(-\alpha x),$$

where $-\alpha$ is the slope of the straight line on the semi-log
graph.

Then we moved on to representing numbers on the computer.
Since we only have a finite amount of space available, we can
only represent a finite subset of all numbers. All numbers (and indeed any other kind of object) are represented as a sequence of **bits** (binary digits) stored somewhere in memory. The **type** of a variable tells Julia which kind of number the data corresponds to, and hence how to interpret it, i.e. its **behaviour**.

We started with Booleans (true or false), represented by 1 bit. Then we saw that integers are represented as binary expansions, although something strange happens for negative integers ("two's complement").

Rational numbers correspond to pairs $(p, q)$ of integers, but
we want to define new operations on them, so we *defined our own rational type* using Julia's immutable `struct` composite types,
and did an example of how to define a new operation on such a type, and how to change how objects of that type are displayed (by overloading `Base.show`).

To represent real numbers we started off thinking about **fixed-point numbers**, which are basically integers with a decimal point (actually a "binary point") inserted at a fixed place in the binary expansion of an integer.

But these can't represent a wide enough range of numbers, so instead we turned to **floating-point numbers**. These are still just special rational numbers, with denominators that are powers of 2. They are spaced in an unusual way along the line: larger values have more space between them (although the *relative* spacing is the same).

We saw how to "peel apart" a float in Julia and reconstruct its value.

## Lecture 3: Representing functions (7 Feb)

I started with a few comments about the [bibliography](`bibliography.md`). The book `Fundamentals of Numerical Computation` by Driscoll and Braun is a nice, recent, modern point of view at the level of the course. It covers much of what we will see in the course.

Then we briefly discussed floating-point arithmetic. The fundamental operations `+`, `-`, `*`, `/` and `sqrt` are guaranteed by the IEEE 754 standard to be **correctly rounded**: the result is the *closest* floating-point number to the true result, as if it were calculated in infinite precision. In practice the CPU only needs to use a few extra "guard bits" to do this.

Next we moved on to thinking about how to evaluate a function $f$, for example  $f(x) = \exp(x)$ (exponential). Since that's a difficult problem, we replace it by a simpler problem: we **approximate** $f$ by a function that is simpler to evaluate.

The only functions that we can actually calculate are **polynomials**, which are a finite sum of terms of the form
$p(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n$. We can evaluate
these just with the basic arithmetic operations `+`, `-` and `*`.
[In fact we can also evaluate *ratios* of two polynomials, $p_1(x) / p_2(x)$; these are called **rational functions**, and play an increasingly important role in numerical analysis.]

Then we saw that it is actually possible to approximate *any* continuous function $f$ on a *finite* interval $[a, b]$, arbitrarily close by a polynomial; this is the content of the **Weierstrass approximation theorem**.

How can we actually *find*, or *construct*, an approximating polynomial, though? We will see several methods to do so during the course, but we started with one motivated in mathematics: **Taylor series**.

For example, we know that $\exp(x) = \sum_{n=0}^\infty \frac{x^n}{n!}$. We still can't evaluate this **power series**, since it has an infinite number of terms. Instead, we **truncate** it by stopping after $N$ terms, which gives a polynomial, called the **Taylor polynomial of degree $N$** for the function $f$.

In doing so we commit a **truncation error** by omitting the infinite tail of the series. The Lagrange form of this remainder is a way of writing this as a single term involving evaluating the $(N+1)$ the derivative of $f$; however, it is evaluated at an unknown point $\xi$ in the interval. Nonetheless, this allows us to calculate a *bound* of the truncation error.

We then saw how to use the `TaylorModels.jl` package to do this calculation rigorously, using **interval arithmetic** to calculate guaranteed (mathematically rigorous) lower and upper bounds of all errors in the calculation (rounding errors and truncation error). We also saw how the `Interact.jl` package can easily generate nice interactive visualizations.
