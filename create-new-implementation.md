Creating high-quality implementations for benchmarks is the most time-consuming part of benchmarking and arguably the most important since it the foundation in the evaluation of many other tasks, such a creating new optimizations in compilers or virtual machines, source-code analyzes, comparing various implementations of a programming language, etc. There are many factors that need to be accounted for to make the resulting implementation useful, correct, convenient, and reusable by others. The payoff for a community is the same as for having high-quality publications or libraries readily available: we can stand on other people's shoulders.

In this guide, we introduce the important points for writing high-quality implementations from 3 starting points, from simplest to the most complicated:

1. An existing Wu-Wei implementation in the same language
2. An existing Wu-Wei implementation in a different language (part of an existing benchmark)
3. No existing Wu-Wei implementation

The various considerations are progressively introduced since the later tasks subsume the previous ones by requiring additional considerations.

# 1. Create a new implementation from an existing Wu-Wei implementation in the same language

This is the simplest case. It may be used to try different optimizations techniques by hand to investigate their performance tradeoffs before the creation of a fully automatic transformation in a compiler or execution environment (ex: loop unrolling strategies, vectorization strategies, optimization techniques from optimization guides for various architectures, different object/matrix representations in memory, etc.). It may also be used to try different existing libraries that implement some part of the core computation of a benchmark (ex: various JavaScript libraries from npm that perform numerical computations). You can see an example [here](https://github.com/Sable/lcpc16-analysis/tree/master/benchmarks/backprop/implementations), in which multiple versions of an implementation were compared for the performance evaluation in a publication about automatic vectorization techniques for MATLAB.

There are two manual steps to do, (1) copy and rename the existing implementation directory and (2) change the copy's short-name in the implementation.json file to the same value as the copy directory name. For example, suppose you have an existing c implementation for the template benchmark. Starting from the repository root you would do:

    cd benchmarks/template/implementations
    cp -r c c-modified
    cd c-modified
    open implementation.json # Modify the 'short-name' property to 'c-modified'

The files that perform the core computation (the files under the implementation.json 'core-source-files' by convention) can then be modified to obtain a different variation. A new copy of the implementation may be created for each additional interesting variation that is tried.

The original implementation may need to be modified also. In order to keep the different versions consistent with less effort, the copies of the non-core files can be replaced with either hard- or soft-links to the files of the original implementation.

The main advantage of this approach is that all the rest of the Wu-Wei infrastructure and benchmark implementation need not be modified to compare the performance metrics of the variation(s) against the base version. The presence of a new (valid) implementation directory is sufficient for the tools to use it automatically for all the other phases of the benchmark cycle.
    
# 2. Create a new implementation from an existing Wu-Wei implementation (and benchmark) in a different language

Creating a new implementation in a different language for an existing benchmark requires thinking about a few issues in order to make the comparison between the different implementations correct and the new implementation convenient to reuse. To make the task easier, we suggest using existing templates that correctly address those issues as starting points and replace their code with custom code written from scratch or ported from existing benchmark suites.

Every implementation should be self-contained and be stored under the 'benchmarks/*benchmark-name*/implementations/*implementation-name*' directory. The *benchmark-name* and *implementation-name* should correspond to the short-names of each artifact and are also respectively properties of the 'benchmark.json' and 'implementation.json' description files. A new implementation can be bootstrapped using an existing template with the following command from the repository root:

    wu install *template-source* --destination benchmarks/*benchmark-name*/implementations/*implementation-name*

The install tool will automatically update the implementation's 'implementation.json' description file's short-name to *implementation-name* and save the template files under the 'benchmarks/*benchmark-name*/implementations/*implementation-name*' directory to ensure consistency between short-name and the directory. 

Here is a list of currently available templates for various language implementations:

| Language              | Description                               | Source                            |
| :-------------------- | :---------------------------------------- | :-------------------------------- |
| matlab                | MATLAB template that relies on the Ostrich pseudo-random number generator function compiled from C with MEX, itself part of the [ostrich-matlab-environment](https://github.com/Sable/ostrich-matlab-environment)  | https://github.com/Sable/matlab-implementation-template.git |

If you create a new template for another language, please do a pull request on this handbook with the source (url) of your template to add it to this list. We recommend using a git repository for traceability of the changes but any url may work.

The templates are implemented to show how to make the implementation both correct and convenient to reuse by addressing the following issues. We will use the matlab template example for illustration. You may follow the examples by installing the template in a new repository by doing:

    mkdir implementation-tutorial && cd implementation-tutorial
    wu init
    wu install https://github.com/Sable/benchmark-template.git
    mkdir -p benchmarks/template/implementations # Ensure there is an implementations folder present
    wu install https://github.com/Sable/matlab-implementation-template.git --destination benchmarks/template/implementations/matlab
    
You may open the 'benchmarks/template/implementations/matlab/implementation.json' file in order to follow along. When you do, in the first part of the file you will find the following properties:

        {
            "type": "implementation",
            "short-name":"matlab",
            "description":"Template for matlab implementations",
            "language":"matlab"
            ...
        }

The 'type' property specificies that the rest of the description is for an implementation, the 'short-name' is a unique name used with the various tools to refer to this specific implementation, the 'description' is to provide general explanations about the source of the benchmark what is interesting about this specific implementation. The 'language' property is a short canonical name that specifies the programming language in which the implementation was written. The other properties in the file are discussed as we cover the various topics.

## Highlighting the interesting part of the implementation sources

All benchmarks need to perform setup and teardown steps around the interesting part of the computation in order to produce a repeatable correct result and some metric measurements that will be communicated to the rest of the benchmarking infrastructure. It is convenient in some studies (code instrumentation, run-time profiling, etc.) to differenciate between the interesting and non-interesting part of the computation. 

An implementation can highlight the interesting parts of the computation by listing the interesting files under the 'core-source-files' property of the 'implementation.json' description file. In the matlab template, the 'implementation.json' file list the following files:

        {
            "type": "implementation",
            "short-name":"matlab",
            ...
            "core-source-files": [
               { "file": "./kernel.m"}
            ],
            "runner-source-file": { "file": "./runner.m" },
            "libraries": [
                { "file": "./verify.m" }
            ]
            ...
        }

The uninteresting but necessary code for interfacing the interesting part of the computation with the rest of the infrastructure is in the 'runner-source-file'. Some part of the runner file may be put in separate file(s) under the 'libraries' property for clarity or because it is reused from files common to multiple benchmarks (more on that later when we address the dependencies). 

Note that all file paths are wrapped in a JSON object with a 'file' property. This is a convention used to disambiguate the usage of literal strings for different purposes in the description files. Moreover, the file paths are relative to the parent directory of the description file, the 'implementation.json' parent directory ('benchmarks/template/implementations/matlab/') in this example.

## Passing parameters to an implementation

An implementation needs to specify whether and how it uses the parameters that may vary between different experiments but are common to all implementations of the same benchmark. The first and most common case is the input size used, which by convention may be one of 'small', 'medium, 'large' values for which all benchmarks should provide concrete values.

For any benchmark, the 'small' value means the implementation should execute quickly but we do not really bother about the time spent in the computation. This is used to quickly test the correctness of the implementation (after various automatic transformations during the build phase). The 'medium' value means the implementation should execute long enough for performance measurements to stabilize but still quickly enough to deliver timely results, typically 1-10 seconds. The 'large' value means the input should be scaled up to see how the performance varies with a bigger load.

Since the concrete values are common between different implementations, they are specificed in the 'benchmark.json' description file. In our example, ('benchmarks/template/benchmark.json') you will find the following values:

        {
            "type": "benchmark",
            "short-name":"template",
            ...
            "input-size": {
                "small": 10,
                "medium": 3000,
                "large": 7000
            }
        }

### Using an expand macro for the input-size parameter

When creating a new implementation, to make it consistent with the other existing implementations of the same benchmark, you will simply refer to the same input size value with an 'expand' macro. The expand macro will eventually resolve to the benchmark concrete input value(s) in the implementation's configuration. The path of the expand macro refers to an element of the [configuration](https://github.com/Sable/wu-wei-handbook/blob/master/README.md#configuration-elements) that is used in the run phase. In this example, the '/experiment/input-size' is used as an argument to the implementation's runner and may take the 'small, 'medium', or 'large' values: 

        {
            "type": "implementation",
            "short-name":"matlab",
            ...
            "runner-source-file": { "file": "./runner.m" },
            "runner-arguments": [
                { "expand": "/experiment/input-size" }
            ],
            ...
        }

The Wu-Wei tools will resolve one of the 'small', 'medium', or 'large values to its concrete value that comes from the 'benchmark.json' description file in the configuration of the current phase of the benchmarking cycle.

### Parameterizing an implementation

An experiment may modify the behaviour of an implementation through build-time parameters, which change the operations performed by a compiler during the build phase to produce a runner from the implementation source code, and/or through run-time parameters, which modify the behaviour of the runner that is executed during the run phase.

For most dynamic languages, the implementation parameters are passed at run time to the runner file. For static languages, the implementation of some benchmarks may require parameter(s) at build time. We cover both separately.

#### Run-time parameters

To support run-time parameters, a single-step is required. The 'implementation.json' description file simply needs to define the experiment parameters that should be passed as arguments to the runner with the 'runner-arguments' property. This is the case for the experiment size in the matlab template:

        {
            "type": "implementation",
            "short-name":"matlab",
            "description":"Template for matlab implementations",
            ...
            "runner-source-file": { "file": "./runner.m" },
            "runner-arguments": [
                { "expand": "/experiment/input-size" }
            ],
            ...
        }

The exact mechanism by which the runner arguments are converted to concrete values and passed to an implementation are specific to the execution environment that is used. In most cases, if the implementation language provides facilities for obtaining commandline arguments, the runner will receive the arguments in the same order as defined in the runner-arguments. Otherwise, in most other cases the arguments will be passed as positional arguments to a 'run' or 'runner' function. In both cases, JSON numbers are usually converted to ints/floats and JSON string as strings. For the complete details you should refer to the document of the environment your are using.


#### Build-time parameters


To support build-time parameters two steps are required:

1. The compiler needs to expect a parameter from the implementation (which may be optional)
2. The implementation needs to define the value(s) of that parameter from an experiment's property

This is the case in the [PolyBench/C correlation benchmark's c implementation](https://github.com/Sable/polybench-correlation-benchmark) when used with the [ostrich-gcc-compiler](https://github.com/Sable/ostrich-gcc-compiler).

In this example, the compiler expects an optional 'compilation-flags' parameter from the implementation, which may contain multiple flags. These flags will be passed as arguments to the 'gcc' executable:

        {
            "type": "compiler",
            "short-name":"gcc",
            ...
            "commands": [
                {   "executable-name": "gcc",
                    "options":[
        	            ...
                        { "config": "/implementation/compilation-flags", "optional": true },
                        ...
                    ]
                }
            ]
        }


The PolyBench/C correlation implementation defines a 'compilation-flags' property that prepends the '-D' prefix to the value of the 'input-size' parameter of the experiment, itself resolve to a concrete value defined in the 'benchmark.json' description file.

        {
            "type": "implementation",
            "short-name":"c",
            "description":"Reference C implementation ported from PolyBench/C",
            "language":"c",
            ...
            "compilation-flags": [
               { 
                 "prefix": "-D",
                 "value": { "expand": "/experiment/input-size" }
               },
               ...
            ],
            ...
        }


### Multiple arguments

Sometimes, more than one parameter influence the behaviour of an implementation. In this case, a combination of these parameters needs to be defined for each of the benchmark 'small', 'medium', and 'large' input sizes. Each individual parameter can be defined as a property on an object:

    {
        "type": "benchmark",
        ...
        "input-size": {
            "small": {
                "parameter1": 1,
                "parameter2": 2,
            },
            "medium": {
                "parameter1": 5,
                "parameter2": 10
            },
            "large": {
                "parameter1": 100,
                "parameter2": 500
            }
        }
    }

An implementation can then refers to these parameters with an additional 'path' property with the 'expand' macro. The 'path' property uses the [json-pointer format](TODO-PUT-REFERENCE):

    {
        "type": "implementation",
        ...
        "runner-arguments": [
            { 
                "expand": "/experiment/input-size",
                "path": "/parameter1"
            },
            {             
                "expand": "/experiment/input-size",
                "path": "/parameter2" 
            } 
        ]
        ...
    }

The same technique can be used for build-time parameters.

## Consistent Pseudo-random number generation between languages

For replicability of experiments, the execution of implementations should be deterministic, identical between runs, and consistent between different implementations of the same benchmarks. In order to do so we need:
- Consistent inputs
- Consistent deterministic execution

which should lead to replicable and consistent outputs between runs and implementations.

By checking the output of a run against the expected output, we can detect errors that could be introduced by a compiler during the build phase, errors that are introduced when creating a variation of an implementation by hand, or execution errors that could be introduced by an execution environment. Moreover we can easily detect inconsistencies between implementations in different languages.

In order to achieve those properties we need an implementation pseudo-random number generator for each implementation language that is:

1. Fast to execute in all languages for quick input generation;
2. Consistent (it should produce the same sequence of numbers in all languages);

In addition, it should be easy to maintain and distribute on as many platforms as possible.

We support two random number generators, one based on the V8 Benchmarking Suite random number generator that we used in the [Ostrich suite](TODO-PUT-REFERENCE) and the other based on the Mersenne-Twister algorithm. Both support C, JavaScript, and MATLAB. The former is supported for historical reasons but we encourage newer benchmarks to use the latter.

### V8 Benchmarking suite generator

#### C

#### JS

#### MATLAB

### Mersenne-Twister algorithm

#### C

#### JS

#### MATLAB

#### Python/Numpy

## Automatic verification of the output's correctness

The implementation runners are responsible for testing that an execution output corresponds to its expected value. If the output is invalid, the runner should either return a non-zero error code or throw an exception. The details depend on the execution environment implementation.

For implementations whose output is non-scalar (ex: multidimensional arrays) but integer-based, it is usually convenient, application-independent, and fast to compute a checksum on the output value. The various templates show how to use readily available functions to do so.

TODO: Provide list to reusable functions/libraries

In addition, the tools can verify the output consistency between different implementation executions automatically if the execution of the runner specifies an output value for the execution:

    {
        ...
        "output": *value*
        ...
    }

This is useful when developing a new implementation which does not have predefined tests for correctness but another implementation which does exists. By executing the existing implementation with the new implementation in the same run, if the output is consistent with the existing implementation and the existing implementation executes without errors, then we know that the new implementation execution is correct.

## Time measurement on the core computation

The implementation runner is also responsible for providing the time taken for the core computation to execute using the 'time' property on the output object:

    {
        ...
        "time": *time-in-seconds*
        ...
    }

## Factorization of reusable code with dependencies

Multiple implementations in the same language, possibly in different benchmarks, may need to perform similar operations. The operations can then be factored out into reusable libraries. Those libraries can be installed either using a number of options:
1. Supported language-specific package manager (ex: npm for Node.js/JS);
2. Arbitrary list of commands in an install script;
3. Wu-Wei artifact dependency.

We cover each in turn.

### Supported language-specific package manager

### Custom install script

### Wu-Wei dependency

## Licensing

## Attribution of credit


# 3. Create a new Wu-Wei implementation (and benchmark) from scratch


### Choosing the input-size values

#### Build-time parameters

#### Run-time parameters

#### File-input

#### Multiple input parameters

# Language-Specific Instructions

## MATLAB


### Use the Ostrich random-number generator compiled with MEX



### Use the built-in Mersenne-Twister algorithm

## JavaScript

### Use Ostrich random-number generator

### Use the Mersenne-Twister algorithm compiled with Emscripten 

## C

### Use the Ostrich random-number generator

### Use the Mersenne-Twister algorithm


# Distribution of the new implementation

## Integration with the original benchmark

## Stand-alone distribution with manual installation
