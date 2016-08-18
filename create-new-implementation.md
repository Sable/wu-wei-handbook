# Create a new implementation for an existing benchmark

## From an existing implementation in the same language


## From an existing implementation in a different language


# Create a new implementation and a new benchmark

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
