# General-purpose optimisation with SOMA

The `soma` package provides an R implementation of the Self-Organising Migrating Algorithm, a general-purpose, stochastic optimisation algorithm developed originally by Ivan Zelinka.

The algorithm's approach is similar to that of genetic algorithms, although it is based on the idea of a series of "migrations" by a fixed set of individuals, rather than the development of successive generations. It can be applied to any cost-minimisation problem with a bounded parameter space, and is robust to local minima. Further details of the algorithm, and implementations for other languages, can be found at the [SOMA home page](http://www.ft.utb.cz/people/zelinka/soma/). Only the "all-to-one" strategy is currently implemented in this package.

## Usage

The `soma()` function provides the R interface to the SOMA algorithm. It is called with a function to minimise, a list consisting of minimum and maximum bounds for each parameter, an optional list of options. The cost function must take a numeric vector of parameters as its first argument, and return a numeric scalar representing the associated cost value.

As an example, consider the two-dimensional version of [Rastrigin's function](https://en.wikipedia.org/wiki/Rastrigin_function), which has many local minima, and a global minimum of zero at (0,0).

```R
rastrigin <- function(a) 20 + a[1]^2 + a[2]^2 - 10*(cos(2*pi*a[1])+cos(2*pi*a[2]))
```

![Rastrigin's function](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8b/Rastrigin_function.png/640px-Rastrigin_function.png)

We can attempt to find the global minimum with the call

```R
x <- soma(rastrigin, list(min=c(-5.12,-5.12),max=c(5.12,5.12)))
# INFO: Starting SOMA optimisation
# INFO: Migration limit (20) reached - stopping
# INFO: Leader is #4, with cost 1.67e-05
```

We can see from the informative output that on this run the best location has a cost function of `1.67e-05`, close to the true minimum value of zero. The location in search space is

```R
print(x$population[,x$leader])
# [1] -1.005039e-05  2.896569e-04
```

Again, this is very close to the true location of (0,0).

Finally, we can print the progress of the optimisation, showing the best cost function value at each iteration:

```R
plot(x)
```

![SOMA progress](http://www.clayden.org/files/soma/soma_progress.png)

## Options

The supported options are as follows. The defaults are as recommended in the reference below.

| Option name          | Description | Default value |
| -------------------- | ----------- | ------------- |
| `pathLength`         | The distance towards the leader that individuals may migrate. A value of 1 corresponds to the leader's position itself, and values greater than one (recommended) allow for some overshoot. | 3 |
| `stepLength`         | The granularity at which potential steps are evaluated. It is recommended that this not be a whole multiple of the `pathLength`. | 0.11 |
| `perturbationChance` | The probability that individual parameters are changed on any given step. | 0.1 |
| `minAbsoluteSep`     | The smallest absolute difference between the maximum and minimum cost function values. If the difference falls below this minimum, the algorithm will terminate. | 0 |
| `minRelativeSep`     | The smallest relative difference between the maximum and minimum cost function values. If the difference falls below this minimum, the algorithm will terminate. | 0.001 |
| `nMigrations`        | The maximum number of migrations to complete. | 20 |
| `populationSize`     | The number of individuals in the population. It is recommended that this be somewhat larger than the number of parameters being optimised over, and it should not be less than 2. | 10 |

## Reference

I. Zelinka (2004). SOMA - self-organizing migrating algorithm. In G.C. Onwubolu & B.V. Babu, eds, New optimization techniques in engineering. Volume 141 of "Studies in Fuzziness and Soft Computing", pp. 167-217. Springer.
