# [BREIT](http://breit.gsi.de/) - Documentation

---

**Table of Contents** 

- [1-Introduction](#1-introduction)
    - [Input file format](#input-file-format)
        - [Format description](#format description)
- [2-Categories and keys-values description](#2-categories-and-keys-values-description)
    - [[projectile] category](#projectile-category)
        - [Example](#example)
    - [[target] category](#target-category)
        - [Example](#example-1)
    - [[thickness] category](#thickness-category)
        - [Thickness units](#thickness-units)
        - [Example](#example-2)
    - [[fraction] category](#fraction-category)
        - [Example](#example-3)
        - [Remark about the epsilon value](#remark-about-the-epsilon-value)
    - [[cross.section] category](#crosssection-category)
        - [Cross-section units](#cross-section-units)
        - [Example](#example-4)
- [3-Output](#3-output)

---

## 1-Introduction

[BREIT](http://breit.gsi.de/) is an online application which solve analytically the equilibrium and non-equilibrium charge state distributions equations (c.f. [HANS-DIETER BETZ Rev. Mod. Phys. 44, 465](http://journals.aps.org/rmp/abstract/10.1103/RevModPhys.44.465)), provided the cross-sections and initial conditions as input. 
The differential equations (non-equilibrium case) are solved using the eigenvalues decomposition method, and the asymptotic limits (equilibrium case) are solved by matrix inversion. 

### Input file format

BREIT needs electron-loss and -capture cross-sections, as well as initial conditions as inputs in order to solve the (non-equilibrium) Betz equations. Other inputs are required, like cross-section units, thickness units, target mass number ... Only charge (or state) q greater or equal than zero and less than 200 are supported. 

#### Format description 

An [INI-like format](https://en.wikipedia.org/wiki/INI_file) is used as input file. The INI-like input files are divided in several categories. Categories are indicated by square brackets and are :

* [projectile]
* [target]
* [thickness]
* [fraction]
* [cross.section]

Under each category, key-value pairs can be found, for example as follow:
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

---

## 2-Categories and keys-values description

### [projectile] category
The [projectile] category has two key-value pairs, which are summarized in the table below :


| key    | default value      | value type |
|:------:|:------------------:|:----------:|
| symbol | unknown projectile | string     |
| energy | unknown energy     | string     |

and are only used to form the title in the output files. Both keys take a string as value.
#### Example
```bash
    [projectile]
    symbol      = U
    energy      = 1.4 MeV/u
```

If the key-value pairs are not provided in the input file, the default values shown above in the table are assigned to the key.

---

### [target] category
The [target] category has three key-value pairs, which are summarized in the table below :

| key         | default value           | value type                        |
|:-----------:|:-----------------------:|:---------------------------------:|
| symbol      | unknown target          | string                            |
| mass.number | required, no default    | floating point (double precision) |
| pressure    | unknown target pressure | string                            |

The symbol and pressure keys are only used to form the title in the output files. These two keys take a string as value. The mass.number key take a floating point number (double precision) and is used for the computation of the scaling factor of the input cross-section coefficients. Therefore the mass.number value is a required value that must be positive and provided by the user. Note that the atomic mass value can be given instead of the mass number for a proper scaling.

#### Example
```bash
    [target]
    symbol      = Ar
    mass.number = 40
    pressure    = 1 mbar
```

---

### [thickness] category
The [thickness] category has four key-value pairs, which are summarized in the table below :

| key          | default value           | value type                        |
|:------------:|:-----------------------:|:---------------------------------:|
| unit         | mug/cm2                 | string                            |
| minimum      | 1.e-3                   | floating point (double precision) |
| maximum      | 20.0                    | floating point (double precision) |
| point.number | 1000                    | integer                           |

The point.number key takes integer numbers, the maximum and minimum keys take floating point numbers, and are used for ploting the figures and printing the table. Because of the log scale of the thickness axis, the minimum must be greater than 0.0. If it is set to 0.0, the corresponding value will be reassigned to 1.e-10. The unit key take a string as value and is used for the computation of the scaling factor of the input cross-section coefficients.

#### Thickness units
The supported units of the penetration depth, required for the non-equilibrium solutions, are : "10 mg/cm2" or "xg/cm2" where x is a [SI prefix](https://en.wikipedia.org/wiki/Metric_prefix). The table below shows the list of supported units for the penetration depth (target thickness) with the corresponding scale factor of the cross-section coefficients :

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


#### Example
```bash
    unit         = cg/cm2
    maximum      = 30
    minimum      = 1e-4
    point.number = 200
```

---

### [fraction] category
The [fraction] category has three key-value pairs, which are summarized in the table below :

| key          | default value  | value type                        |
|:------------:|:--------------:|:---------------------------------:|
| minimum      | 0.0            | floating point (double precision) |
| maximum      | 1.1            | floating point (double precision) |
| epsilon      | 0.001          | floating point (double precision) |

The maximum and minimum keys take floating point numbers, and are only used for plotting the figures (i.e. setting up the ion fraction axis range). The epsilon key takes a floating point as value (double precision) and is used to compute the estimation of the distance to equilibrium for each ion fraction. 


#### Example
```bash
    [fraction]
    maximum     = 1.
    epsilon     = 0.001
```
Note that in this example the minimum key is not provided and will have a value of 0.0 by default.

#### Remark about the epsilon value

The algorithm used for the distance-to-equilibrium estimation is the following :

For each fraction, starting from xmax (=maximum key, defined in the [thickness] category) and going towards xmin, the distance-to-equilibrium is estimated if the following condition is satisfied:

<img src="https://github.com/FAIR-BREIT/BREIT-DOC/blob/master/figures/distance-to-equilibrium-condition.png" width="400">

If the condition is satisfied at x=xmax, a warning in the output results is printed telling that the estimated distance is found at the range limit. In addtion to the epsilon value, the estimation of the distance to equilibrium may be sensitive to

* thickness units (scale the x-axis range)
* thickness minimum and maximum values (x-axis range)
* thickness point.number (the algorithm go through steps of (xmax-xmin)/point.number)


The epsilon value should not be too large, otherwise the estimation will be erroneous. It should not be too small either, because the solution at equilibrium is asymptotic and is never reached (or reached at infinity). Therefore if the epsilon value is too small the estimated distance will be most likely at the range limit (xmax). If the epsilon value is reasonnable (on the order of 0.1% or 1%) and the estimated distance is found at xmax, then, most probably, the thickness units or thickness range should be modified accordingly. It is up to the user to find the proper setting for reliable distance-to-equilibrium estimations. 



---


### [cross.section] category
The [cross.section] category has one fixed key-value pair, namely the cross-sections unit, and variable key-value pairs depending on the dimension of the system to be solved.
The variable key-value pairs are the initial conditions (ion charge state in a vector form) and cross-section coefficients in a square matrix form. The index of the initial conditions and cross-section coefficients must satisfy the following condition : 0 < i,j < 200. The key-values pairs for the [cross.section] category are summarized in the table below:


| key          | meaning             | default value           | value type                        | comments                                                      |
|:------------:|:-------------------:|:-----------------------:|:---------------------------------:|:-------------------------------------------------------------:|
| unit         | cross-section units | required, no default    | string                            | see table below                                               |
|              |                     |                         |                                   |                                                               |
| F0.imin      | intial conditions   | 0.0                     | floating point (double precision) | imin is an integer (unsigned)                                 |
| F0.i         | intial conditions   | 0.0                     | floating point (double precision) |             imin < i < imax        |
| F0.imax      | intial conditions   | 0.0                     | floating point (double precision) | imax is an integer (unsigned).  the sum of F0.imin to F0.imax must be 1. Moreover imin and imax must correspond to the imin and imax of the cross-section coefficients (see below in this table)                                |
|              |                     |                         |                                   |                                                               |
| Q.imin.jmin  | cross-sections coef | 0.0                     | floating point (double precision) | imin, jmin are integers (unsigned)                |
| Q.i.j        | cross-sections coef | 0.0                     | floating point (double precision) | i and j are integers (unsigned). Moreover imin < i < imax and jmin < j < jmax                          |
| Q.imax.jmax  | cross-sections coef | 0.0                     | floating point (double precision) | imax, jmax are integers (unsigned). Moreover imax-imin must be equal to jmax-jmin to form a nonsingular square matrix i.e. no zero-rows or zero-columns should be provided          |

#### Cross-section units
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

#### Example

```bash
    [cross.section]
    
    ##################################
    # unit key-value
    unit        = cm2

    ##################################
    # initial condition key-value pairs
    F0.85 = 0.
    F0.86 = 1
    F0.87 = 0.
    F0.88 = 0.
    F0.89 = 0.
    F0.90 = 0.
    F0.91 = 0.
    F0.92 = 0.

    ##################################
    # cross-sections key-value pairs
    Q.85.86 = 7.0e-21
    Q.85.87 = 0.
    Q.85.88 = 0.

    Q.86.87 = 6.25e-21
    Q.86.88 = 0.
    Q.86.89 = 0.

    Q.87.88 = 5.4e-21
    Q.87.89 = 0.
    Q.87.90 = 0.

    Q.88.89 = 4.e-21
    Q.88.90 = 0.
    Q.88.91 = 0.

    Q.89.90 = 2.e-21
    Q.89.91 = 0.
    Q.89.92 = 0.

    Q.90.91 = 1.35e-22
    Q.90.92 = 0.

    Q.91.92 = 6.8e-23

    Q.86.85 = 2.e-21

    Q.87.86 = 2.2e-21
    Q.87.85 = 0.

    Q.88.87 = 2.35e-21
    Q.88.86 = 0.

    Q.89.88 = 2.4e-21
    Q.89.87 = 0.

    Q.90.89 = 2.45e-21
    Q.90.88 = 0.

    Q.91.90 = 2.5e-21
    Q.91.89 = 0.

    Q.92.91 = 2.55e-21
```


---

## 3-Output


Output equilibrium results are provided on demand in the form of :

* 1-electron approximation (txt file)
* no-approximation obtained from matrrix inversion (txt file)
* figures (pdf file)

Output non-equilibrium results are provided on demand in the form of :

* analytical formulae (txt file)
* table (txt file)
* figures (pdf file)






