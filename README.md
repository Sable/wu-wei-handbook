# Wu-Wei Handbook

The [Wu-Wei benchmarking toolkit](https://github.com/Sable/wu-wei-benchmarking-toolkit) is (1) a set of conventions for organizing artifacts related to benchmarking on the file system and describing them using JSON files and (2) a set of commandline utilities for performing common benchmarking tasks with artifacts. The endgoal is to make benchmarking results drastically easier to obtain, replicate, compare, analyze, publish, etc. and to eventually obtain a benchmarking commons similar to other package repositories (ex: Linux distributions or the npm ecosystem).

# Overview

The tools are built around the following conceptual cycle. Initially, artifacts (benchmarks, implementations, compilers, execution environments, etc.) are gathered into a repository from various sources (git repositories, file archives, directories on the file system, etc.). The different benchmark implementation, possibly in different programming languages, are then processed using a compiler that may translate them to another or the same language to obtain a build, an executable version of the benchmark and the configuration of artifacts that was used to create it. The build is then executed on an execution environment (natively on the operating system, in a virtual machine for a programming language, etc.) to obtain a run, the result of the execution and the associated metrics (time to completion, memory or energy used, etc.) as well as the configuration of artifacts, the platform on which the build was executed, and the experience parameter. Finally, multiple runs may be aggregated into a single human-readable report (ascii, html, etc.) that summarizes the execution results, the metrics gathered, and comparisons between the different configurations used. Those reports may be used for academic publications, for internal reports, or self-publication online for collaboration with other people.

The process is linear for a simple replication study but in practice there are cycles between the different phases based on the feedback obtain from the reports. That may involve the gathering of more artifacts for comparison, the modification of existing implementations, compilers, or environments while keeping track of the previous versions. The cycle are repeated until the whole development/analysis process converges to an interesting result. The modified or newer versions of artifacts may then be shared online for others to be used directly, to replicate experiments, or to be extend/improved upon. 

The following figure summarizes the process:

![Image](BenchmarkingCycle.png)

Conceptually, there are three major times for a benchmark implementation:
  - **Design Time**: when a modification may be made by a human or externally to the wu-wei cycle
  - **Static Time**: when a moditifcation may be done automatically using only information from the source code
  - **Run Time**: when a modification may be done automatically using the execution information

An execution environment might run the same or multiple versions of an implementation before producing a result, therefore it might interleave multiple executions and compilation steps before converging to a final result. In the simplest and most common case however, it executes the implementation only once to gather metrics and produces an output with the metric values.

## Artifact Categories

### Installed Artifacts

### Generated Artifacts

   TODO


# Installing the tools

## (Recommended) Install nvm and activate a recent (>6.3.1) version of Node.js
    
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash
    nvm use 6.3.1

## Install Wu-Wei

    git clone https://github.com/Sable/wu-wei-benchmarking-toolkit.git
    cd wu-wei-benchmarking-toolkit
    npm install
    npm link


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
    
### Install all artifacts that are listed as compatible with the benchmark

    wu install https://github.com/Sable/polybench-correlation-benchmark.git --compatibilities
    
    
## Verify the installation and setup the current platform

    wu list
    
## Execute all valid combinations of artifacts on the current platform

    wu run # equivalent to 'wu run correlation c gcc native'
    
## Report execution results (runs)

    wu report


# Add a new language implementation to an existing benchmark

## Language-specific guidelines

   MATLAB: TODO

    TODO
    
# Add a new compiler

    TODO
    
# Miscellaneous    
    
## Clear existing builds and runs
    
    wu build --clear
    wu run --clear
    

# Terminology

| Terminology      | Definition |
| :--------------- | :--------- |
| artifact         | Elementary component with a file representation and associated JSON meta-information |
| repository       | Collection of artifacts on the file system |
| action           | Operation that can be performed on artifact(s) |

| Artifacts        | Definition |
| :--------------- | :--------- |
| benchmark        | Abstract algorithm that performs a useful numerical task |
| build            | Runnable implementation that is ready to be executed on an environment and the configuration that was used to create it |
| compiler         | Program that processes a benchmark implementation to produce a new implementation (in the same or a different language) |
| configuration    | Combination of artifacts and their associated parameters necessary for performing an action |
| environment      | Additional virtualization layer executing on top of a platform (or another environment) a benchmark might execute on |
| experiment       | Combination of a configuration and experimental parameters that determine what, where, and how an implementation is to be executed |
| implementation   | Realization of a benchmark in a specific language (ex: C, assembly) and packaged in a particular format (ex: text file, binary, webpage) which might be directly runnable or not |
| platform         | Hardware and (native) OS combination for the machine to run benchmarks on |
| report           | Collection of figure(s) and the configuration that was used to produce them |
| run              | Execution report (ex: timing results, memory usage, etc.) and the experiment that produced it |

| Actions          | Definition |
| :--------------- | :--------- |
| building         | Creation, from a configuration, of an implementation that is executable on an environment |
| installing       | Retrieval, and initialization of an artifact in the repository |
| reporting        | Aggregation of multiple compatible runs, selection of significant results, and production of  human-readable figure(s) from those results |
| running          | Execution of an implementation on a stack of environment(s), and production of a run from the monitoring of its execution |


    
