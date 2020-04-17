# Overview

**3DiVi Face SDK** is a set of software components (code libraries) for building face recognition solutions of any complexity – from a simple app to portals like Azure Face API or AWS Rekognition.

## Features

* detecting, tracking and recognizing faces in video streams (see [Video Stream Processing](doc/development/video_stream_processing.md));
* detecting and tracking faces on images and video (see [Face Capturing](doc/development/face_capturing.md));
* recongizing faces on images and video. You can use different recognition methods (algorithms) depending on your use case (see [Face Identification](doc/development/face_identification.md));
* face estimation (gender, age, emotions, liveness) (see [Face Estimation](doc/development/face_estimation.md)).

See the detailed info about Face SDK components in [Components](doc/components.md).  

## Supported platforms and API

Currently, the following platforms and architectures are supported:

* Windows (x86 32-bit, x86 64-bit)
* Linux (x86 32-bit, x86 64-bit, ARM 32-bit, ARM 64-bit)
* Android (ARM 32-bit, ARM 64-bit)
* iOS (ARM 64-bit)

Face SDK provides the following APIs:

* C++ API (for Windows, Linux, Android)
* Java wrapper (for Windows, Linux, Android)
* C# wrapper (for Windows, Linux)

To get started with Face SDK, download the [Face SDK distribution package](https://face.3divi.com/download_sdk). You can use a trial license, which is valid for 14 days and fully portable. 

## Documentation 

* [Face SDK Components](doc/components.md)
* [Use Cases](doc/use_cases.md)
* [Getting Started](doc/getting_started.md)
* [Demo Programs](doc/demo_programs)
* [Tutorials](doc/tutorials)
* Development
  * [Connecting Face SDK to Your Project](doc/development/connect_facesdk.md)
  * [Video Stream Processing](doc/development/video_stream_processing.md)
  * [Face Capturing](doc/development/face_capturing.md)
  * [Face Estimation](doc/development/face_estimation.md)
  * [Face Identification](doc/development/face_identification.md)
  * [Error Handling](doc/development/error_handling.md)
  * [Memory Management](doc/development/memory_management.md)
* [Performance Parameters](doc/performance_parameters.md)
* [Guidelines for Cameras](doc/guidelines_for_cameras.md)
* [Licenses](doc/licenses.md)
* [Face SDK Cross-Platform API. Latest Doxygen Output](http://download.3divi.com/facesdk/0d88ba7c-9a5d-45cd-897a-406fb1fca2d4/latest_docs/english/annotated.html)

## Open Source Licenses Used

* [OpenSSL](doc/open_source_licenses/openssl.txt) (https://www.openssl.org)
* [Crypto++](doc/open_source_licenses/crypto%2B%2B.txt) (https://www.cryptopp.com)
* [Boost](doc/open_source_licenses/boost.txt) (http://www.boost.org)
* [OpenCV](doc/open_source_licenses/opencv.txt) (http://opencv.org)
