##Download

The **regenie** source code is hosted on
[Github](https://github.com/rgcgithub/regenie).

##Installation

Note: **regenie** requires compilation with 
[GCC](https://gcc.gnu.org) version >= 5.1 (on Linux) 
or Clang version >=3.3 (on Mac OSX). 
It also requires having GFortran library installed.

### Pre-compiled binaries

Pre-compiled binaries are available in the 
[Github repository](https://github.com/rgcgithub/regenie/releases).
These are provided for Linux (including Centos7) and Mac OSX 
computing environments and are statically linked. 
For the Linux binaries, users should have GLIBC version >= 2.22 installed.
Additionally, they are provided compiled with Intel MKL library which
will provide speedups for many of the operations done in **regenie**. 


### Standard installation
1. **regenie** requires the
  [BGEN library](https://enkre.net/cgi-bin/code/bgen/dir?ci=trunk) so
  you will need to download and install that library.
2. Edit the BGEN_PATH variable in the `Makefile`
   to the BGEN library path.
3. On the command line type `make` while in the main source code directory.
4. This should produce the executable called `regenie`.

**regenie** has been enhanced to allow for gzip compressed input 
(for phenotype/covariate files) and output (for association results files)
 using the Boost Iostream library. 
If this library is installed on the system, you should compile using 
`make HAS_BOOST_IOSTREAM=1`. 

Furthermore, we have enabled compilation of **regenie** with
the Intel Math Kernel (MKL) library. You first need to have it installed 
on your system and modify the MKLROOT variable in the `Makefile`
to the installed MKL library path.

### With CMake
You can compile the binary using CMake version >=3.13 (instead of `make` as above).
```
mkdir -p build
cd build
cmake ..
make
```
This will generate the binary in the `build/` subdirectory. 
To use with Boost Iostreams and/or Intel MKL library,
add the corresponding flags before the `cmake` command on line 3
(e.g. `HAS_BOOST_IOSTREAM=1 cmake ..`).

### With Docker
Alternatively, you can use a Docker image to run **regenie**. 
A guide to using docker is available on 
the [Github page](https://github.com/rgcgithub/regenie/wiki/Using-docker).

### With conda
To install with conda, follow the directions 
[here](https://anaconda.org/bioconda/regenie).



##Computing requirements

We have tested **regenie** on 64-bit Linux and 64-bit Mac OSX computing environments.
 
Note that for Mac OSX computing environments, compiling is done without OpenMP, as the library is not built-in by default and has to be installed separately. 

### Memory usage
In both Step 1 and Step 2 of a **regenie** run the genetic data file is
read once, in blocks of SNPs, so at no point is the full dataset ever stored in
memory.

**regenie** uses a dimension reduction approach using ridge regression
  to produce a relatively small set of genetic predictors, that are
  then used to fit a whole-genome regression model. These genetic
  predictors are stored in memory by default, and can be relatively
  large if many phenotypes are stored at once.

For example, if there are \(P\) phenotypes, \(M\) SNPs and \(N\) samples, and a
block size of \(B\) SNPs is used with \(R\) ridge parameters,
 then **regenie** needs to store roughly \(N\times M/B\times R\)
doubles per phenotype, which is 8Gb per phenotype when \(M=500,000,
N=400,000, B =1,000,R=5\) and 200Gb in total when \(P=25\).

However, the `--lowmem` option can be used to avoid that memory usage,
at negligible extra computational cost, by writing temporary files to disk.

### Threading

**regenie** can take advantage of multiple cores using threading. The
number of threads can be specified using the `--threads` option.

**regenie** uses the [Eigen library](http://eigen.tuxfamily.org/index.php?title=Main_Page) for 
efficient linear algebra operations and this uses threading where possible.

For PLINK bed/bim/fam files, PLINK2 pgen/pvar/psam files, as well as BGEN v1.2 files with 8-bit encoding (format used for UK Biobank
500K imputed data), step 2 of **regenie** has been optimized by 
using multithreading through [OpenMP](https://www.openmp.org).



