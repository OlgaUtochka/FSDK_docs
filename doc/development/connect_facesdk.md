# Connecting Face SDK to Your Project 

## Adding Library to the Project

Required dependencies

To use Face SDK, you have to install the following dependencies:

* [Windows] `sense4.dll`. This library is located in the *bin* folder in Face SDK and should be located next to `facerec.dll` if you change the default location.
* [Linux] `libusb-0.1.so.4`

### C++

The `libfacerec` library is loaded dynamically **in runtime mode** when the static method `pbio::FacerecService::createService` is called. Therefore, **do not** link your program with `libfacerec.so` (Linux) or `facerec.dll` (Windows). You only need to add the *include* directory to include directories, or, for Linux projects, to link the `libdl` library with the `-export-dynamic` flag.

_**Warning:** You can call `pbio::FacerecService::createService` only once, otherwise, you may encounter a crash._

All *libfacerec* declarations are divided in two headers:

* [`facerec/import.h`](../../include/facerec/import.h) – should be included only once (for example, in `main.cpp`).
* [`facerec/libfacerec.h`](../../include/facerec/libfacerec.h) – should be included in each file that uses library.

The *libfacerec* library does not have any actual third-party dependencies, since all used third-party libraries (see [Open Source Licenses](../open_source_licenses)) are linked statically. Therefore, any version of these libraries should work for examples, in which OpenCV or boost is used.

#### Library Version

Following preprocessor constants specify the current library version:

* `LIBFACEREC_VERSION` – string representation, e.g. "1.2.34", where 1 is the major version, 2 is the minor version, 34 is the patch version
* `LIBFACEREC_VERSION_HEX` – 3-byte HEX representation that is useful in numeric comparisons, e.g. 0x010234

### Java

To use `facerec.jar` in a project, specify the path to this file in `classpath`. The `facerec.jar` Java library is a wrapper for C++ library, so `libfacerec_jni.so` and `libfacerec.so` for Linux and Android or `facerec_jni.dll` and `facerec.dll` for Windows are required in runtime. Path to the directory containing `lifacerec_jni.so` or `facerec_jni.dll` should be specified in `java.library.path`.

### C#

To use *facerec* in project, add reference to `FacerecCSharpWrapper.dll` in project. Add `facerec.so` (for Linux) or `facerec.dll` (for Windows) and `FacerecCSharpWrapper.dll` to folder with executable file.
