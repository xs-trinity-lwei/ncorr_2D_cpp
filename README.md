# ncorr_2D_cpp

This is the offical repo for the complete C++ port of:

```
Ncorr: open-source 2D digital image correlation matlab software
J Blaber, B Adair, A Antoniou
Experimental Mechanics 55 (6), 1105-1122
```

Please cite this paper if you use this software in your research.

Future plans for this code are to write a real command-line executable to help automation and to containerize the code with Docker. Stay tuned!

## Build on VS2019 + MSVC 2017 toolset (v141, x64)

This project uses CMake and supports Visual Studio 2019 while targeting the MSVC 2017 compiler toolset (`v141`) on x64.

### 1) Install dependencies

Recommended approach is `vcpkg`:

```powershell
vcpkg install opencv fftw3 suitesparse --triplet x64-windows
```

Required libraries used by this project:

- OpenCV (`core`, `imgproc`, `highgui`, `imgcodecs`)
- FFTW3
- SuiteSparse (SPQR/CHOLMOD/AMD/COLAMD/suitesparseconfig)

### 2) Configure with Visual Studio 2019 + v141 toolset

```powershell
cmake -S build -B build_vs2019_v141 ^
  -G "Visual Studio 16 2019" -A x64 -T v141 ^
  -DCMAKE_TOOLCHAIN_FILE=<path-to-vcpkg>/scripts/buildsystems/vcpkg.cmake
```

> If you prefer, you can also set `-DCMAKE_GENERATOR_TOOLSET=v141` instead of `-T v141`.

### 3) Build

```powershell
cmake --build build_vs2019_v141 --config Release
```

Built static library output is placed in `lib/`.
