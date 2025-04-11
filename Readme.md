# CMake+Swift C module dependency virality bug repro

As soon as a CMake project declares a Swift module consuming a C module that has a module map, all upstream Swift modules need to declare a direct dependency on the C module.

## Steps

Check out this repo and run:

```
cmake -S . -B build -G Ninja
ninja -C build Three
```

## Actual

```
ninja: Entering directory `build'
[3/4] Building Swift Module 'Three' with 1 source
FAILED: Three.swiftmodule CMakeFiles/Three.dir/Three.swift.obj
C:\Users\micro\AppData\Local\Programs\Swift\Toolchains\0.0.0+Asserts\usr\bin\swiftc.exe -j 20 -num-threads 20 -c  -parse-as-library -static -emit-module -emit-module-path Three.swiftmodule -module-name Three -module-link-name Three -libc MD -incremental -output-file-map CMakeFiles\Three.dir\\output-file-map.json -I C:/Users/micro/Desktop/Virality/build C:/Users/micro/Desktop/Virality/Three.swift
error: emit-module command failed with exit code 1 (use -v to see invocation)
C:\Users\micro\Desktop\Virality\Three.swift:1:8: error: missing required module 'One'
1 | import Two
  |        `- error: missing required module 'One'
2 |
3 | public func three() -> Int { two() + 1 }
ninja: build stopped: subcommand failed.
```

## Expected

Module Three should build without requiring a direct dependency on One.

## Workaround

```
target_link_libraries(Three PRIVATE One)
```

or

```
target_include_directories(Three PRIVATE include)
```

But either of these need to be applied to every upstream target that transitively takes a dependency on Two.
