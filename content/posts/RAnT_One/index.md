---
title: "RAnTing about my new Project"
date: 2024-05-31T19:49:33+02:00
draft: true
toc: false
images:
tags:
    - nonlinear
    - Rust
---

I haven't made a post in a minute.
So I thought I should write about the project that I just started.
It is called "RAnT", and of course it is written in Rust.

# Motivation

In my [Masters Thesis](https://github.com/cloudsftp/Masterarbeit/releases/tag/v1.1.2), I was working in the field of non-linear dynamics.
I had simulate the iteration of functions, as this is what is usually done in that field.
For this I used an analysis tool, [AnT](https://github.com/cloudsftp/AnT).

This tool was written years ago in C++ by my supervising Professor among other people working at the University, I was studying at the time.
I have huge respect for him, but the user experience was not very good.
Writing my own analysis tools would have not been an option though, as I had to write my Masters Thesis.

In the following examples, I will demonstrate the pain points I experienced.

## Simulating the Periods of the Logistic Function

The logistic function is famous in the field of non-linear dynamics and very simple, therefore I will use it in this example.
It is defined as $f(x) = a x (1 - x)$ where $x$ is the state and $a$ the only parameter.
We are interested in how the function affects the state based on the parameter.

Does it not change the state at all $[f(x) = x]$?
This would be called a fixed point.
Does it return to the same state after a number of iterations $[f(f(...(f(x))...)) = x]$?
This would be called a cycle.
Does it tend toward infinity, or something else entirely?

### Function Implementation

First, we need to define the function in a way, AnT can simulate it.
For this, we need to compile a C++ function with the signature

```Cpp
bool <function_name>(
    const Array<real_t>& currentState,
    const Array<real_t>& parameters,
    Array<real_t>& RHS
)
```

to an object file.

For our logistic function, we create a C++ file called `logistic.cpp` with the following content.

```Cpp
#include "AnT.hpp"

#define a parameters[0]

#define x currentState[0]
#define y RHS[0]

bool logistic(
    const Array<real_t>& currentState,
    const Array<real_t>& parameters,
    Array<real_t>& RHS
) {
    y = a * x * (1 - x);
    return true;
}

extern "C"
{
    void connectSystem() {
        MapProxy::systemFunction = logistic;
    }
}
```

As you can see, I defined macros for the state, $x$, the parameter, $a$, and the output of the function, $y$, to make the code more readable.
We also need to import the library `AnT.hpp` and export our function as the `systemFunction`.

We then can compile the file with a bash-script that is part of AnT by executing the following command in the directory, our function implementation source file is in.

```bash
/path/to/AnT/bin/build-AnT-system.sh
```

### Simulation configuration

Next, we need to tell AnT, how to simulate the function.
Let's say we want to know the periods of the cycles at different values for the parameter $a$.
For this, we create a new file called `periods.ant` with the following content.

```text
dynamical_system = {
    type = map,
    name = "logistic map",
    parameter_space_dimension = 1,
    parameters = {
        parameter[0] = {
            value = 1.25,
            name = "a"
        }
    },
    state_space_dimension = 1,
    initial_state = (0.5),
    reset_initial_states_from_orbit = false,
    number_of_iterations = 20000,
    s[0] = {
        name = "",
        equation_of_motion = "0"
    }
},
scan = {
    type = nested_items,
    mode = 1,
    item[0] = {
        type = real_linear,
        points = 3000,
        min = 0,
        max = 4,
        object = "a"
    }
},
investigation_methods = {
    period_analysis = {
        is_active = true,
        max_period = 128,
        compare_precision = 1e-09,
        period = true,
        period_file = "period.tna"
    }
}
```

As you might recognize, or rather not recognize, this is not a standardized configuration language such as `yaml` or `json`.
Rather, it is its own configuration language.
It has a lot more options that are not shown here for brevity.
This configuration language can be a mouthful, especially if many of the unused options are listed ([example](https://github.com/cloudsftp/Masterarbeit/blob/latest/Simulation/Models/00_Examples/02_Logistic/bifurcation.ant)).

Here are the most important things, the example configuration file specifies.
1. The dimension of the parameter space to be one and
1. The only parameter to be $a$ with a value of $1.25$ (this value is overwritten later).
1. The dimension of the state space to be one and
1. The initial state to be $0.5$.
1. The maximum number of iterations to simulate the function to be $20,000$.
1. Scan the function for $3,000$ points of the parameter $a$ in the range $[0, 4]$.
1. Analyze the period of the function with a maximum period of $128$ and a precision of $1 \cdot 10^{-9}$ and write the result to a file named `period.tna`.

### Executing the simulation

Executing the simulation is not that bad actually.
We just have to call the compiled AnT binary and set the system function object file and the config file per command line options.
Running AnT without arguments will diplay the usage instructions.

```text
--//------------/-------------------------------
 // AnT 4.669  / Release 4c, (c) 1999-2011
//------------/---------------------------------

usage: ../../../AnT/bin/AnT <systemname> [{-i | -I | --initialization} <configfile>] [{-m | -M | --mode} <runmode>] [{-s | -S | --server} <server name>] [{-p | -P | --port} <portnumber>] [{-n | -N | --points} <scanpoints>] [{-t | -T | --time} <seconds>] [{-v | -V | --version}] [{-v | -V | --log}] [{-h | -H | --help}]

<systemname>
    complete path and filename (without extension)
    of the shared library containing at least the system
    function for the dynamical system to be simulated.

Options:
{-i | -I | --initialization} <initialization file>
    complete path and filename of the initialization file
{-m | -M | --mode} <runmode>
    where runmode is one of 'standalone',
    'server' or 'client'. Default is 'standalone'.
{-s | -S | --server} <server name>
    for runmodes 'server' and 'client' only.
    Default is the standard hostname of the current system.
{-p | -P | --port} <portnumber>
    for runmodes 'server' and 'client' only.
    The default port is 54321.
{-n | -N | --points} <scanpoints>
    for runmode 'client' only.
    The number of scanpoints the client
    should fetch from the server. Default is 50.
{-t | -T | --time} <seconds>
    for runmode 'client' only. The (approximate) number
    of seconds the client should be busy before asking
    for new scan points from the server.
    This option overrides the '-n' option.
{-v | -V | --version}
{-l | -L | --log} write the log-file 'transitions.log'
    which shows the internal structure of the current
    simulator instantiation.
{-h | -H | --help}

Error::Exit: abnormal program termination!
```

So following these instructions, we execute the following command.
```bash
/path/to/AnT/bin/AnT logistic.so -i periods.ant
```

### Output and figures

Once the simulation completed, we can check the output.

```text
> head period.tna
0.000000000000000e+00  1
1.333777925975325e-03  1
2.667555851950650e-03  1
4.001333777925975e-03  1
5.335111703901300e-03  1
6.668889629876626e-03  1
8.002667555851949e-03  1
9.336445481827275e-03  1
1.067022340780260e-02  1
1.200400133377793e-02  1
```

The output consists of all the scan points in one line each.
On the left, the value of the parameter $a$ and on the right the period of the system function at that point.
A period of $1$ means that a fixed point exists for that value of $a$.

In my Master Thesis, I used `gnuplot` to generate images from this data.
This technique was taught to my by my supervising professor.
As well as using `fragmaster`, `pdfcrop`, and `convert` to create better quality images from the `gnuplot` images.

## Parallelization

For scans with a lot more scan points and more computationally expensive system functions, this process might take a very long time.
If we want to speed things up and run scans in parallel, we have to manually start different instances of AnT, that run in server mode and client mode respectively.
They need to operate on the same function implementation object file and configuration file.

For example, we can start the server process with the following command.

```bash
/path/to/AnT/bin/AnT logistic.so -i periods.ant -m server -s "0.0.0.0" -p 6660 &
```

And then start one client process with

```bash
/path/to/AnT/bin/AnT logistic.so -i periods.ant -m client -s localhost -p 6660
```

Using `0.0.0.0` for the server process makes sense, since we want to bind to it.
But for some reason, I can't use it for the client process and must instead use `localhost`.
This might vary for your environment.

Each client process computes points of the scan on one thread.
So if you want to use 8 threads of your CPU for the simulation, you have to start 8 client processes.

## Wrapper script

As you can see, running scans in parallel is a highly repetetive manual process.
Of course this can be automated alongside the generation of images.
For this, I wrote a wrapper script called `simulAnT.py` during my Masters Thesis.
("Simulant" is german for malingerer and loosely translates to "the one who simulates".
I think the name is especially funny, because it reminds me of [this video](https://www.youtube.com/watch?v=-ssGusoccGw) that went viral during my school days.)

The source code of the script is available [here](https://github.com/cloudsftp/Masterarbeit/tree/latest/Simulation).
If we take a look at the usage options, we see that it allows to set the number of client processes to use for the simulation, and also to only render a simple image among other options.
This option skips the step that makes the resulting image better looking, but takes a lot of time for images with a lot of dots like plotting periods.

```text
usage: simulAnT.py [-h] -m MODEL -d DIAGRAM [-n NUM_CORES]
                   [--simple-figure | --no-simple-figure]
                   [--skip-computation | --no-skip-computation]
                   [--dont-show | --no-dont-show]

options:
  -h, --help            show this help message and exit
  -m MODEL, --model MODEL
  -d DIAGRAM, --diagram DIAGRAM
  -n NUM_CORES, --num-cores NUM_CORES
  --simple-figure, --no-simple-figure
  --skip-computation, --no-skip-computation
  --dont-show, --no-dont-show
```

Unfortunately, this script is very convoluted and still does not provide a good user experience when creating new models or scans of models.

# RAnT

Now you know, what the purpose of my new project is and what pain points I am trying to avoid.
Rather than a standalone program, RAnT is a library for developing programs that simulate and analyze system functions.

My ultimate goal is to allow the user to explore scans of state spaces interactively.
But for this, I have a long way to go.

## Current State

Currently, the library supports only the analysis of periods and after how many iterations a condition is met.
Also it only supports maps, as where AnT supports many different system function types such as ordinary differential equations and partial differential equations.

But it is built with extensibility in mind.
At least different analysis methods can be implemented without any problems as a user of the library.

Thanks to [rayon](https://github.com/rayon-rs/rayon), the library supports parallelization out of the box.
Unfortunately, the library is not designed to distribute the computations between multiple nodes, as was possible with AnT.
Although it seems to not work anymore, at least on Linux machines.

### Logistic Example

