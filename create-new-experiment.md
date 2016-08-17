# Experiments

Experiments describe which artifacts and parameters were used to obtain performance results. They allow the concise specification of a complete configuration. For example, the entire setup that was created step-by-step in the [replicate an existing experiment guide](replicate-an-experiment.md) can be specified with the following experiment description file:

    {
      "type": "experiment",
      "short-name": "replication",
      "dependencies": [
        "https://github.com/Sable/polybench-correlation-benchmark.git",
        "https://github.com/Sable/ostrich-gcc-compiler.git",
        "https://github.com/Sable/ostrich-native-environment.git"
      ],
      "iteration-number": 3
    }

The 'dependencies' property list the source for the artifacts of the experiment. The 'iteration-number' property correspond to the number of iterations for the runs. 

Replicating the experiment using the experiment file therefore only needs the following steps:

    mkdir repo && cd repo
    wu init
    wu install https://raw.githubusercontent.com/Sable/wu-wei-handbook/master/experiments/replication-experiment.json
    wu run replication # Asks user input for platform short-name 
    wu report

We strongly encourage the distribution of experiment files alongside results to make replication easier for others.
