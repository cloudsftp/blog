---
title: "RAnT One"
date: 2024-05-31T19:49:33+02:00
draft: true
toc: true
images:
tags:
    - nonlinear
    - Rust
---

I haven't made a post in a minute.
So I thought I should write about the project that I just started.

# Motivation

In my [Masters Thesis](https://github.com/cloudsftp/Masterarbeit/releases/tag/v1.1.2), I was working in the field of non-linear dynamics.
I had simulate the iteration of functions, as this is what is usually done in that field.
For this I used an analysis tool, [AnT](https://github.com/cloudsftp/AnT).

This tool was written years ago in C++ by my mentoring Professor among other people working at the University, I was studying at the time.
I have huge respect for him, but the user experience was not very good.
Writing my own analysis tools would have not been an option though, as I had to write my Masters Thesis.

In the following examples, I will demonstrate the pain points I experienced.

## Example: Simulating the logistic function on a single thread

The logistic function is famous in the field of non-linear dynamics and very simple, therefore I will use it in this example.
It is defined as $f(x) = a x (1 - x)$ where $x$ is the state and $a$ the only parameter.
We are interested in how the function changes the state based on the parameter.

Does it not change the state at all $[f(x) = x]$?
This would be called a fixed point.
Does it return to the same state after a few iterations $[f(f(...(f(x))...)) = x]$?
This would be called a cycle.
Or does it tend to infinity?

### Function implementation

First, we need to define the function in a way, AnT can simulate it.
For this, we need to compile a C++ function with the signature

```C++
bool <function_name>(
    const Array<real_t>& currentState,
    const Array<real_t>& parameters,
    Array<real_t>& RHS
)
```

to an object file.

For our logistic function, we create a C++ file called \`logistic.cpp\` with the following content.

```C++
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

As you can see, I defined macros for the state, x, the parameter, a, and the output of the function, y, to make the code more readable.
We also need to import the library `AnT.hpp` and export our function as the `systemFunction`.

We then can compile the file with a bash-script that is part of Ant.

```bash
TODO
```

### Simulation configuration

Next, we need to tell Ant, how to simulate the function.
Let&rsquo;s say we want to know the periods of the cycles at different values for the parameter $a$.
For this, we create a new file called \`periods.ant\` with the following content.

```json
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
    general_trajectory_evaluations = {
    is_active = false,
    saving = {
    },
    min_max_values = {
    },
    wave_numbers = {
    },
    statistics = {
    },
    pgm_output = {
    }
    },
    period_analysis = {
    is_active = true,
    max_period = 128,
    compare_precision = 1e-09,
    period = true,
    period_file = "period.tna",
    cyclic_asymptotic_set = false,
    cyclic_bif_dia_file = "bif_cyclic.tna",
    acyclic_last_states = false,
    acyclic_bif_dia_file = "bif_acyclic.tna",
    cyclic_graphical_iteration = false,
    cyclic_graph_iter_file = "cyclic_cobweb.tna",
    acyclic_graphical_iteration = false,
    acyclic_graph_iter_file = "acyclic_cobweb.tna",
    using_last_points = 1528,
    period_selections = false,
    periods_to_select = (),
    period_selection_file = "period_selection",
    period_selection_file_extension = "tna"
    },
    band_counter = {
    },
    symbolic_analysis = {
    },
    rim_analysis = {
    },
    symbolic_image_analysis = {
    },
    lyapunov_exponents_analysis = {
    },
    dimensions_analysis = {
    },
    check_for_conditions = {
    }
}
```

TODO explain

-   custom config language
-   mouthful
-   hard to read

### Executing the simulateion

not that bad actually

### Output and figures

-   example output text
-   example gnuplot program
-   example image

## Example parallel usage

-   executing commands

## Wrapper script

-   built-in parallelization
-   built-in image generation

# Ultimate goal

My ultimate goal with this project is to build a library that makes it trivial to write programs that allow the user to explore the parameter space interactively.


# State

-   Nice core with parallelization built-in
    -   Effortless parallelization thanks to rayon
-   only discrete right now
    -   core designed to be extensible

