# cmake_demo_single_file
Really simple CMake demo

If you have a recent version of CMake (3.15 or better) under linux, macOS or freeBSD,  you can simply
go in the directory and type the following commands:

```
cmake -B build .
cmake --build build
./build/repro
./build/reproc
```

If you are using Visual Studio, the instructions are nearly the same:


```
cmake -B build .
cmake --build build --config Release
./build/Release/repro
./build/Release/reproc
```
