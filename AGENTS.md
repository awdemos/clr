# AMD CLR Agent Guide

AMD CLR (Compute Language Runtimes) is a component of the HIP runtime containing `HIP` and `OpenCL™` implementations for AMD platforms.

## Repository Layout

- `hipamd/` — HIP language implementation on AMD GPUs.
- `opencl/` — OpenCL™ implementation on AMD GPUs.
- `rocclr/` — Shared compute runtime used by both HIP and OpenCL™.

## Build Commands

### HIP on Linux

```bash
export CLR_DIR="$(readlink -f .)"
export HIP_DIR="$(readlink -f ../hip)"  # or path to HIP common headers
mkdir -p build; cd build
cmake -DHIP_COMMON_DIR=$HIP_DIR \
      -DCMAKE_PREFIX_PATH="/opt/rocm/" \
      -DCMAKE_INSTALL_PREFIX=$PWD/install \
      -DCLR_BUILD_HIP=ON -DCLR_BUILD_OCL=OFF -DHIP_PLATFORM=amd ..
make -j$(nproc)
make install
```

### OpenCL on Linux

```bash
sudo apt-get install ocl-icd-opencl-dev
cmake -DCLR_BUILD_HIP=OFF -DCLR_BUILD_OCL=ON -DCMAKE_INSTALL_PREFIX=$PWD/install ..
make -j$(nproc)
make install
```

## Test Commands

CLR itself is mostly a runtime; run downstream HIP/OpenCL™ tests or the ROCm validation suite after install:

```bash
./install/bin/hipcc --version
```

## Lint / Code Style

- C/C++ sources follow LLVM/ROCm style; use `clang-format` with the project config if present.
- Python helper scripts require `CppHeaderParser` (`pip3 install CppHeaderParser`).
- Keep copyright headers intact; do not remove AMD license text.

## Key Conventions

- `HIP_PLATFORM=amd` selects the AMD path; `HIP_PLATFORM=nvidia` is supported for cross-platform development but is not the primary target here.
- Install layout mirrors ROCm: `bin/`, `lib/`, `include/`.
- `rocclr` headers are internal; public API lives in `hipamd/include/` and `opencl/include/`.

## Common Gotchas

- A full ROCm stack (especially `rocm-llvm-dev`) must be installed before building.
- Building inside the `rocm-systems` super-project is the recommended path; standalone builds need manually cloned `HIP` common headers.
- The README clone URL points to `ROCm/rocm-systems`; the actual CLR code is under `projects/clr` in that super-project.
