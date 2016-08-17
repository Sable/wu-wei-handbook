# Replicate a benchmarking experiment

Replicating an existing experiment is the simplest way to try the [benchmarking cycle](https://github.com/Sable/wu-wei-handbook#overview). You will learn about the major conventions of Wu-Wei as you perform the steps for replication. The goal of this tutorial is to reproduce a performance study over a single benchmark on a new platform (machine and OS combination).

Make sure the Wu-Wei tools are [installed correctly](https://github.com/Sable/wu-wei-handbook#installing-the-tools) before continuing. To test the installation simply type the following on the commandline:

    wu
   
You should see an help message that list the available commands.


## Creating a new benchmarking repository

We first need a repository to organize the different artifacts that we need for our experiments. A repository in Wu-Wei is simply a directory with a '.wu' hidden directory, and a few conventions for organizing the artifacts that are gathered. It is initialized with the 'wu init' command:

    mkdir my-repo
    cd my-repo
    wu init
    
By listing the directories you should obtain:
    ls -a
    output: .wu		benchmarks	builds		compilers	environments	experiments	platforms	runs
    
Each of the directories contains artifacts that are manually created, installed automatically, or generated as part of the benchmarking cycle. We will go through each of them as we perform the individual tasks.

## Setup the platform information

The platform contains information about both the hardware of the machine, the operating system, and various metrics about both that may serve in analysing results. You can setup your platform with this command:

    wu platform
    
You are asked to provide a short name to reference the platform. The information for your platform is stored under

    platforms/*short-name*/platform.json

You may add addition JSON properties that could not be inferred automatically and they will be preserved in all configurations of builds, runs, or reports later.

If you run the tools from multiple machines using the same shared repository, let's say with NFS or with git repositories that are manually synchronized, you may save multiple platforms at the same time. Wu-Wei will automatically select the current platform based on the current cpu, gpu, memory, and os specifications. You can therefore gather all your results in the same repository.

You can test that the automatic selection of the platform is correctly working by using

    wu list
    
It should the current platform short-name and all available artifacts. At this point there should be none. We will add some shortly.
    
## Install artifacts

Now that we have a working repository, we will add some artifacts for performance measurements.

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
