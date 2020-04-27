<p align="center">
<img width="500" src="doc/img/3divi_logo.png"><br>
</p>

# Overview

**3DiVi Face SDK** is a set of software components (code libraries) for building face recognition solutions of any complexity – from a simple app to portals like Azure Face API or AWS Rekognition.

## Features

* [Face Capturing](doc/development/face_capturing.md) – face detection and tracking on images and video;
* [Face Identification](doc/development/face_identification.md) – face recognition on images and video;
* [Face Estimation](doc/development/face_estimation.md)  – face estimation (gender, age, emotions, liveness) on images and video;
* [Video Stream Processing](doc/development/video_stream_processing.md)  – face detection, tracking and recognition in video streams.

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

## Getting Started with Trial 

To get started with Face SDK, you need to download [Face SDK Trial](https://face.3divi.com/products/face_sdk). The trial license is valid for 14 days and fully portable. The trial version has the following limitations:
* runtime limit (3 minutes);
* number of each component (5);
* maximum size of the search index (1000 images).

To remove these limitations, you have to purchase Face SDK license. You can license any set of [Face SDK Components](doc/components.md) depending on your use case. We offer several types of licences:
* [Standard](doc/licenses.md#standard-licenses) – a licence that can be used offline after it's signed;
* [Online](doc/licenses.md#online-licenses) – a license that is regularly updated in the background;
* Enterprise Specific
* [Mobile app](doc/licenses.md#licenses-for-mobile-apps) – a license is bound to your iOS or Android app;
* [Hardware key](doc/licenses.md#hardware-key) – a device that is used instead of a standard license file.

See detailed information on license activation in the section [Face SDK Licensing](doc/licenses.md). Contact us at face@3divi.com if you have any questions.

Extract the distribution package to any directory on your device. The archive should contain:

* directories: *conf, docs, examples, include, share*
* archives: `windows_x86_64.zip`, `windows_x86_32.zip`, `linux_x86_64.tar.xz`, `linux_x86_32.tar.xz`, `linux_armhf_32.tar.xz`, `linux_aarch64.tar.xz`, `android_arm_32.tar.xz`, `android_arm_64.tar.xz`, `ios_arm_64.tar.xz`
* file *CHANGES*

When extracting the archive, specify the path to Face SDK root – the extracted folders *bin* and *lib* should be located at the same level with the *conf, docs, examples, include,* and *share* folders.

<p align="center">
<img width="700" src="doc/img/cpp_extract_OS.png"><br>
<b>Extraction path – root folder of the Face SDK distribution</b><br>
</p>

After unpacking the archive, you can familiarize yourself with the Face SDK features using the [trial license](licenses.md) and [demos](demo_programs) that demonstrate how to work with with C ++, Java, and C # API. 

## Introduction to Face SDK

* [Components](doc/components.md) – essential information about the components included in Face SDK
* [Use Cases](doc/use_cases.md) – diagrams that show possible use cases and connection between the Face SDK components
* [Licenses](doc/licenses.md) – activation of different license types, licensing of mobile apps 
* [Demo Programs](doc/demo_programs) – sample programs in C++/C#/Java that show you the features of Face SDK
* [Tutorials](doc/tutorials) – step-by-step tutorials on face detection, recognition, and estimation of age, gender, and emotions

## Documentation 

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
* [Face SDK Cross-Platform API. Latest Doxygen Output](http://download.3divi.com/facesdk/0d88ba7c-9a5d-45cd-897a-406fb1fca2d4/latest_docs/english/annotated.html) 

## Open Source Licenses

* [OpenSSL](doc/open_source_licenses/openssl.txt) (https://www.openssl.org)
* [Crypto++](doc/open_source_licenses/crypto%2B%2B.txt) (https://www.cryptopp.com)
* [Boost](doc/open_source_licenses/boost.txt) (http://www.boost.org)
* [OpenCV](doc/open_source_licenses/opencv.txt) (http://opencv.org)
