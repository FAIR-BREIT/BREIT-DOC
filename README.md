# [BREIT: Balance Rate Equations for Ion Transportation](http://breit.gsi.de/)

## Introduction

[BREIT](http://breit.gsi.de/) is an online application which solve analytically the equilibrium and non-equilibrium charge state distributions equations (c.f. [HANS-DIETER BETZ Rev. Mod. Phys. 44, 465](http://journals.aps.org/rmp/abstract/10.1103/RevModPhys.44.465)), provided the cross-sections and initial conditions as input. 
The differential equations (non-equilibrium case) are solved using the eigenvalues decomposition method, and the asymptotic limits (equilibrium case) are solved by matrix inversion. 

## Input file format

BREIT needs electron-loss and -capture cross-sections, as well as initial conditions as inputs in order to solve the (non-equilibrium) Betz equations. Other inputs are required, like cross-section units, thickness units, target mass number ... Only charge (or state) q greater or equal than zero and less than 200 are supported. 

#### Format description : introduction

An [INI-like format](https://en.wikipedia.org/wiki/INI_file) is used as input file. The INI-like input files are divided in several categories. Categories are indicated by square brackets and are :

* [projectile]
* [target]
* [thickness]
* [fraction]
* [cross.section]

Under each category key-value pairs can be found, for example as follow:
```bash
    # <- comments marker
    [Category1]
    key1 = value1

    ################ other comments
    [Category2]
    key2 = value2
```

The proper syntax of the categories and the keys must be respected and will be described later. The order of the keys inside a category is irrelevant. 
Some keys are not required to run the program because they have default values, but some others, however, are required.
Input file examples can be found [here](https://github.com/FAIR-BREIT/BREIT-CORE/blob/master/data/input/Example-8lvl-system-bis.txt) or [there](https://github.com/FAIR-BREIT/BREIT-CORE/blob/master/data/input/Example-15lvl-system.txt). 

#### Categories and keys-values description

##### [projectile] category
The [projectile] category has two key-value pairs, namely :


| key    | default value      | value type |
|:------:|:------------------:|:----------:|
| symbol | unknown projectile | string     |
| energy | unknown energy     | string     |

and are only used to form the title in the output files. Both keys take a string as value.
###### Example:
```bash
    [projectile]
    symbol      = U
    energy      = 1.4 MeV/u
```

If the key-value pairs are not provided in the input file, the default values shown above in parenthesis are assigned.

##### [target] category
The [target] category has three key-value pairs, namely :

| key         | default value           | value type                        |
|:-----------:|:-----------------------:|:---------------------------------:|
| symbol      | unknown target          | string                            |
| mass.number | required, no default    | floating point (double precision) |
| pressure    | unknown target pressure | string                            |

The symbol and pressure keys are only used to form the title in the output files. These two keys take a string as value. The mass.number key take a floating point number (double precision) and is used for the computation of the scaling factor of the input cross-section coefficients. Therefore the mass.number value is a required value that must be positive and provided by the user. Note that the atomic mass value can be given instead of the mass number for a proper scaling.

###### Example:
```bash
    [target]
    symbol      = Ar
    mass.number = 40
    pressure    = 1 mbar
```

##### [thickness] category
The [thickness] category has four key-value pairs, namely :

| key          | default value           | value type                        |
|:------------:|:-----------------------:|:---------------------------------:|
| unit         | mug/cm2                 | string                            |
| minimum      | 1.e-3                   | floating point (double precision) |
| maximum      | 20.0                    | floating point (double precision) |
| point.number | 1000                    | integer                           |

The point.number key takes integer numbers, the maximum and minimum keys take floating point numbers, and are used for ploting the figures and printing the table. Because of the log scale of the thickness axis, the minimum must be greater than 0.0. If it is set to 0.0, the corresponding value will be reassigned to 1.e-10. The unit key take a string as value and is used for the computation of the scaling factor of the input cross-section coefficients.
The supported units of the penetration depth, required for the non-equilibrium solutions, are : "10 mg/cm2" or "xg/cm2" where x is a [SI prefix](https://en.wikipedia.org/wiki/Metric_prefix). Below is the list of supported units for the penetration depth (target thikness) :

| Supported units | Scale factor |
|:---------------:|:------------:|
| fg/cm2          | 1.e-15       |
| pg/cm2          | 1.e-12       |
| ng/cm2          | 1.e-9        |
| mug/cm2         | 1.e-6        |
| mg/cm2          | 1.e-3        |
| cg/cm2          | 1.e-2        |
| 10 mg/cm2       | 1.e-2        |
| g/cm2           | 1.0          |
| kg/cm2          | 1.e+3        |
| Mg/cm2          | 1.e+6        |
| Gg/cm2          | 1.e+9        |
| Tg/cm2          | 1.e+12       |
| Pg/cm2          | 1.e+15       |

Proper units and proper minimum/maximum values combination allow a better visibility/details of the resulting curves.


###### Example:
```bash
    unit         = cg/cm2
    maximum      = 30
    minimum      = 1e-4
    point.number = 200
```


##### [fraction] category
The [thickness] category has three key-value pairs, namely :

| key          | default value  | value type                        |
|:------------:|:--------------:|:---------------------------------:|
| minimum      | 0.0            | floating point (double precision) |
| maximum      | 1.1            | floating point (double precision) |
| epsilon      | 0.001          | floating point (double precision) |

The maximum and minimum keys take floating point numbers, and are only used for plotting the figures (i.e. setting up the ion fraction axis range). The epsilon key takes a floating point as value (double precision) and is used to compute the estimation of the distance to equilibrium for each ion fraction. The algorithm used for this estimation is the following :

For each fraction, starting from xmax (=maximum key, defined in the [thickness] category) and going towards xmin, the distance-to-equilibrium is estimated if the following condition is satisfied:

<img src="https://github.com/FAIR-BREIT/BREIT-DOC/blob/master/figures/distance-to-equilibrium-condition.png" width="400">

If the condition is satisfied at x=xmax, a warning in the output results is printed. In addtion to the epsilon value, the estimation may be sensitive to

* x-axis range
* point.number
* scaling factor like the thickness units



###### Example:
```bash
    [fraction]
    maximum     = 1.
    epsilon     = 0.001
```
Note that in this example the minimum key is not provided and will have a value of 0.0 by default.



##### [cross.section] category
The [cross.section] category has one key-value pair, namely the cross-sections unit, and variable key-value paris depending on the dimension of the system to be solved.
The variable key-value pairs are the initial conditions (ion charge state in a vector form) and cross-section coefficients in a square matrix form.


| key          | meaning             | default value           | value type                        | comments                                                      |
|:------------:|:-------------------:|:-----------------------:|:---------------------------------:|:-------------------------------------------------------------:|
| unit         | cross-section units | required, no default    | string                            | see table below                                               |
|              |                     |                         |                                   |                                                               |
| F0.imin      | intial conditions   | 0.0                     | floating point (double precision) | imin is an integer (unsigned)                                 |
| F0.i         | intial conditions   | 0.0                     | floating point (double precision) |             imin < i < imax        |
| F0.imax      | intial conditions   | 0.0                     | floating point (double precision) | imax is an integer (unsigned).  the sum of F0.imin to F0.imax must be 1                                |
|              |                     |                         |                                   |                                                               |
| Q.imin.jmin  | cross-sections coef | 0.0                     | floating point (double precision) | imin, jmin are integers (unsigned)                |
| Q.i.j        | cross-sections coef | 0.0                     | floating point (double precision) | i and j are integers (unsigned). Moreover imin < i < imax and jmin < j < jmax                          |
| Q.jmax.jmax  | cross-sections coef | 0.0                     | floating point (double precision) | imax, jmax are integers (unsigned). Moreover imax-imin must be equal to jmax-jmin to form a nonsingular square matrix i.e. no zero-rows or zero-columns should be provided          |


The supported cross-section units are : "cm2", "1e-16 cm2", or "xb", where x is a [SI prefix](https://en.wikipedia.org/wiki/Metric_prefix). The supported units are summarized in the following table :

| Supported units | Scale factor |
|:---------------:|:------------:|
| cm2             | 1.0          |
| 1e-16 cm2       | 1.e-16       |
| pb              | 1.e-36       |
| nb              | 1.e-33       |
| mub             | 1.e-30       |
| mb              | 1.e-27       |
| b               | 1.e-24       |
| kb              | 1.e-21       |
| Mb              | 1.e-18       |
| Gb              | 1.e-15       |
| Tb              | 1.e-12       |

#### Output


Output equilibrium results are provided on demand in the form of :

* 1-electron approximation (txt file)
* no-approximation obtained from matrrix inversion (txt file)
* figures (pdf file)

Output non-equilibrium results are provided on demand in the form of :

* analytical formulae (txt file)
* table (txt file)
* figures (pdf file)






