# ASTERISM

This is the Aggie Simulation Tool Exhibiting Research on 
Importance Sampling in Markov Models, developed by Utah 
State University. 

Copyright: Chris Winstead et al. 2019-- 
Lincense: GPL2

## Contributors:

Supervisor: Chris Winstead, Associate Professessor, Electrical and
Computer Engineering, Utah State University.

Developers and Research Participants:

* Zinnia Mowri
* Tom Prouty
* Andrew Gerber
* Eric Reiss
* Daniel Crane


# Build Instructions

Tested on Ubuntu 23, Ubuntu 20 *WSL*, Centos 7, and Rocky 8.

## Dependencies:

- Install the following packages (package names for Debian/Ubuntu Based):
    - openjdk-17-jdk *(or 13; 17 preferred)*
    - build-essential
        - gcc
        - g++
        - git
        - make
- Clone this repository and run `./bin/initPrism.sh` to install Prism from source

<br>

## Build:

1. Run `./gradlew build`


### Troubleshooting

- Run `./gradlew -v`. Gradle should be 7, JVM should be 13-17
- Java version not available?
    - Debian/Ubuntu Based: run `apt update`
    - Arch-based: `pacman -Syuu`
- Error `Cannot find symbol ... Prism* ...` or `package parser/prism does not exist`
    - Prism is not installed/compiled correctly
    - Run `./bin/initPrism.sh`

<br>

# Usage:

Algorithms are in the `imsam` package (short for "Importance
Sampling") and are implemented in the class called `ExtendedWSSA`. All
stochastic simulation methods are accessed via this command:

`./bin/run.sh simulate [OPTION]...` 

The particular choice of algorithm and its parameters are determined
by command-line arguments and by defining special constants/labels in
the PRISM model file. 


## Common Arguments

 | Usage:        | `./bin/run.sh <command> [OPTION]...`           |
 |---------------|------------------------------------------------|
 | `-v`          | Verbose logging (Info level)                   |
 | `-vv`         | Verbose logging (Debug level)                  |
 | `-vvv`        | Verbose logging (Trace level)                  |
 | `--quiet`     | Suppress logging to console, except for errors |
 | `--help (-h)` | Show usage information                         |
 | `--raw`       | Print raw output values (default: false)       |
 |               |                                                |

## Stochastic Simulation

Arguments for stochastic simulation apply to all other simulation methods.

```
 --Nruns N            : Number of stochastic runs 
 --Tmax N             : Maximum transitions before truncating 
 --const VAL          : Model constant name=value 
 --model FILENAME     : Prism model file name 
```

## Modulo Heuristic

The modulo heuristic provides accelerated simulation to estimate path
completion time. This is helpful in CTMC models where a path can
satisfy a temporal property with some probability (possibly a very
small probability). Whereas SSA generates a 1/0 result for the
temporal property, the modulo method returns a probability between 0
and 1. 

Arguments relevant to the modulo heuristic are as follows:

```
 --modulo             : Use 'modulo' heuristic (default: false)
 --gamma N            : Stretch parameter for 'modulo' heuristic. (default: 100.0)
 --numModuloSamples N : Number of samples to compute 'modulo' heuristic
                        (default: 1000)
 --rho N              : Stopping time parameter for 'modulo' heuristic.
                        (default: 100.0)
```


## Weighted Stochastic Simulation (wSSA)

In the domain of Chemical Reaction Networks (CRN), a wSSA simulation
can be configured by setting rate and propensity constants in the
PRISM model file.

* `k1`, `k2`, ...  define the rate constants
* `delta1`, `delta2`, ... define the associated propensities

Some examples are provided in the `models/` subdirectory. To pick one
example, `three_rxn_wssa.pm` includes the lines below which set
reaction rates and propensities: 

```
const double k1=1.0;
const double k2=0.2;
const double k3=0.05;

const double delta1=1.0;
const double delta2=5.0;
const double delta3=50.0;
```


## Constrained Sampling Heuristic

Simulated paths can be constrained by defining a `constraint` label in
the PRISM model file. An example of this is in the file
`models/three_rxn/three_rxn_constrained.pm` which contains these lines:

```
const int maxConstraint=3;

label "constraint" = a+c<maxConstraint & b+c<maxConstraint;
```

The `constraint` label is mandatory for any paths followed by the
simulator. If the `constraint` is violated in some state during
simulation, then transitions entering that state are removed from the
model. This is useful for reducing the state space of a Markov model.




