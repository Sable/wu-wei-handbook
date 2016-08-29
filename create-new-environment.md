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

For environments that wrap other projects, we suggest the environment is created in a different repository and the original project is listed as a dependency in the 'environment.json' description file to be installed it in a specific directory. A similar structure has been used for the [AspectMatlab compiler](https://github.com/Sable/aspect-matlab-compiler).

## Execution context

During the run phase, the working directory of the execution environment is the *run-hash*/*iteration-number* directory of the latest run ('runs/latest' that will later become 'runs/*datetime*'), where *run-hash* is the hash of the run configuration, and *iteration-number* is the index of the iteration that is run. An execution environment may create as many temporary files in that directory as necessary and leave execution logs for later manual revision.

Although none of the current Wu-Wei tools use the information that may be produced as side-effect of an execution, it may still be useful. If you develop custom tools that leverage it, let us know so we can publicize them.

## Conventions

An environment artifact is composed of at least two files, an execution script called 'run' and a description file called 'environment.json'.

### Commandline interface 


### Description file conventions

    {
    	"type": "environment",
    	"short-name": "native",
    	"name": "native",
    	"version": "",
    	"supported-languages": ["x86"],
    	"supported-formats": ["binary"]
    }


### Controlling environment-specific parameters with description properties

## Attribution and Licensing

The use of the resulting work may be governed by a license. In order to recognize the effort that was contributed by various authors and honor the current licensing, the implementation should have a few additional files that otherwise are not strictly required for performing experiments:

- The origin of the benchmark should be mentioned in the README file for the implementation
- The different authors of the implementation should also be mentioned in the AUTHORS file, including the original authors of the implementation if obtained from an existing source
- If the implementation has been imported from an existing source, the original license of the implementation should be included. Otherwise, a new license that explains how the implementation can be used, modified, and distributed should be included. In both cases, the license text should be in the LICENSE file.
