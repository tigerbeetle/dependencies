TigerBeetle doesn't have dependencies and they are hidden in this repository.

Currently, this repository vendors a copy of llvm-objcopy for use during build process. There's no
easy way to install _just_ objcopy without pulling the entirety of LLVM. Versions for mac and windows
are pulled from https://github.com/llvm/llvm-project/releases/tag/llvmorg-18.1.8. Version for Linux
x86_64 is build with

```
docker run -it --name llvm_build ubuntu:noble-20240801 /bin/bash -c "
    apt-get update && apt-get install -y git cmake ninja-build clang
    git clone --depth 1 --branch release/18.x https://github.com/llvm/llvm-project.git
    mkdir llvm-project/build && cd llvm-project/build
    cmake -G Ninja -DLLVM_ENABLE_PROJECTS=clang -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD=X86 -DLLVM_BUILD_STATIC=ON ../llvm
    ninja llvm-objcopy
"

docker cp llvm_build:/llvm-project/build/bin/llvm-objcopy .
docker rm llvm_build
```

For Linux aarch64, the following is used:

```
docker run --platform=linux/arm64/v8 -it --name llvm_build ubuntu:noble-20240801 /bin/bash -c "
    apt-get update && apt-get install -y git cmake ninja-build clang
    git clone --depth 1 --branch release/18.x https://github.com/llvm/llvm-project.git
    mkdir llvm-project/build && cd llvm-project/build
    cmake -G Ninja -DLLVM_ENABLE_PROJECTS=clang -DCMAKE_BUILD_TYPE=Release -DLLVM_TARGETS_TO_BUILD=AArch64 -DLLVM_BUILD_STATIC=ON ../llvm
    ninja llvm-objcopy
"

docker cp llvm_build:/llvm-project/build/bin/llvm-objcopy .
docker rm llvm_build
```

From source build is used to make a static executable (`-DLLVM_BUILD_STATIC=ON`).

All four binaries are available as a GH release in this repository.
