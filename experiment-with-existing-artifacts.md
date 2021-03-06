# Perform an experiment using existing artifacts

Performing an experiment using existing artifacts is the simplest way to try the [benchmarking cycle](https://github.com/Sable/wu-wei-handbook#overview). You will learn about the major conventions of Wu-Wei as you perform the steps. The goal of this tutorial is to reproduce a performance study over a single benchmark on a new platform (machine and OS combination).

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

The platform contains information about both the hardware of the machine, the operating system, and various metrics about both that may serve in analyzing results. You can setup your platform with this command:

    wu platform
    
You are asked to provide a short name to reference the platform. The information for your platform is stored under

    platforms/*short-name*/platform.json

You may add additional JSON properties that could not be inferred automatically and they will be preserved in all configurations of builds, runs, or reports later.

If you run the tools from multiple machines using the same shared repository, let's say with NFS or with git repositories that are manually synchronized, you may save multiple platforms at the same time. Wu-Wei will automatically select the current platform based on the current cpu, gpu, memory, and os specifications. You can therefore gather all your results in the same repository.

You can test that the automatic selection of the platform is correctly working by using

    wu list
    
It should show the current platform short-name and all available artifacts. At this point there should be no other artifacts.
    
## Install artifacts

Now that we have a working repository, we will add some artifacts for performance measurements. To obtain a performance measurement we need at least one benchmark with one valid implementation, one compatible compiler to create a valid build, and one execution environment compatible with the created build to measure performance metrics. We will install them in turn.

### Installing an existing benchmark

We will use the [PolyBench/C](http://web.cse.ohio-state.edu/~pouchet/software/polybench/) correlation benchmark that has been repackaged for Wu-Wei and is available as a [github repository](https://github.com/Sable/polybench-correlation-benchmark.git):

    wu install https://github.com/Sable/polybench-correlation-benchmark.git
    
The installation can be tested by running:
    
    wu list
    
and verify that the 'correlation' benchmark is available. We can list the available implementation for that benchmark with:

    wu list correlation
    
It should list 'c' as an available implementation. 

The benchmark is self-contained, all the files related to the 'correlation' benchmark are under the 'benchmarks/correlation' directory. By looking in the filesystem we should find at least those:

    benchmarks/correlation/benchmark.json
    benchmarks/correlation/implementations/c/correlation.c
    benchmarks/correlation/implementations/c/correlation.h
    benchmarks/correlation/implementations/c/implementation.json
    benchmarks/correlation/implementations/c/utilities/polybench.c
    
The 'benchmark.json' description file contains information that is common to all implementations of the benchmark, in the same or different programming languages. It minimally contains the short-name, version, and input sizes for all versions. All implementations of the benchmark are saved under their respective 'implementations/*implementation-short-name*' folder. The short-name may or may not correspond to the language implementation. That enables multiple variations written in the same language to coexist in the same benchmark. A benchmark may have another implementation, let's say 'c-optimized' that correspond to a hand-optimized version of the default version to compare against. It may also have an implementation in a different language such as JavaScript under 'js'.

Each implementation has its own 'implementation.json' description file that specifies minimally the name of the implementation, the source language, which files are used for a build and what parameters to pass to the executable created. Additional parameters are specific to a given source language.

An artifact description file may specify dependencies that are installed alongside the artifact. These dependencies enable the factoring of source code that may be common to multiple artifacts. That is the case for the '[utilities](https://github.com/Sable/polybench-c-utilities.git)' folder that is common to all PolyBench/C implementations. It is listed as a dependency inside the 'c/implementation.json' file.

The rest of the files are source files or information about licensing, authors, etc.

### Installing an existing compiler

A compiler processes a benchmark implementation to create an executable version, ready to be run on an execution environment. We will use the gcc compiler for this experiment. First ensure that gcc is installed on your system beforehand by typing 'gcc' on the commandline as the compiler we are installing is simply a wrapper around the system one. You can then install the wrapper with:
    
    wu install https://github.com/Sable/ostrich-gcc-compiler.git
    
Check that the installation succeed by running:

    wu list 
    
You should see 'gcc' in the available compilers.

The 'compilers/gcc' directory contains the 'compiler.json' description file but not the gcc executable because it uses the system executable. Other compilers may contain the executable itself, which is handy to compare multiple versions of the same compiler without having to modify the system configuration.

We can now test the build step by compiling the correlation benchmark's c implementation with the gcc compiler:

    wu build correlation c gcc
    
The build is stored under 'builds/*build-config-hash*' where *build-config-hash* is specific and deterministically derived from the configuration used to produce it by hashing its string representation.
    
You can obtain more information about where the build was stored by adding the verbose option ('-v'):
    
    wu build correlation c gcc -v
    
Moreover, Wu-Wei automatically matches compatible artifacts available for a given action to create all possible valid combinations. Since we have only one implementation and one compiler installed, it is equivalent to do:

    wu build -v
    
If a build error happens, the error will be printed in the console, and the log will be saved in the 'build/*build-config-hash*' directory for later investigation. A second build created from the same configuration will overwrite the previous files in the directory. 

All previous builds may be removed by doing:

    wu build --clear
    
### Installing an existing execution environment

An execution environment executes a build to produce performance metrics used for analyzes. It might produce additional output files. You can install an execution environment that execute native executables directly by doing:

    wu install https://github.com/Sable/ostrich-native-environment.git
    
You can verify that the installation was successful by ensuring the 'native' environment is listed in the available environments with:
   
    wu list
    
All execution environments share the same commandline interface by each having a 'run' script that accepts the same kind of arguments on the commandline. Doing so allows the abstraction over the peculiarities of various execution environments, such as Web browsers, and enables implementations to simply specify which arguments should be passed. The specifics of mapping language specific types and handling data transfer is handled by the run script once for all compatible implementations.

An environment is stored under 'environments/*short-name*' and minimally has a 'run' script that follows the [commandline conventions](create-new-environment.md) and an 'environment.json' description file whose most important use is to describe which language is supported for automatic compatibility matching with builds when running the implementations.

Now that we have an environment installed, we can run the correlation benchmark's c implementation compiled with gcc on the native environment by doing:

    wu run correlation c gcc native
    
Again, because Wu-Wei provides automatic matching of compatible artifacts we can run all valid combinations of artifacts with:

    wu run
   
Each valid combination is printed in the console as it is executed. Note that each build is rebuilt for every new run to ensure the latest version is used. You can see both the build process and the output of each run by using the verbose option ('-v'):

    wu run -v
    
You can vary the size of the input used by using the '--input-size' ('-is') option with the 'small', 'medium', or 'large' value, whose concrete values are specific to a benchmark and correspond to the parameter values provided in the 'benchmark.json' description file:

    wu run --input-size small
    
You can finally run a given configuration multiple times to control for the non-deterministic behaviour of your platform by specifying a number of iteration to execute, let's say 3 in this example:

    wu run -n 3
   
The metrics gathered for each individual iteration are saved alongside the run configuration under the 'runs/*datetime*/run.json' file. The output of individual runs are saved under 'runs/*datetime*/*build-config-hash*/*iteration-number*'. For convenience, a symbolic link to the latest run is updated for every new successful run. The latest run results are therefore under 'runs/latest/'.

Execution errors during runs are printed in the console.

    
## Report execution results (runs)

Once one or multiple runs have been successfully executed and saved, we can generate a summary of the results by doing:

    wu report

The output will be shown in a markdown format for easy sharing on github and elsewhere. On my machine I obtained the following results:

### Invariants (configuration parameters that are the same for all runs) ###

| category | short-name |
| -------- | ---------- |
|    implementation | c |
|    compiler | gcc |
|    platform | mba-2011 |
|    environment | native |
|    input-size | medium |

### Results ###

|benchmark   | mean     | std      | min      | max      | repetitions|
|----------- | -------- | -------- | -------- | -------- | -----------|
|correlation | 18.9680s | +-0.93%  | 18.7983s | 19.1492s | 3          |

The 'Invariants' part list all the configuration parameters that are the same for all results. The results are then listed with summary metrics. Multiple runs that were obtained with the same configuration are aggregated into a higher number of repetitions.

# Summary

We saw how to perform an experiment by initializing a repository with 'wu init', installing artifacts with 'wu install', running valid combinations of artifacts with 'wu run', and finally report summarized results with 'wu report'. All phases generated information about the configuration that was used to obtain the results for traceability and easier comparison with later results. We finally got an overview of the file system organization and commandline conventions for artifacts. You can look at the other [guides](https://github.com/Sable/wu-wei-handbook/blob/master/README.md#specific-guides) for more information about how to modify existing artifacts and create new ones to use with the tools and share with others.
