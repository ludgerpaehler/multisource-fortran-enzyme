# Running Log

## Setup Instructions

Clone the Enzyme repository

```bash
git clone git@github.com:EnzymeAD/Enzyme.git
```

Clone Johannes Doerfert's custom LLVM branch with Enzyme's up-to-date OpenMP optimizations

```bash
git clone git@github.com:jdoerfert/llvm-project.git llvm-openmp-jdoerfert
cd llvm-openmp-jdoerfert
git checkout enzyme_support2
```

Clone the current state of the F18 compiler

```bash
git clone git@github.com:flang-compiler/f18-llvm-project.git
cd f18-llvm-project
git checkout fir-dev
```

## Build F18 against the custom branch of LLVM

For this we are essentially performing an [out-of-tree build of F18](https://github.com/flang-compiler/f18-llvm-project/blob/fir-dev/flang/README.md) against the custom LLVM version with the OpenMP optimizations. Begin by building the custom branch of LLVM 

#### Custom LLVM-MLIR Version

```bash
cd llvm-openmp-jdoerfert
```

Our build targets here are:

- clang (required by OpenMP)
- mlir (what F18 is built on)
- openmp
- lld (required for multi-source AD at link-time)

```bash
mkdir build && cd build
cmake -G Ninja ../llvm -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD=X86 -DLLVM_ENABLE_PROJECTS="clang;mlir;openmp;lld" -DCMAKE_CXX_STANDARD=17 -DLLVM_BUILD_UTILS=On -DLLVM_INSTALL_UTILS=On -DLLVM_ENABLE_PLUGINS=On
ninja
```

#### F18 built against Custom LLVM Branch





#### Build Enzyme against Custom LLVM Branch

For Enzyme we can follow the usual Enzyme [installation workflow](https://enzyme.mit.edu/Installation/):

```bash
cd /path/to/enzyme_dir
mkdir build && cd build
cmake -G Ninja ../enzyme -DLLVM_DIR=/path/to/llvm/lib/cmake/llvm
```

The static libraries then reside in `/build/Enzyme`. The 3 libraries one should see are:

1. `ClangEnzyme-15.so`
2. `LLDEnzyme-15.so`
3. `LLVMEnzyme-15.so`

