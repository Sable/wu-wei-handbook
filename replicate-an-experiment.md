# Replicate a benchmarking experiment

Replicating an existing experiment is the simplest way to try the [benchmarking cycle](https://github.com/Sable/wu-wei-handbook#overview). You will learn about the major conventions of Wu-Wei as you perform the steps for replication. The goal of this tutorial is to reproduce a performance study over a single benchmark on a new platform (machine and OS combination).

Make sure the Wu-Wei tools are [installed correctly](https://github.com/Sable/wu-wei-handbook#installing-the-tools) before continuing. To test the installation simply type the following on the commandline:

    wu
   
You should see an help message that list the available commands.


## Creating a new benchmarking repository

We first need a repository to organize the different artifacts that we need for our experiments. A repository in Wu-Wei is simply a directory with a '.wu' hidden directory, and a few conventions for organizing the artifacts that are gathered. It is initialized by with the 'wu init' command:

    mkdir my-repo
    cd my-repo
    wu init
    
By listing the directories you should obtain:
    ls -a
    output: .wu		benchmarks	builds		compilers	environments	experiments	platforms	runs
    

| Directory             |            Content                                              |
| :-------------------- | :-------------------------------------------------------------- |
| .wu                   | Wu-Wei configuration and temporary files used during operations |
| benchmarks            | Benchmark artifacts under *short-name*/benchmark.json and their various implementations          |
| builds                | Each generated build has at least  *configuration-hash*/build.json with the configuration that generated the build and *configuration-hash*/executable with the executable version of an implementation |
| compilers             | Compiler artifacts under *short-name*/compiler.json and associated files |
| environments          | Execution environment artifacts under *short-name*/environment.json, their associated *short-name*/run script and other associated files |
| experiements          | Experiments under *short-name*/experiment.json |
| platforms             | Known platform configurations under *short-name*/platform.json |
| runs                  | Each generated run has at least *datetime*/run.json. Outputs from runs are stored under *datetime*/*configuration-hash*/*iteration-number* |
    
    
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
