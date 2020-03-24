**3DiVi Face SDK** is a set of software components (code libraries) for building face recognition solutions of any complexity – from a simple app to portals like Azure Face API or AWS Rekognition.

## Features

* detecting, tracking and recognizing faces in video streams (see [Video Stream Processing](docs/development/video_stream_processing.md));
* detecting and tracking faces on images and video (see [Face Capturing](docs/development/face_capturing.md));
* recongizing faces on images and video. You can use different recognition methods (algorithms) depending on your use case (see [Face Identification](docs/development/face_identification.md));
* face estimation (gender, age, emotions, liveness) (see [Face Estimation](docs/development/face_estimation.md)).

See the detailed info about Face SDK components in [Components](docs/components.md).

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

* OpenSSL (https://www.openssl.org)
* Crypto++ (https://www.cryptopp.com)
* Boost (http://www.boost.org)
* OpenCV (http://opencv.org)
