
# Leveraging compatibilities and dependencies between artifacts for easier evolution of artifacts

The format of the description files for any artifact support the explicit listing of compatible artifacts and of dependency relationships to a directory of files or other artifacts. The compatible artifacts listed under the "compatibilities" property of the description file are those that are known to work with a given artifact. It serves both as documentation for potential users of that artifact and enables automatic testing of the artifact as it evolve to ensure it keeps working with at least some other artifacts. The dependencies of an artifact listed under the "dependencies" property are typically  directories of files that are shared between multiple artifacts or an existing project that is wrapped to follow Wu-Wei conventions. 

Compared to a monolithic benchmark suite, organizing artifacts with dependencies that can be automatically installed enables a user of the artifacts to install only those that are necessary for his task. It also enables the ecosystem of artifacts to grow faster because it removes the need for a maintainer to integrate new benchmarks and artifacts into a suite for them to be known and distributed. We can get back the benefits of a monolithic benchmarking suite by listing all artifacts of a suite as dependencies on an experiment as is done for this [AspectMatlab experiment](https://github.com/elavoie/aspect-matlab-experiment/blob/master/experiment.json).

# Format

The format for artifact descriptions is the following. Both "compatibilities" and "dependencies" are optional and share the same *dependency* format for individual items:

    {
        ...
        "compatibilities": [ *dependency*, ... ],
        "dependencies": [ *dependency*, ... ]
        ...
    }
  
The format for individual dependencies (*dependency*) is the following. The destination is optional is some cases:

    *dependency* =
    {
        "source": *source*,
        "destination": *path*
    }

# Source for compatibilities and dependencies

| Type         | Description                                           | Source Format          | Example                  |
| :----------- | :---------------------------------------------------- | :--------------------- | :----------------------- |
| Git Repo     | Git repository, accessible using either the git or http(s) protocol | git@<domain>:<path> or http(s)://<domain>/<path>.git | git@github.com:Sable/ostrich-c-implementation-common https://github.com/Sable/ostrich-c-implementation-common.git |
| File archive | Compressed file archive with zip or gzip. Can be located in the file system or at any url. Especially useful for GitHub releases. | ./path/to/archive.tar.gz /path/to/archive.zip https://domain.com/path/to/archive.tar.gz |
| Local directory | Directory of files in the filesystem. Useful for tracking changes in projects locally. | /path/to/directory ./path/to/directory |

# Destination for compatibilities and dependencies

Artifacts usually do not need an explicit destination since the installation tool with figure out in most cases where it should be installed in a Wu-Wei repository. This is true for benchmarks, compilers, environments, and experiments. The only exception are implementations because there is no way at the moment to specifiy that an implementation belongs to a given benchmark. There are plans to support it eventually. In the meantime, an implementation listed as a dependency should have an explicit destination that specifies where it should be installed.

Some artifacts have dependencies that are not artifacts. In these cases, the destination directory also needs to be specified explicitly.

# Installing compatibilities

By default the compatibilities of an artifact are not installed. To ask the install tool to install them automatically you may pass the "--compatibilities" flag. For a new artifact you do:

    wu install https://github.com/Sable/polybench-correlation-benchmark.git --compatibilities
    
For an artifact that has already been installed you do:

    cd path/to/artifact
    wu install --compatibilities
   
# Avoiding the installation of dependencies

By default, the dependencies of an artifact are always installed. However they will be skipped if the destination directory already exists. However you may skip the installation of dependencies by passing the '--not-recursive' option to the installer:

    wu install --not-recursive <source>
    

