# Create a new execution environment

A (Wu-Wei) execution environment takes a build, executes it during the run phase of the benchmarking cycle, and produces a run, which is a combination of performance metrics obtained from one or multiple executions and the configuration(s) that were used to produce the results. In the more complex setups, the environment may potentially execute multiple versions of the same build to obtain performance metrics. These additional metrics may supplement the base execution timing performed by the implementations. Artifact that are good candidates to be wrapped as environments are those that:

- perform dynamic translation of a programming languages to lower-level languages during the execution of the program. These are traditionally called Just-In-Time compilers;
- perform interpretation of a programming language. These are traditionally called interpreters;
- perform iterative refinement of an executable by gathering execution data, then modifying the source code or the executable, and then running the (hopefully improved version of the) executable again.

Note that the compiler and/or interpreter may be realized in hardware in which case the language that is compiled or interpreted is the processor architecture (ex: x86 for intel-compatible processors). We take the view that hardware is simply software with a direct material realization.

Please refer to the [list of available environments](list-available-artifacts.md#environments) as examples before creating a new one. In the case none of those environments suit your needs, we suggest you:

- fork one of the existing environments;
- create a backward-compatible modification;
- do a a pull request on the repository of the existing compiler.

This way the modification will benefit current users of that artifact and it will avoid fragmentation of the compatible artifacts. If no available compiler may suit your needs please create a new one from scratch and do a pull request to add it in the [list](list-available-artifacts.md#environments).

## Conventions

## Execution context

## Controlling parameters with description properties
