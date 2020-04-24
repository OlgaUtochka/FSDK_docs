<p align="center">
<img width="500" src="doc/img/3divi_logo.png"><br>
</p>

# Overview

**3DiVi Face SDK** is a set of software components (code libraries) for building face recognition solutions of any complexity – from a simple app to portals like Azure Face API or AWS Rekognition.  Example of page edit.

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

To get started with Face SDK, download the [Face SDK distribution package](https://face.3divi.com/download_sdk).   
You can use [a trial license](https://face.3divi.com/products/face_sdk/face_sdk_trial), which is valid for 14 days and fully portable. 

## Documentation 

* [Face SDK Components](doc/components.md) – essential information about the components included in Face SDK
* [Use Cases](doc/use_cases.md) – diagrams that show possible use cases and connection between the Face SDK components
* [Getting Started](doc/getting_started.md) – learn how to get started with Face SDK
* [Demo Programs](doc/demo_programs) – sample programs in C++/C#/Java that show you the features of Face SDK
* [Tutorials](doc/tutorials) – step-by-step tutorials on face detection, recognition, and estimation of age, gender, and emotions
* Development – all you need to know to develop your project with Face SDK
  * [Connecting Face SDK to Your Project](doc/development/connect_facesdk.md) – learn how to add and use the libfacerec library in your project
  * [Video Stream Processing](doc/development/video_stream_processing.md) – face tracking, creation of templates, face recognition, estimation of age, gender, and emotions, short-time identification
  * [Face Capturing](doc/development/face_capturing.md) – custom face tracking, getting information about faces, anthropometric points, face cropping 
  * [Face Estimation](doc/development/face_estimation.md) – custom estimation of age, gender, emotion, and liveness (2D/3D)
  * [Face Identification](doc/development/face_identification.md) – custom face identification, identification methods
  * [Error Handling](doc/development/error_handling.md) – error handling in C++/C#/Java
  * [Memory Management](doc/development/memory_management.md) – memory management in C++/C#/Java
* [Performance Parameters](doc/performance_parameters.md) – performance of Face SDK based on several tests  
* [Guidelines for Cameras](doc/guidelines_for_cameras.md) – camera positioning and shooting, recommended cameras
* [Licenses](doc/licenses.md) – registration of different license types, licensing of mobile apps 
* [Face SDK Cross-Platform API. Latest Doxygen Output](http://download.3divi.com/facesdk/0d88ba7c-9a5d-45cd-897a-406fb1fca2d4/latest_docs/english/annotated.html) 

## Open Source Licenses Used

* [OpenSSL](doc/open_source_licenses/openssl.txt) (https://www.openssl.org)
* [Crypto++](doc/open_source_licenses/crypto%2B%2B.txt) (https://www.cryptopp.com)
* [Boost](doc/open_source_licenses/boost.txt) (http://www.boost.org)
* [OpenCV](doc/open_source_licenses/opencv.txt) (http://opencv.org)
