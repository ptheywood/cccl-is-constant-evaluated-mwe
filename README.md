# cccl-is-constant-evaluated-mwe

Repo using CI to demonstrate a visual studio 2019 compilation error when using CCCL / libcudacxx with some CUDA versions

libcudacxx within CCCL encounteres compilation errors using visual studio 2019 as the host compiler, with certain CUDA versions, in CCCL v2.2.0 and potentially others (to be confirmed via CI in this repo).

CCCL (at the time of writing) supports CUDA 11.1+, and the same host compilers as CUDA (i.e. 2019 and 2022 at the time of writing).

## Reproducing

1. Clone nvidia/CCCL
2. Navigate to `examples/example_project` (to avoid an llvm dependency)
3. Configure CMake
4. Build

For Visual Studio 2019 with CUDA 11.2 for example, this will result in a compilation error:

```console
C:\Users\ptheywood\code\cccl\examples\example_project\build\_deps\cccl-src\libcudacxx\include\cuda\std\detail\libcxx\in
clude\__type_traits/is_constant_evaluated.h(31): error : identifier "__builtin_is_constant_evaluated" is undefined [C:\
Users\ptheywood\code\cccl\examples\example_project\build\example_project.vcxproj]

C:\Users\ptheywood\code\cccl\examples\example_project\build\_deps\cccl-src\libcudacxx\include\cuda\std\detail\libcxx\in
clude\__type_traits/is_constant_evaluated.h(36): error : identifier "__builtin_is_constant_evaluated" is undefined [C:\
Users\ptheywood\code\cccl\examples\example_project\build\example_project.vcxproj]
```

## CI

This repsitory contains a CI workflow, `.github/workflows/demo.yml` which demonstrates this problem using a matrix of visual studio, CUDA and CCCL versions.

See the latest actions run for the matrix showing which versions do and do not compile.