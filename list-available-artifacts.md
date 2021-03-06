Artifacts compatible with the Wu-Wei tools are listed here. If you have created a new one and you don't see it listed here, send a pull request with your addition to the corresponding table and we will add it.

# Benchmarks

| Name     | Default Languages | Description                                             | Source                           |
| :------- | :---------------- | :------------------------------------------------------ | :------------------------------- |
| babai    | matlab            | Computes the closest vector in a lattice using the babai algorithm | https://github.com/Sable/babai-benchmark.git |
| backprop | c,js,matlab       | Implements the backward propagation of errors algorithm for training neural networks. | https://github.com/Sable/backprop-benchmark.git |
| bfs      | c,js,matlab       | Implements the breath-first search algorithm on a randomly generated graph. | https://github.com/Sable/bfs-benchmark.git |
| bubble   | c,js,matlab       | Implements the bubblesort algorithm. | https://github.com/Sable/bubble-benchmark.git |
| capr     | matlab            | Computes the capacitance of a transmission line using finite difference and Gauss-Seidel iteration | https://github.com/Sable/babai-benchmark.git |
| clos     | matlab            | Computes the transitive closure of a graph. | https://github.com/Sable/clos-benchmark.git |
| collatz  | matlab            | Implementation of the Collatz conjecture. | https://github.com/Sable/collatz-benchmark.git |
| correlation | c              | Implements a correlation algorithm for data mining. Taken from PolyBench/C. | https://github.com/Sable/polybench-correlation-benchmark.git |
| crc      | matlab,c,js       | An error-detecting code which is designed to detect errors causedby network transmission or anyother accidental error      |https://github.com/Sable/crc-benchmark.git
| crni     | matlab          | Compute the Crank-Nicholson solution to the one-dimensional heat equation. | https://github.com/Sable/crni-benchmark.git |
| dirich   | matlab          | Compute the Dirichlet solution to Laplace's equation. | https://github.com/Sable/dirich-benchmark.git |
| fdtd       | matlab          | Apply the Finite Difference Time Domain (FDTD) technique on a hexahedral cavity with conducting walls. | https://github.com/Sable/fdtd-benchmark.git |
| fft_simple | matlab          | Performs the Fast Fourier Transform (FFT) function.   | https://github.com/Sable/fft-simple-benchmark.git |
|fft         | matlab,c,js     | 2D Fast Fourier Transform (FFT).                      | https://github.com/Sable/fft-benchmark.git
| fiff       | matlab          | Compute the finite-difference solution to a given wave equation. | https://github.com/Sable/fiff-benchmark.git |
| lavamd     | matlab,c,js     | This benchmark calculates particle potential and relocation due to mutual forces between particles within a large 3D space. | https://github.com/Sable/lavamd-benchmark |
| lgdr       | matlab          | Compute the normalized, orthogonormal Legendre polynomials. | https://github.com/Sable/lgdr-benchmark.git |
| lud        | matlab,c,js     | Perform a Lower-Upper Decomposition. | https://github.com/Sable/lud-benchmark.git |
| makechange | matlab          | Compute the ways to make change for a given amount using dynamic programming. | https://github.com/Sable/makechange-benchmark.git |
| matmul   | matlab            | Implementation of the matrix multiplication algorithm. | https://github.com/Sable/matmul-benchmark.git |
| mesh2d   | matlab            | MESH2D is a MATLAB program which generates unstructured meshes in 2D, by Darren Engwirda. | https://github.com/Sable/mesh2d-benchmark.git |
| mcpi     | matlab            | Calculate pi by the Monte Carlo method. | https://github.com/Sable/mcpi-benchmark.git |
| nb1d     | matlab            | Simulate the 1-dimensional n-body problem. | https://github.com/Sable/nb1d-benchmark.git |
| nqueens  | matlab,c,js       | Count the number of valid positions for n-queens on a NxN board. | https://github.com/Sable/nqueens-benchmark.git |
| numprime | matlab,c          | Count the number of primes up to a given upper bound. | https://github.com/Sable/numprime-benchmark.git |
| nw       | matlab,c,js       | Calculates the optimal global alignment of two DNA sequences. | https://github.com/Sable/nw-benchmark.git |
| pagerank | matlab,c,js       | PageRank is a link analysis algorithm used by Google Search and it assigns a numerical weighting to each element of a hyperlinked set of documents. | https://github.com/Sable/pagerank-benchmark.git |
| srad     | matlab,c,js       | Speckle Reducing Anisotropic Diffusion Benchmark.     | https://github.com/Sable/srad-benchmark.git |
| template | none              | Demonstration of the conventions for structuring a benchmark. | https://github.com/Sable/template-benchmark.git |



# Additional Implementations

| Benchmark Name | Name        | Language    | Description                 | Source                                    |
| :------------- | :---------- | :---------- | :-------------------------- | :---------------------------------------- |
| template       | c           | c           | Canonical implementation for a C implementation. | https://github.com/Sable/c-implementation-template.git |
| template       | matlab      | matlab      | Canonical implementation for a MATLAB implementation. | https://github.com/Sable/matab-implementation-template.git |
| template       | js           | js         | Canonical implementation for a JavaScript implementation. | https://github.com/Sable/js-implementation-template.git |


# Compilers

| Language   | Name          | Description                                         | Source                               |
| :--------- | :------------ | :---------------------------------------------------| :----------------------------------- |
| C          | gcc           | Wrapper for the GCC compiler.           | https://github.com/Sable/ostrich-gcc-compiler.git |
| C++        | g++           | Wrapper for the G++ compiler.           | https://github.com/Sable/ostrich-gpp-compiler.git |
| JavaScript | browserify    | Wrapper for browserify.           | https://github.com/Sable/ostrich-browserify-compiler.git|
| matlab     | matlab-concat | Concatenate files into a single file. | https://github.com/Sable/ostrich-matlab-concatenate-compiler.git |
| matlab     | aspect-matlab | Source-to-Source transformer that weaves an aspect into the original source code. Aspects may be used for instrumentation, dynamic analyses, etc. (Note that the link is to the Wu-Wei wrapper) | https://github.com/Sable/aspect-matlab-compiler.git |
| matlab     | matjuice      | Static compiler that compiles matlab to JavaScript using the McLabCore framework. (Note that the link is to the Wu-Wei wrapper) | https://github.com/Sable/matjuice-compiler.git |


# Environments

| Language   | Name          | Description                                         | Source                               |
| :--------- | :------------ | :---------------------------------------------------| :----------------------------------- |
| x86        | native        | Execute an x86 executable natively.                  | https://github.com/Sable/ostrich-native-environment.git |
| js         | chrome        | Wrapper for executing benchmarks in the Chrome browser on linux and osx. | https://github.com/Sable/ostrich-chrome-environment.git |
| js         | firefox       | Wrapper for executing benchmarks in the Firefox browser on linux and osx. | https://github.com/Sable/ostrich-firefox-environment.git |
| js         | safari        | Wrapper for executing benchmarks in the Safari browser on osx. | https://github.com/Sable/ostrich-safari-environment.git |
| js         | node          | Wrapper for executing benchmarks in Node.js on linux and osx. | https://github.com/Sable/ostrich-node-environment.git |
| MATLAB     | matlab-vm     | Wrapper for executing benchmarks in MATLAB on linux and osx. | https://github.com/Sable/ostrich-matlab-environment.git |
| MATLAB     | octave        | Wrapper for executing benchmarks in Octave on linux and osx. | https://github.com/Sable/ostrich-octave-environment.git |


# Experiments

| Name               | Description                                             | Source                              |
| :----------------  | :------------------------------------------------------ | :---------------------------------- |
| socs-aspect-matlab | Experiment that compares the speed of the matlab template implementation, with and without the compilation of an aspect on the 2013 and 2014 versions of Matlab installed on the McGill School of Computer Science computers. | https://github.com/elavoie/aspect-matlab-experiment |
