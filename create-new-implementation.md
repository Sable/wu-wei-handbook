Creating high-quality implementations for benchmarks is the most time-consuming part of benchmarking and arguably the most important since it the foundation in the evaluation of many other tasks, such a creating new optimizations in compilers or virtual machines, source-code analyzes, comparing various implementations of a programming language, etc. There are many factors that need to be accounted for to make the resulting implementation useful, correct, convenient, and reusable by others. The payoff for a community is the same as for having high-quality publications or libraries readily available: we can stand on other people's shoulders.

In this guide, we introduce the important points for writing high-quality implementations from 3 starting points, from simplest to the most complicated:

1. An existing Wu-Wei implementation in the same language
2. An existing Wu-Wei implementation in a different language (part of an existing benchmark)
3. No existing Wu-Wei implementation

The various considerations are progressively introduced since the later tasks subsume the previous ones by requiring additional considerations.

# 1. Create a new implementation from an existing Wu-Wei implementation in the same language

This is the simplest case. It may be used to try different optimizations techniques by hand to investigate their performance tradeoffs before the creation of a fully automatic transformation in a compiler or execution environment (ex: loop unrolling strategies, vectorization strategies, optimization techniques from optimization guides for various architectures, different object/matrix representations in memory, etc.). It may also be used to try different existing libraries that implement some part of the core computation of a benchmark (ex: various JavaScript libraries from npm to perform numerical computations). You can see an example [here](https://github.com/Sable/lcpc16-analysis/tree/master/benchmarks/backprop/implementations), in which multiple versions of an implementation were compared for the performance evaluation in a publication about automatic vectorization techniques for MATLAB.

There are two manual steps to do, (1) copy and rename the existing implementation directory and (2) change the copy's short-name in the implementation.json file to the same value as the copy directory name. For example, suppose you have an existing c implementation for the template benchmark. Starting from the repository root you would do:

    cd benchmarks/template/implementations
    cp -r c c-modified
    cd c-modified
    open implementation.json # Modify the 'short-name' property to 'c-modified'

The files that perform the core computation (the files under the implementation.json 'core-files' by convention) can then be modified to obtain a different variation. A new copy of the implementation may be created for each additional interesting variation that is tried. The main advantage of this approach is that all the rest of the Wu-Wei infrastructure or benchmark implementation need not be modified to compare the performance metrics of the variation(s) against the base version. The presence of a new (valid) implementation directory is sufficient for the tools to use it automatically for all the other phases.
    

# 2. Create a new implementation from an existing Wu-Wei implementation in a different language


# 3. Create a new implementation (and benchmark) from scratch

### Pseudo-random number generation

### Input parameters

#### Build-time parameters

#### Run-time parameters

#### File-input

#### Multiple input parameters

### Output verification

#### Mathematically correct output for known inputs

#### Consistent output with other implementations

### Metric measurements

### Dependency management

### Choosing the input size

# Language-Specific Instructions

## MATLAB


### Use the Ostrich random-number generator compiled with MEX

    wu install https://github.com/Sable/benchmark-template.git 
    mkdir -p benchmarks/template/implementations
    wu install https://github.com/Sable/matlab-implementation-template.git --destination '{ "suite-root": "/benchmarks/template/implementations/matlab" }'


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
