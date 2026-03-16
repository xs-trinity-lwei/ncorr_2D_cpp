# Build ncorr on VS2019 + MSVC v141 (x64)

This guide is isolated in a new file to avoid merge conflicts with `README.md`.

## Dependencies (vcpkg)

```powershell
vcpkg install opencv fftw3 suitesparse --triplet x64-windows
```

Required packages:

- OpenCV (`core`, `imgproc`, `highgui`, `imgcodecs`)
- FFTW3
- SuiteSparse (`spqr`, `cholmod`, `amd`, `colamd`, `suitesparseconfig`)

## Configure

```powershell
cmake -S build -B build_vs2019_v141 ^
  -G "Visual Studio 16 2019" -A x64 -T v141 ^
  -DCMAKE_TOOLCHAIN_FILE=<path-to-vcpkg>/scripts/buildsystems/vcpkg.cmake
```

## Build

```powershell
cmake --build build_vs2019_v141 --config Release
```
