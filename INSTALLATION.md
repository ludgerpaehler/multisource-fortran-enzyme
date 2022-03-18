# Installation Instructions

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

### Custom LLVM-MLIR Version

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

### F18 built against Custom LLVM Branch

#### In-Tree Build

```bash
mkdir build && cd build
cmake -G Ninja ../llvm -DCMAKE_BUILD_TYPE=RelWithDebInfo -DLLVM_TARGETS_TO_BUILD=X86 -DLLVM_ENABLE_PROJECTS="clang;flang;mlir;openmp" -DCMAKE_CXX_STANDARD=17 -DLLVM_BUILD_TOOLS=On -DLLVM_INSTALL_UTILS=On -DLLVM_ENABLE_PLUGINS=On
```


#### Out-of-Tree Build

Addition of the custom LLVM installation to the Path environment

```bash
export PATH=/home/lpaehler/AutomaticDifferentiation/FlangTesting/llvm-openmp-jdoerfert/build/bin:$PATH
```

Create build space and prepare for installation of Flang/F18

```bash
mkdir build-flang && cd build-flang
cmake -G Ninja ../flang -DLLVM_DIR=~/Work/AutomaticDifferentiation/FlangTesting/llvm-openmp-jdoerfert/build/lib/cmake/llvm -DCMAKE_BUILD_TYPE=RelWithDebInfo -DLLVM_TARGETS_TO_BUILD=X86 -DCLANG_DIR=~/Work/AutomaticDifferentiation/FlangTesting/llvm-openmp-jdoerfert/build/lib/cmake/clang
ninja
```

Currently fails with

```bash
/home/lpaehler/Work/AutomaticDifferentiation/FlangTesting/f18-llvm-project/flang/include/flang/Optimizer/Transforms/Passes.td:19:30: error: Couldn't find class 'FunctionPass'
def AffineDialectPromotion : FunctionPass<"promote-to-affine">
```

After previously encountering this warning in the configuration step of ninja

```bash
CMake Warning (dev) at <...>/lib/cmake/llvm/TableGen.cmake:99 (add custom command):
    Policy CMP0116 is not set: Ninja generators transform DEPFILEs from
    add_custom_command(). Run "cmake --help-policy CMP0116" for policy
    details. Use the cmake_policy command to set the policy and suppress this
    warning.
Call Stack (most recent call first):
    /home/lpaehler/Work/AutomaticDifferentiation/FlangTesting/llvm-openmp-jdoerfert/build/lib/cmake/mlir/AddMLIR.cmake:5 (tablegen)
    include/flang/Optimizer/CodeGen/CMakeLists.txt:3 (mlir_tablegen)
```

The error returned when using the same command without Ninja, but with make is

```bash
/home/lpaehler/Work/AutomaticDifferentiation/FlangTesting/f18-llvm-project/flang/include/flang/Optimizer/Dialect/FIROps.td:22:36: error: Couldn't find class 'OpTrait'
class fir_Op<string mnemonic, list<OpTrait> traits>

/home/lpaehler/Work/AutomaticDifferentiation/FlangTesting/f18-llvm-project/flang/include/flang/Optimizer/Dialect/FIROps.td:22:43: error: unknown class name
class fir_Op<string mnemonic, list<OpTrait> traits>
```

> Investigate what is happening there inside of this class!!!

### Build Enzyme against Custom LLVM Branch

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
> Do we need to build with `-DENZYME_FLANG=ON`?

