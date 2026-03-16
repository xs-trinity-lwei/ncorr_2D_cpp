# ncorr_2D_cpp

This is the offical repo for the complete C++ port of:

```
Ncorr: open-source 2D digital image correlation matlab software
J Blaber, B Adair, A Antoniou
Experimental Mechanics 55 (6), 1105-1122
```

Please cite this paper if you use this software in your research.

Future plans for this code are to write a real command-line executable to help automation and to containerize the code with Docker. Stay tuned!

## Build on MSVC 2017 (Visual Studio 15 2017)

This project uses CMake and can generate a native MSVC 2017 solution.

### 1) Install dependencies

Recommended approach is `vcpkg`:

```powershell
vcpkg install opencv fftw3 suitesparse --triplet x64-windows
```

Required libraries used by this project:

- OpenCV (`core`, `imgproc`, `highgui`, `imgcodecs`)
- FFTW3
- SuiteSparse (SPQR/CHOLMOD/AMD/COLAMD/suitesparseconfig)

### 2) Configure with Visual Studio 2017 generator

```powershell
cmake -S build -B build_msvc2017 ^
  -G "Visual Studio 15 2017 Win64" ^
  -DCMAKE_TOOLCHAIN_FILE=<path-to-vcpkg>/scripts/buildsystems/vcpkg.cmake
```

### 3) Build

```powershell
cmake --build build_msvc2017 --config Release
```

Built static library output is placed in `lib/`.
