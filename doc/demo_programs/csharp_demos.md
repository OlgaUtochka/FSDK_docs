# C# Demo Programs

The following dependencies are required to build and run C# demos:

*  [.NET Core Platform](https://docs.microsoft.com/en-us/dotnet/core/get-started) (recommended version: 2.1)
*  [.NET Wrapper for OpenCV](https://github.com/shimat/opencvsharp) (recommended version: 3.4.1)
*  Manual build of OpenCvSharp for Linux is required.

To build the demo project in VisualStudio:

* Create an empty C# console application;
* Add the source files of the demo to the project;
* Add links to `FacerecCSharpWrapper.dll` and [OpenCvSharp3-AnyCPU](https://www.nuget.org/packages/OpenCvSharp3-AnyCPU).

## demo

The program demonstrates tracking, detection and cropping of faces, detection of anthropometric points and angles, as well as estimation of face quality, age and gender, emotions, and liveness (by processing an RGB image from your camera).

To make a quick run of the demo with default parameters on Windows, go to the *bin/csharp_demo/demo* folder and double-left-click `run.bat`.

<p align="center">
<img width="350" src="../img/demo_cs_bat.png"><br>
<b>Location of C# demo</b>
</p>

Tracking results and information about each face are displayed in a window. In the upper left corner you can see the Face SDK components, which you can turn on and off by left mouse click. This demo program is similar to **C++ demo**, see the detailed description of the components in [C++ demo](cpp_demos.md#demo).

<p align="center">
<img width="600" src="../img/demo_cs.png"><br>
<b>Running C# demo</b>
</p>

To run the application on Linux, go to the *bin/csharp_demos/video_recognition_demo* directory and execute the command `run.sh <path_to_opencv_csharp>`, where `<path_to_opencv_csharp>` is the path to the directory with the OpenCvCsharp library.

You can also run C# demo specifying some parameters (for example, the path to your online license).

To build the demo, run the following commands:
```
cd examples/csharp_demos/demo
dotnet publish -o publish
```

Startup parameters:
```[--config_dir=<config_dir>] [--license_dir=<license_dir>] [--capturer_config=<capturer_config>]```

Where:

* `config_dir` – path to the *conf/facerec* directory;
* `capturer_conf` – path to the `Capturer` config file (learn more about types of config files in [Capturer Class Reference](../development/face_capturing.md#capturer-class-reference))
* `license_dir` – path to the directory with a license; provide this parameter if you need to change a default directory *license*;

**To run the demo on Windows:**  
Add the path to the directory that includes `facerec.dll` to the `PATH` environment variable.
```
set PATH=%PATH%; ..\...\..\bin
dotnet publish\csharp_demo.dll --config_dir=../../../conf/facerec --capturer_config=common_video_capturer_lbf.xml
```
**To run the demo on Linux:**  
Add the path to the directory that includes `libfacerec.so` and path to the directory with the OpenCVSharp library built for Linux to the `LD_LIBRARY_PATH` environment variable.  
```
LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:../../../lib:/path/to/opencvsharp/native/libs dotnet publish/csharp_demo.dll --config_dir=../../../conf/facerec --capturer_config=common_video_capturer_lbf.xml
```

Error messages (if any) are printed in the console.

Source code: [demo.cs](../../examples/csharp/demo/demo.cs)

## video_recognition_demo

Program demonstrates tracking, detection and recognition of faces from the database.

To make a quick run of the demo with default parameters and check face recognition on Windows:

1. Create a database, which will be used for face recognition (see p.1 of the section [C++ Video Recognition Demo](cpp_demos.md#video_recognition_demo)).
2. To run the demo, go to *bin/csharp_demo/video_recognition_demo* and double-left-click `run.bat`.

<p align="center">
<img width="450" src="../img/video_rec_cs_bat.png"><br>
<b>Location of C# video_recognition_demo</b>
</p>

3. Tracking and identification results are displayed in a window (one window per one source). Tracked faces are highlighted with a green circle. In the upper right corner of the window you can see the recognition results: a tracked face on the left and a face from the database and the name on the right.

<p align="center">
<img width="750" src="../img/video_rec_cs.png"><br>
<b>Running C# video_recognition_demo</b>
</p>

To run the application on Linux, go to the *bin/csharp_demos/video_recognition_demo* directory and execute the command `run.sh <path_to_opencv_csharp>`, where `<path_to_opencv_csharp>` is the path to the directory with the OpenCvCsharp library.

You can also run C# video_recognition_demo specifying some parameters.

To build the demo, run the following commands:
```
cd examples/csharp_demos/video_recognition_demo
dotnet publish -o publish
```

To run the demo, specify the path to the `csharp_video_recognition_demo.dll` library, startup options (optional) and a video source:
```
dotnet csharp_video_recognition_demo.dll [OPTIONS] <video_source>...
```

Startup examples:

* Webcam: `dotnet csharp_video_recognition_demo.dll –config_dir ../../../conf/facerec 0`
* RTSP stream: `dotnet csharp_video_recognition_demo.dll –config_dir ../../../conf/facerec rtsp://localhost:8554/`

Startup parameters:
```
[--config_dir=<config_dir>] [--license_dir=<license_dir>] [--database_dir=<database_dir>] [--method_config=<method_config>] [--recognition_distance_threshold=<recognition_distance_threshold>] [--frame_fps_limit=<frame_fps_limit>] (<camera_id> | <rtsp_url>)...
```

Where:

* `config_dir` – path to the *conf/facerec* directory;
* `license_dir` – path to the directory with a license; provide this parameter if you need to change a default directory license;
* `database_dir` – path to the directory with the database;
* `method_config` – name of the `Recognizer` configuration file;
* `recognition_distance_threshold` – recognition distance threshold (float number);
* `camera_id | rtsp_url` – one or several sources, each source is a number (webcam ID) or a string (video stream URL or path to the video file);
* `frame_fps_limit` – FPS limit.

**To run the demo on Windows:**  
Add the path to the directory that includes `facerec.dll` to the `PATH` environment variable.
```
set PATH=%PATH%;..\...\..\bin
dotnet publish/csharp_video_recognition_demo.dll --config_dir=../../../conf/facerec --database_dir=../../../bin/base --method_config=method6v7_recognizer.xml --recognition_distance_threshold=7000 --frame_fps_limit=25 0
```

**To run the demo on Linux:**  
Add the path to the directory that includes `libfacerec.so` and path to the directory with the OpenCVSharp library built for Linux to the `LD_LIBRARY_PATH` environment variable.
```
LD_LIBRARY_PATH=${LD_LIBRARY_PATH}: ../../../lib:~/opencv/lib/x86_64-linux-gnu dotnet publish/csharp_demo.dll --config_dir=../../../conf/facerec --database_dir=../../../bin/base --method_config=method6v7_recognizer.xml --recognition_distance_threshold=7000 --frame_fps_limit=25 0
```
Tracking and identification results are displayed in windows (one window per one video source).

Source code: [examples/csharp/video_recognition_demo](../../examples/csharp/video_recognition_demo)
