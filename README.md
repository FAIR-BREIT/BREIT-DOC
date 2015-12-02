# [BREIT: Balance Rate Equations for Ion Transportation](http://breit.gsi.de/)

## Introduction

[BREIT](http://breit.gsi.de/) is an online application which solve analytically the equilibrium and non-equilibrium charge state distributions equations (c.f. [HANS-DIETER BETZ Rev. Mod. Phys. 44, 465](http://journals.aps.org/rmp/abstract/10.1103/RevModPhys.44.465)), provided the cross-sections and initial conditions as input. 
The differential equations (non-equilibrium case) are solved using the eigenvalues decomposition method, and the asymptotic limits (equilibrium case) are solved by matrix inversion. 

## Input file format

BREIT needs electron-loss and -capture cross-sections, as well as initial conditions as inputs in order to solve the (non-equilibrium) Betz equations. Other inputs are required, like cross-section units, thickness units, target mass number ... Only charge (or state) q greater or equal than zero and less than 200 are supported. 

#### Format description

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

The proper syntax of the categories and the keys must be respected and will be described later.
Input file examples can be found [here](https://github.com/FAIR-BREIT/BREIT-CORE/blob/master/data/input/Example-8lvl-system-bis.txt) or [there](https://github.com/FAIR-BREIT/BREIT-CORE/blob/master/data/input/Example-15lvl-system.txt). 

#### Category and key-value description

##### [projectile]
The [projectile] category has two key-value pairs, namely :

* symbol (default=unknown projectile)
* energy (default=unknown energy)

and are only used to form the title in the output files. Both keys take a string as value.
Example:
```bash
    [projectile]
    symbol      = U
    energy      = 1.4 MeV/u
```

If the key-value pairs are not provided in the input file, the default values shown above in parenthesis are assigned.

##### [target]
The [target] category has three key-value pairs, namely :

* symbol      (default=unknown target)
* mass.number (required, no default)
* pressure    (default=unknown target pressure)

The symbol and pressure keys are only used to form the title in the output files. These two keys take a string as value. The mass.number key take a floating point number (double precision) and is used for the computation of the scaling factor of the input cross-section coefficients. Therefore the mass.number value is a required value that must be positive and provided by the user. Note that the atomic mass value can be given instead of the mass number for a proper scaling.

Example:
```bash
    [target]
    symbol      = Ar
    mass.number = 40
    pressure    = 1 mbar
```


##### [thickness]
The [thickness] category has four key-value pairs, namely :

* unit          (default=mug/cm2)
* maximum       (default=20.0)
* minimum       (default=1.e-3)
* point.number  (default=1000)

The point.number key takes integer numbers, the maximum and minimum keys take floating point numbers, and are used for ploting the figures and printing the table. Because of the log scale of the thickness axis, the minimum must be greater than 0.0. If it is set to 0.0, the corresponding value will be reassigned to 1.e-10. The unit key take a string as value and is used for the computation of the scaling factor of the input cross-section coefficients.
The supported units of the penetration depth, required for the non-equilibrium solutions, are : "10 mg/cm2" or "xg/cm2" where x is a [SI prefix](https://en.wikipedia.org/wiki/Metric_prefix). Below is the list of supported units for the penetration depth (target thikness) :

* fg/cm2        (scale factor =  1.e-15)
* pg/cm2        (scale factor =  1.e-12)
* ng/cm2        (scale factor =  1.e-9)
* mug/cm2       (scale factor =  1.e-6)
* mg/cm2        (scale factor =  1.e-3)
* cg/cm2        (scale factor =  1.e-2)
* 10 mg/cm2     (scale factor =  1.e-2)
* g/cm2         (scale factor =  1.0)
* kg/cm2        (scale factor =  1.e+3)
* Mg/cm2        (scale factor =  1.e+6)
* Gg/cm2        (scale factor =  1.e+9)
* Tg/cm2        (scale factor =  1.e+12)
* Pg/cm2        (scale factor =  1.e+15)

Proper units and proper minimum/maximum values combination allow a better visibility/details of the resulting curves.


Example:
```bash
    unit         = cg/cm2
    maximum      = 30
    minimum      = 1e-4
    point.number = 200
```


##### [fraction]
The [thickness] category has three key-value pairs, namely :

* minimum (default=0.0)
* maximum (default=1.1)
* epsilon (default=0.001)

The maximum and minimum keys take floating point numbers, and are only used for plotting the figures (i.e. setting up the ion fraction axis range). The epsilon key takes a floating point as value (double precision) and is used to compute the estimation of the distance to equilibrium for each ion fraction. The algorithm used for this estimation is the following :

For each fraction, starting from xmax (=maximum key, defined in the [thickness] category) and going towards xmin, the distance-to-equilibrium is estimated if the following condition is satisfied:

<img src="https://github.com/FAIR-BREIT/BREIT-DOC/blob/master/figures/distance-to-equilibrium-condition.png" width="400">

If the condition is satisfied at x=xmax, a warning in the output results is printed. In addtion to the epsilon value, the estimation may be sensitive to

* x-axis range
* point.number
* scaling factor like the thickness units



Example:
```bash
    [fraction]
    maximum     = 1.
    epsilon     = 0.001
```
Note that here the minimum is not provided and will have a value of 0.0 by default.



##### [cross.section]

Supported cross-section units are : "cm2", "1e-16 cm2", or "xb", where x is a [SI prefix](https://en.wikipedia.org/wiki/Metric_prefix). 




#### Output


Output equilibrium results are provided on demand in the form of :

* 1-electron approximation (txt file)
* no-approximation obtained from matrrix inversion (txt file)
* figures (pdf file)

Output non-equilibrium results are provided on demand in the form of :

* analytical formulae (txt file)
* table (txt file)
* figures (pdf file)






