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


