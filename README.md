# CMake DLL and Thread Demo

A small C++17 project that builds an executable and a shared library with CMake.
On Windows the shared library is a DLL; on Linux it is a `.so`. The executable
starts a `std::thread` that calls an exported function from the library.

## Build on Linux

```sh
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --parallel
./build/bin/ThreadDemo
```

## Build on Windows

Run these commands from a Visual Studio Developer PowerShell or a terminal with
the MSVC compiler available:

```powershell
cmake -S . -B build
cmake --build build --config Release
.\build\bin\Release\ThreadDemo.exe
```

The executable and DLL are emitted to the same runtime output directory, so the
Windows loader can find the DLL when starting the executable. The `.lib` import
library is used at link time.

## Build with Docker

Linux container (run on a Linux host, or Docker Desktop in Linux containers mode):

```sh
docker build -f docker/linux/Dockerfile -t cmake-thread-demo:linux .
docker run --rm cmake-thread-demo:linux
```

Windows container (run Docker Desktop on Windows and switch to Windows
containers mode):

```powershell
docker build -f docker/windows/Dockerfile -t cmake-thread-demo:windows .
docker run --rm cmake-thread-demo:windows
```

The Windows image installs Visual Studio C++ Build Tools and CMake, so the build
downloads and image creation can take a while. The sample uses the Windows
Server Core LTSC 2022 image; Windows container compatibility depends on the host
Windows version and isolation mode.

Containers share the host OS kernel; an image supplies user-space files and
programs, not a separate kernel. Windows binaries rely on Windows kernel APIs,
so a Windows base image alone cannot make them run on a Linux Docker host. The
Docker daemon must use a Windows kernel to build and run Windows containers.
Linux containers build Linux binaries, and Windows containers build Windows
binaries; the two images cannot be interchanged.

## What to look at

- `src/my_library.h` declares the exported API. The `MYDLL_EXPORTS`
  definition is private to the library target, selecting `dllexport` while the
  library is built and `dllimport` for Windows consumers.
- `src/my_library.cpp` implements the library function.
- `src/main.cpp` links the library and runs the function on a worker thread.
- `CMakeLists.txt` discovers the platform thread library and places runtime
  artifacts together.