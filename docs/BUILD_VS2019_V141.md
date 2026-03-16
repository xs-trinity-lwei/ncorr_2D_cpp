# Build ncorr on VS2019 + MSVC v141 (x64), without vcpkg

This project can be built on Windows directly from repository-bundled dependencies.
No vcpkg is required.

## 1) Put dependencies into `third_party/`

Create this structure in the repository root:

```text
third_party/
  opencv/
    include/opencv2/...
    lib/              (or lib/x64)
  fftw3/
    include/fftw3.h
    lib/              (or lib/x64)
  suitesparse/
    include/SuiteSparseQR.hpp
    lib/              (or lib/x64)
```

Required libraries:

- OpenCV: either `opencv_world*.lib` **or** all of:
  - `opencv_core*.lib`
  - `opencv_imgproc*.lib`
  - `opencv_highgui*.lib`
  - `opencv_imgcodecs*.lib`
- FFTW3: `fftw3.lib` (or `libfftw3-3.lib` / `fftw3-3.lib`)
- SuiteSparse:
  - `spqr.lib`
  - `cholmod.lib`
  - `amd.lib`
  - `colamd.lib`
  - `suitesparseconfig.lib`

> Tip: keep include+lib files already built for x64 and toolset-compatible with MSVC v141.

## 2) Configure

```powershell
cmake -S build -B build_vs2019_v141 ^
  -G "Visual Studio 16 2019" -A x64 -T v141 ^
  -DCMAKE_BUILD_TYPE=Release
```

If `third_party` is in a custom location:

```powershell
cmake -S build -B build_vs2019_v141 ^
  -G "Visual Studio 16 2019" -A x64 -T v141 ^
  -DTHIRD_PARTY_DIR=D:/deps/ncorr_third_party
```

## 3) Build

```powershell
cmake --build build_vs2019_v141 --config Release
```
