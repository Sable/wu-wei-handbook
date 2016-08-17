# Replicate a benchmarking experiment

## Creating a new benchmarking repository

    mkdir my-suite
    cd my-suite
    wu init
    
## Install artifacts

### Installing an existing benchmark

    wu install https://github.com/Sable/polybench-correlation-benchmark.git
    
### Installing an existing compiler
    
    wu install https://github.com/Sable/ostrich-gcc-compiler.git
    
### Installing an existing execution environment

    wu install https://github.com/Sable/ostrich-native-environment.git
    
or 
    
### Install all artifacts that are listed as compatible with the benchmark inside the implementation.json file(s)

    wu install https://github.com/Sable/polybench-correlation-benchmark.git --compatibilities
    
    
## Verify the installation and setup the current platform

    wu list
    
## Execute all valid combinations of artifacts on the current platform

    wu run # equivalent to 'wu run correlation c gcc native'
    
## Report execution results (runs)

    wu report
