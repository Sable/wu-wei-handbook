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

The files that perform the core computation (the files under the implementation.json 'core-files' by convention) can then be modified to obtain a different variation. A new copy of the implementation may be created for each additional interesting variation that is tried. 

The main advantage of this approach is that all the rest of the Wu-Wei infrastructure and benchmark implementation need not be modified to compare the performance metrics of the variation(s) against the base version. The presence of a new (valid) implementation directory is sufficient for the tools to use it automatically for all the other phases of the benchmark cycle.
    
# 2. Create a new implementation from an existing Wu-Wei implementation (and benchmark) in a different language

Creating a new implementation in a different language for an existing benchmark requires thinking about a few issues in order to make the comparison between the different implementations correct and the new implementation convenient to reuse. To make the task easier, we suggest using existing templates that correctly address those issues as starting points and replace their code with custom code written from scratch or ported from other benchmark suites.

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

For any benchmark, the 'small' value means the implementation should execute quickly but we do not really bother about the time spent in the computation. This is used to quickly test the correctness of the implementation (after various automatic transformations during the build phase). The 'medium' value means the implementation should execute long enough for performance measurements to stabilize but still quickly enough to deliver timely results, typically 1-10 seconds. The 'large' value means the input should be scaled up to see if the 'medium' results how the performance varies with a bigger load.

Since the concrete values are common between different implementation, they are specificed in the 'benchmark.json' description file. In our example, ('benchmarks/template/benchmark.json') you will find the following values:

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

### Using an expand macro

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

### Build-time and Run-time parameters

For most dynamic languages, the implementation parameters are passed at run time to the runner file. If you are working with an implementation in one of those languages, you may safely skip this section. However, some benchmarks for static languages do require parameter(s) at build time. This is the case for the following PolyBench/C example...


TODO

        {
            "type": "implementation",
            "short-name":"c",
            ...
        }


### Multiple arguments

TODO


## Consistent Pseudo-random number generation between languages

- Consistent deterministic execution
- Consistent inputs
- Consistent outputs

## Automatic verification of the output's correctness

## Time measurement on the core computation

## Factorization of reusable code with dependencies

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
