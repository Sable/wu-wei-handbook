# Replicate an experiment using an experiment description file

Experiments describe which artifacts and parameters were used to obtain performance results. They allow the concise specification of a complete configuration. For example, the entire setup that was created step-by-step to [perform an experiment using existing artifacts](experiment-with-existing-artifacts.md) can be specified with the following experiment description file:

    {
      "type": "experiment",
      "short-name": "replication",
      "dependencies": [
        "https://github.com/Sable/polybench-correlation-benchmark.git",
        "https://github.com/Sable/ostrich-gcc-compiler.git",
        "https://github.com/Sable/ostrich-native-environment.git"
      ],
      "iteration-number": 3,
      "input-size": "medium"
    }

The 'type' property specifies that it is an experiment. The 'short-name' dependencies is used to refer to the experiment description with the commandline interface of the tools. The 'dependencies' property list the source for the artifacts of the experiment. The 'iteration-number' property specifies the number of iterations for the runs. The 'input-size' property specifies the size of the input to use.

After installation, the experiment description file is available under '*repository-root*/experiments/*experiment-short-name*/'. It is also understood by the 'run' command so experiment parameters such as the 'iteration-number' will be used to perform the experiments.

Replicating the experiment using the experiment file therefore only needs the following steps:

    mkdir repo && cd repo
    wu init
    wu install https://raw.githubusercontent.com/Sable/wu-wei-handbook/master/experiments/replication-experiment.json
    wu run replication # Asks user input for platform short-name 
    wu report

We strongly encourage the distribution of experiment files alongside results to make replication easier for others. A reference for all experiment description properties is available [here](https://github.com/Sable/wu-wei-handbook#experiment).
