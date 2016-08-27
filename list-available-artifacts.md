Artifacts compatible with the Wu-Wei tools are listed here. If you have created a new one and you don't see it listed here, send a pull request with your addition to the corresponding table and we will add it.

# Available benchmarks

| Name     | Default Languages | Description                                             | Source                           |
| :------- | :---------------- | :------------------------------------------------------ | :------------------------------- |
| template | none              | Demonstration of the conventions for structuring a benchmark | https://github.com/Sable/template-benchmark.git |


# Available implementations

| Benchmark Name | Name        | Language    | Description                 | Source                                    |
| :------------- | :---------- | :---------- | :-------------------------- | :---------------------------------------- |
| template       | c           | c           | Canonical implementation for a C implementation | https://github.com/Sable/c-implementation-template.git |
| template       | matlab      | matlab      | Canonical implementation for a MATLAB implementation | https://github.com/Sable/matab-implementation-template.git |
| template       | js           | js         | Canonical implementation for a JavaScript implementation | https://github.com/Sable/js-implementation-template.git |


# Available compilers

| Language   | Name          | Description                                         | Source                               |
| :--------- | :------------ | :---------------------------------------------------| :----------------------------------- |
| C          | gcc           | Wrapper for the GCC compiler           | https://github.com/Sable/ostrich-gcc-compiler.git |
| C++        | g++           | Wrapper for the G++ compiler           | https://github.com/Sable/ostrich-gpp-compiler.git |
| JavaScript | browserify    | Wrapper for browserify           | https://github.com/Sable/ostrich-browserify-compiler.git|
| MATLAB     | matlab-concat | Concatenate files into a single file | https://github.com/Sable/ostrich-matlab-concatenate-compiler.git |
| MATLAB     | aspect-matlab | Source-to-Source transformer that weaves an aspect into the original source code. Aspects may be used for instrumentation, dynamic analyses, etc. | https://github.com/Sable/aspect-matlab-compiler |


# Available environments

| Language   | Name          | Description                                         | Source                               |
| :--------- | :------------ | :---------------------------------------------------| :----------------------------------- |
| x86        | native        | Execute an x86 executable natively                  | https://github.com/Sable/ostrich-native-environment.git |
| js         | chrome        | Wrapper for executing benchmarks in the Chrome browser on linux and osx | https://github.com/Sable/ostrich-chrome-environment.git |
| js         | firefox       | Wrapper for executing benchmarks in the Firefox browser on linux and osx | https://github.com/Sable/ostrich-firefox-environment.git |
| js         | safari        | Wrapper for executing benchmarks in the Safari browser on osx | https://github.com/Sable/ostrich-safari-environment.git |
| js         | node          | Wrapper for executing benchmarks in Node.js on linux and osx | https://github.com/Sable/ostrich-node-environment.git |
| MATLAB     | matlab-vm     | Wrapper for executing benchmarks in MATLAB on linux and osx | https://github.com/Sable/ostrich-matlab-environment.git |
| MATLAB     | octave        | Wrapper for executing benchmarks in Octave on linux and osx | https://github.com/Sable/ostrich-octave-environment.git |


# Available experiments

| Name               | Description                                             | Source                              |
| :----------------  | :------------------------------------------------------ | :---------------------------------- |
| socs-aspect-matlab | Experiment that compares the speed of the matlab template implementation, with and without the compilation of an aspect on the 2013 and 2014 versions of Matlab installed on the McGill School of Computer Science computers | https://github.com/elavoie/aspect-matlab-experiment |
