# C++ Demo Programs

The *bin* directory contains the following executable files of the demo programs:

* `video_recognition_demo` – an example of using the `pbio::VideoWorker` object;
* `demo` – an example of face tracking and all types of face estimation;
* `test_calibration` – an example of camera calibration;
* `test_filecap` – an example of face capturing, saving and sample loading;
* `test_facecut` – an example of face capturing and cropping, as well as estimation of the face image quality, gender and age;
* `test_identify` – an example of creating, saving, loading and matching of templates;
* `test_videocap` – an example of face tracking and liveness estimation.

## demo

The program demonstrates tracking, detection and cropping of faces, detection of anthropometric points and angles, as well as estimation of face quality, age and gender, emotions, and liveness (by processing an RGB image from your camera).

To make a quick run of the demo with default parameters on Windows, go to the *bin* folder and double-left-click `demo.exe`.

<p align="center">
<img width="180" src="../img/cpp_demo_exe.png"><br>
<b>Location of C++ demo</b>
</p>

In the upper left corner of the demo you can see the Face SDK components, which can be turned on and off with the left mouse click:

* **rectangles** – face rectangle;
* **angles** – angles;
* **quality** – image quality;
* **liveness** – liveness;
* **age and gender** – age and gender;
* **base cut, full cut, token cut** – type of face cropping (basic, full frontal, token frontal);
* **points** – anthropometric points;
* **face quality** – face quality;
* **angles vectors** – vector angles;
* **emotions** – emotions.

<p align="center">
<img width="600" src="../img/demo_cpp.png"><br>
<b>Running demo.exe</b>
</p>

Error messages (if any) are printed in the console.  

You can also run `demo.exe` specifying some parameters (for example, if you have an online license).  
Startup parameters:

* `capturer_conf` – path to the capturer config file (learn more about types of config files in [Capturer Class Reference](../development/face_capturing.md#capturer-class-reference));
* `config_dir` – path to the *conf/facerec* directory;
* `dll_path` – path to the `libfacerec.so` or `facerec.dll` library file;
* `license_dir` – path to the directory with a license; provide this parameter if you need to change a default directory license.

Source code: [demo.cpp](../../examples/cpp/demo/demo.cpp)

OpenCV library is required for build.

## video_recognition_demo

The program is an example of using `pbio::VideoWorker` and demonstrates face tracking and identification on several video streams.

To make a quick run of the demo with default parameters and check face recognition on Windows:

1. Create a database, which will be used for face recognition. To create a database, go to the *bin/base* folder and create a new folder, for example, *person0*. Copy a picture of a person to be used for recognition and create a file *name.txt*, which should contain a person's name.

<p align="center">
<img width="250" src="../img/video_rec_base_folder.png"><br>
<b>Content of bin/base/person0</b>
</p>

2. Go to the *bin* folder and run the script `demo_web_m8v_latest.bat`, `demo_web_m6v_latest.bat` or `demo_web_m7v_latest.bat` by double-left-click. You can use any above script to run this demo, the only difference is a method (8.7, 6.7 and 7.7, see the detailed information in [Face Identification](../development/face_identification.md)).

<p align="center">
<img width="200" src="../img/cpp_video_recognition.png"><br>
<b>Location of C++ video_recognition_demo</b>
</p>

3. Tracking and identification results are displayed in a window (one window per one source). Tracked faces are highlighted with a green circle. In the upper right corner of the window you can see the recognition results: a tracked face on the left and a face from the database and the name on the right.

<p align="center">
<img width="700" src="../img/video_rec_demo_result.png"><br>
<b>Running C++ video_recognition_demo</b>
</p>

You can also run `video_recognition_demo.exe` specifying some parameters (for example, if you have an online license).  
Startup parameters:

* at least one source, each source is a number (webcam number), or a text (the URL of a video stream or the path to a video file);
* and then, in any order, the following parameters:
    * `config_dir` – path to the *conf/facerec* directory;
    * `dll_path` – path to the `libfacerec.so` or `facerec.dll` library file;
    * `database_dir` – path to the directory with a database, in which a directory was created for each person containing his/her photos and a text file `name.txt` containing his/her name. An example of the database is stored in *bin/base*;
    * `frame_fps_limit` – FPS limit;
    * `fullscreen` – fullscreen mode;
    * `license_dir` – path to the directory with a license; provide this parameter if you need to change a default directory license;
    * `vw_config_file` – name of the `VideoWorker` config file;
    * `method_config` – name of the `Recognizer` config file;
    * `recognition_distance_threshold` – recognition distance threshold (float number).

Examples of startup scripts:

* Linux:
    * `demo_web_m6v_latest.sh` – with method 6.7 and webcam 0
    * `demo_web_m7v_latest.sh` – with method 7.7 and webcam 0
    * `demo_web_m8v_latest.sh` – with method 8.7 and webcam 0
* Windows:
    * `demo_web_m6v_latest.bat` – with method 6.7 and webcam 0
    * `demo_web_m7v_latest.bat` – with method 7.7 and webcam 0
    * `demo_web_m8v_latest.bat` – with method 8.7 and webcam 0

Error messages (if any) are printed in the console.

Source code: [examples/cpp/video_recognition_demo](../../examples/cpp/video_recognition_demo)

OpenCV and boost libraries are required for build.

## test_identify

The program demonstrates creation, saving, loading and matching of templates.

The program operates in three modes:

* **enrollment** – face detection in a group of images, creation of a template for each face, and saving the templates to a file.
* **identifying** – face detection in one image, template creation, and matching of the template with every template created in the enrollment mode and loaded from the file.
* **verification** – detection of faces in two images, creation of two templates and their matching.

Launch parameters in the **enrollment** mode:

* path to the `libfacerec.so` or `facerec.dll` library file;
* path to the *conf/facerec* directory;
* name of the `Recognizer` config file;
* enroll;
* path to the directory with images (e.g. *bin/set1*);
* path to a text file containing a list of image file names (e.g. `bin/set1/list.txt`);
* path for saving templates to a file.

Launch parameters in the **identification** mode:

* path to the `libfacerec.so` or `facerec.dll` library file;
* path to the *conf/facerec* directory;
* name of the `Recognizer` config file;
* identify;
* path to the image file;
* path to the file with templates that was created in the enrollment mode.

Launch parameters in the **verification** mode:

* path to the `libfacerec.so` or `facerec.dll` library file;
* path to the *conf/facerec* directory;
* name of the `Recognizer` config file;
* verify;
* path to the first image file;
* path to the second image file.

Example of launch from the *bin* directory in the identification mode:

* Linux: `./test_identify ../lib/libfacerec.so ../conf/facerec method7v7_recognizer.xml enroll set1 set1/list.txt templates.bin`
* Windows: `test_identify facerec.dll ../conf/facerec method7v7_recognizer.xml enroll set1 set1/list.txt templates.bin`

Example of launch from the *bin* directory in the enrollment mode:

* Linux: `./test_identify ../lib/libfacerec.so ../conf/facerec method7v7_recognizer.xml identify set2/01100.jpg templates.bin`
* Windows: `test_identify facerec.dll ../conf/facerec method7v7_recognizer.xml identify set2/01100.jpg templates.bin`

Example of launch from the *bin* directory in the verification mode:

* Linux: `./test_identify ../lib/libfacerec.so ../conf/facerec method7v7_recognizer.xml verify set1/01100.jpg set2/01100.jpg`
* Windows: `test_identify facerec.dll ../conf/facerec method7v7_recognizer.xml verify set1/01100.jpg set2/01100.jpg`

Work progress and matching results are displayed in the console.  
Error messages, if any, are also displayed in the console.  

Source code: [test_identify.cpp](../../examples/cpp/demo/test_identify/test_identify.cpp)

No additional libraries are required for build.
