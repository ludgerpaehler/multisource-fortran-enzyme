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

```bash
cd llvm-openmp-jdoerfert
```

Our build targets here are:

- clang (required by OpenMP)
- mlir (what F18 is built on)
- openmp

```bash
mkdir build && cd build
cmake -G Ninja ../llvm -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD=X86 -DLLVM_ENABLE_PROJECTS="clang;mlir;openmp" -DCMAKE_CXX_STANDARD=17 -DLLVM_BUILD_UTILS=On -DLLVM_INSTALL_UTILS=On -DLLVM_ENABLE_PLUGINS=On
ninja
```


