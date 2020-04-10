**3DiVi Face SDK** is a set of software components (code libraries) for building face recognition solutions of any complexity – from a simple app to portals like Azure Face API or AWS Rekognition.

## Features

* detecting, tracking and recognizing faces in video streams (see [Video Stream Processing](doc/development/video_stream_processing.md));
* detecting and tracking faces on images and video (see [Face Capturing](doc/development/face_capturing.md));
* recongizing faces on images and video. You can use different recognition methods (algorithms) depending on your use case (see [Face Identification](doc/development/face_identification.md));
* face estimation (gender, age, emotions, liveness) (see [Face Estimation](doc/development/face_estimation.md)).

See the detailed info about Face SDK components in [Components](doc/components.md).  
Read the [**documentation**](doc) to learn how to integrate Face SDK, see the examples and tutorials. 

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

3DiVi Face SDK uses the following open source libraries:

* [OpenSSL](doc/open_source_licenses/openssl.txt) (https://www.openssl.org)
* [Crypto++](doc/open_source_licenses/crypto%2B%2B.txt) (https://www.cryptopp.com)
* [Boost](doc/open_source_licenses/boost.txt) (http://www.boost.org)
* [OpenCV](doc/open_source_licenses/opencv.txt) (http://opencv.org)
