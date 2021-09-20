# Making Approximation Easy
Let programmers specify that specific functions don't need exact results- computing them in a more CPU or RAM effecient way, with loss of precision, is fine.

## Goals
* Running programs with approximations is always at least as fast as without them, amortized across lots of runs
* Minimal need for programmers to guide how approximations work for decent results- should be as similar to "standard" numerical programming as possible
* Ideally, support code that links external libraries
* Side effects of approximated code do NOT need to be maintained- it's implicitly fine to treat approximated functions as pure, and the side effects like logging or network calls may or may not occur.

## Possible interfaces
* C++ library that wraps code
  * Potentially requires that code to be written with e.g. Boost autodiff support
* Modify a compiler to support an "epproximable" function attribute, similar to [the pure and const attributes](https://gcc.gnu.org/onlinedocs/gcc/Common-Function-Attributes.html)
 * This could either approximate the function at compile time by e.g. processing the IR or compiling with [ffast-math](https://gcc.gnu.org/onlinedocs/gcc/Common-Function-Attributes.html), or insert extra logging code track inputs/outputs to the function, and use this information to guide optimizations during recompilation al la profile guided optimization.
* Support some kind of just in time compilation
 * Presumably this would be much easier in Julia than C++

## Optimization techniques
* Use machine learning models trained on the function input/output- supervised learning is already effectively function approximation, let's make that literal
  * The downside is that this requires lots of "plausible" inputs, and doesn't really take advantage of our already having an exact solution for the function
* Statically analyze the code
  * e.g. look for ways to skip iterations, or to do marginal updates to the function result evaluated at a nearby point
    * For example, if function inputs tend to be in clustered regions, compute the gradient of the function at the input on the first call, then return the linear approximation result
* Skip error checking- e.g. assume no overflows
* If there's a pattern to how a function is called (e.g. always on input X, then X+1), and the solution to X and X+1 share code, compute both at once and cache the later result
  * This isn't actually approximation- I wonder if compilers already do that?
* Intelligently select from all of the above

## Relevant research
* [ACCEPT](https://dada.cs.washington.edu/research/tr/2015/01/UW-CSE-15-01-01.pdf)

## Impure interests
For transparency sake, I should note that I'm also interested in learning more about C++ compilers (esp GCC) for my career, and learning Julia both because I think it's an intrinsicly interesting language and potentially a good fit for finance.

