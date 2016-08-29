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

During the run phase, the working directory of the execution environment is the *run-hash*/*iteration-number* directory of the latest run ( 'runs/*datetime*' with an alias of 'runs/latest' for convenience), where *run-hash* is the hash of the run configuration, and *iteration-number* is the index of the iteration that is run. An execution environment may create as many temporary files in that directory as necessary and leave execution logs for later manual revision.

Although none of the current Wu-Wei tools use the information that may be produced as side-effect of an execution, it may still be useful. If you develop custom tools that leverage it, let us know so we can publicize them.

## Conventions

An environment artifact is composed of at least two files, an execution script called 'run' and a description file called 'environment.json'. All the environments share the same commandline interface for 'run' and the same basic conventions to describe the artifact in the 'environment.json' description file. Both are described in turn.

### Commandline interface for 'run'

The commandline interface for the environment 'run' script/executable takes a single file as executable as first argument and an unlimited number of positional arguments that are passed to the executable artifact of the build. The executable artifact might be a native executable (ex: a compiled C program), a bundle of executable files (ex: a Emscripten or Browserify JavaScript bundle of source code), or the entry point of the program (ex: A MATLAB runner script) that refers to other files in the build directory.

When the executable artifact is a native executable, the 'run' script simply calls the executable with the arguments. This is what the [native](https://github.com/Sable/ostrich-native-environment) environment does for x86 compiled code.

When the executable artifact is a bundle of source code or an entry point, the 'run' script first need to convert the commandline parameters to types that are native to the language. This is what the [node environment](https://github.com/Sable/ostrich-node-environment) does. It then calls the entry point function, typically called 'runner', with the converted arguments.

Although there is no example of environments that do it at the time of writing these lines, an environment may measure performance metrics (ex: memory or energy usage, cache misses, etc) and augment the output of the executable artifact, which is a JSON object, with these additional measurements. These measurement can then be used by a report tool for producing figures and diagnostics.

### Description file conventions

| Property Name         | Expected Values  | Description                                                               |
| :-------------------- | :--------------- | :------------------------------------------------------------------------ |
| "type"                | "environment"    | Specifies that the description schema is that of an environment.          |
| "short-name"          | string           | Unique name that identifies the environment for the commandline interface.|
| "version"             | string           | Version of the environment. Should refer to the version of the wrapped artifact in case the environment wraps another project. |
| "supported-languages" | list of strings  | List of the [canonical names](README.md#canonical-names-for-languages) of languages that the environment supports. This is used for automatic matching of compatible artifacts (between implementations, compilers, and environments). |

The following example, taken from the [Native environment wrapper](https://github.com/Sable/ostrich-native-environment) shows possible values for the different properties:


    {
    	"type": "environment",
    	"short-name": "native",
    	"version": "1.0.0",
    	"supported-languages": ["x86"]
    }   


### Controlling environment-specific parameters with description properties

Some environments may have parameters that influence their performance. One example is the activation/deactivation of a Just-In-Time compiler in an interpreter, such as the MATLAB interpreter. Exposing these parameters as properties on the 'environment.json' description file (which may be read by the 'run' script to control the behaviour of the actual artifact),  can be both convenient and provide automatic traceability of the experiment parameters because they are now part of the configuration.

This is done in the [MATLAB environment wrapper](https://github.com/Sable/ostrich-matlab-environment). The description file includes a 'matlab-jit' option that can be set to true or false depending on whether the jit should be activated or not. The 'run' script then uses it to execute the undocumented 'featureaccel on/off' command before executing the benchmark implementation.


## Attribution and Licensing

The use of the resulting work may be governed by a license. In order to recognize the effort that was contributed by various authors and honor the current licensing, the implementation should have a few additional files that otherwise are not strictly required for performing experiments:

- The origin of the benchmark should be mentioned in the README file for the implementation
- The different authors of the implementation should also be mentioned in the AUTHORS file, including the original authors of the implementation if obtained from an existing source
- If the implementation has been imported from an existing source, the original license of the implementation should be included. Otherwise, a new license that explains how the implementation can be used, modified, and distributed should be included. In both cases, the license text should be in the LICENSE file.
