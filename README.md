# cccl-is-constant-evaluated-mwe-vs2019

libcudacxx within [nvidia/CCCL](https://github.com/nvidia/cccl) encounters the following compilation error with the Visual Studio 2019 and CUDA <= 11.2.

```
error : identifier "__builtin_is_constant_evaluated" is undefined
```

This repository uses GitHub Actions `windows-2019` runners to reproduce this issue across each CUDA 11.x minor release, reproducing the issue with `MSVC 19.29.30153.0` (the version on github actions on 2023-12-05), using CCCL's current `main` (9eca399e5c37b462749ae0ba1b9ffd108191969f).

CCCL is documented as being [backwards compatible with current and older major CUDA versions](https://github.com/nvidia/cccl?tab=readme-ov-file#cuda-toolkit-ctk-compatibility) (i.e. 11 and 12 as of 2023-12) and as supporting the same (host compilers as the cuda toolkit)[https://github.com/nvidia/cccl?tab=readme-ov-file#host-compilers]

I.e. Visual Studio 2019 should be supported with CUDA 11.1+ (11.0 is excluded due to compiler issues).

This demonstration does not specify a c++ standard for CUDA nor C++, however this was discovered in a separate repository which sets c++17 as the standard, the latest compatible with CUDA 11.x.

## Reproducing

1. With Visual Studio 2019 and CUDA <= 11.2 installed
2. Clone nvidia/CCCL
3. Navigate to `examples/example_project` (to avoid an llvm dependency)
4. `cmake -B build -S .`
5. `cmake --build build`

For Visual Studio 2019 with CUDA 11.1 and 11.2 (and 11.0 but that's not a supported target) for example, this will result in a compilation error, e.g:

```console
D:\a\cccl-is-constant-evaluated-mwe\cccl-is-constant-evaluated-mwe\cccl\examples\example_project\build\_deps\cccl-src\libcudacxx\include\cuda\std\detail\libcxx\include\__type_traits/is_constant_evaluated.h(31): error : identifier "__builtin_is_constant_evaluated" is undefined [D:\a\cccl-is-constant-evaluated-mwe\cccl-is-constant-evaluated-mwe\cccl\examples\example_project\build\example_project.vcxproj]
```

## CI

This repository contains a CI workflow, `.github/workflows/demo.yml` which demonstrates this problem using a matrix of visual studio, CUDA and CCCL versions.

See the latest [actions run](https://github.com/ptheywood/cccl-is-constant-evaluated-mwe-vs2019/actions/workflows/demo.yml) for the matrix showing which versions do and do not compile.

For commit `7dae562` of this repository, ci demonstrated that:

| CCCL            | CUDA | Visual Studio | Compiled? |
|-----------------|------|---------------|-----------|
| main (9eca399e) | 11.0 | 19.29.30153.0 | **No**    |
| main (9eca399e) | 11.1 | 19.29.30153.0 | **No**    |
| main (9eca399e) | 11.2 | 19.29.30153.0 | **No**    |
| main (9eca399e) | 11.3 | 19.29.30153.0 | Yes       |
| main (9eca399e) | 11.4 | 19.29.30153.0 | Yes       |
| main (9eca399e) | 11.5 | 19.29.30153.0 | Yes       |
| main (9eca399e) | 11.6 | 19.29.30153.0 | Yes       |
| main (9eca399e) | 11.7 | 19.29.30153.0 | Yes       |
| main (9eca399e) | 11.8 | 19.29.30153.0 | Yes       |
