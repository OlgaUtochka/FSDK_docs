# Liveness Detection with Face SDK

In this tutorial, you'll learn how to perform liveness detection in a video stream with Face SDK and an RGBD sensor. As a rule, liveness detection is used to prevent spoofing attacks (when a person tries to subvert or attack a face recognition system by using a picture or a video and thereby gaining illegitimate access).

With Face SDK, you can perform liveness detection by analyzing a depth map or an RGB image from your sensor. The first method is more accurate, that's why we'll consider it in this tutorial. 

This tutorial is based on [Face Recognition in a Video Stream](face_recognition_in_a_video_stream.md) and the corresponding project. In this project, we'll also use a ready-made database of faces for recognition. After you run the project, you'll see RGB and depth maps, which you can use to correct your position relative to the sensor: to ensure the stable performance of a liveness detector, your face should be at a suitable distance from a sensor, and the quality of the depth map should be sufficient. A detected and recognized face will be highlighted with a green rectangle on an RGB image. Next to the detected face, there will be a picture and a name of a person from the database. Also, you'll see the liveness status `REAL`. If a person isn't recognized, the liveness status will be `REAL` but the bounding rectangle will be red. If a detected face is taken from a picture or a video, the bounding rectangle will be red and recognition won't be performed. In this case, the liveness status will be `FAKE`. 

Besides Face SDK and Qt, you'll need:

* An RGBD sensor with OpenNI2 or RealSense2 support (for example, ASUS Xtion or RealSense D415);
* [OpenNI2](https://structure.io/openni) or [RealSense2](https://github.com/IntelRealSense/librealsense/releases) distribution package.

You can find the tutorial project in Face SDK: *examples/tutorials/depth_liveness_in_face_recognition*

<p align="center">
<img width="1000" src="../img/fifth_1.png"><br>
</p>

## Importing OpenNI2 and RealSense2 Libraries

<li> First of all, we have to import necessary libraries to work with the depth camera. You can use either an OpenNI2 sensor (for example, ASUS Xtion) or a RealSense2 sensor. Depending on the camera you're using, you have to specify the condition <i>WITH_OPENNI2=1</i> or <i>WITH_REALSENSE=1</i>.

\htmlonly <input class="toggle-box" id="fifth-1" type="checkbox" checked>
<label class="spoiler-link" for="fifth-1">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
WITH_OPENNI2=1
#WITH_REALSENSE=1

isEmpty(WITH_OPENNI2): isEmpty(WITH_REALSENSE) {
    error("OpenNI2 or RealSense support should be enabled")
}

!isEmpty(WITH_OPENNI2): !isEmpty(WITH_REALSENSE) {
    error("OpenNI2 and RealSense support can't be enabled simultaneously")
}
...
\endcode
</div>

<li> [For OpenNI2 sensors] Specify the path to the OpenNI2 distribution package and also the paths to the necessary OpenNI2 libraries and headers.

@note
For Windows, you have to install OpenNI2 and specify the path to the installation directory. For Linux, you just need to specify the path to the unpacked archive. 

\htmlonly <input class="toggle-box" id="fifth-2" type="checkbox" checked>
<label class="spoiler-link" for="fifth-2">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
!isEmpty(WITH_OPENNI2){

    OpenniDistr = 
    isEmpty(OpenniDistr) {
        error("Empty path to OpenNI2 directory")
    }

    LIBS += -L$$OpenniDistr/Redist/
    win32: LIBS += -L$$OpenniDistr/Lib/

    INCLUDEPATH += = $$OpenniDistr/Include/
    LIBS += -lOpenNI2

    DEFINES += WITH_OPENNI2
}
...
\endcode
</div>

<li> [For RealSense sensors] Specify the path to the RealSense2 distribution package and the paths to the necessary RealSense2 libraries and headers. In the <i>win32</i> block, we determine the platform bitness to set the correct paths to the RealSense libraries.

@note
For Windows, you have to install RealSense2 and specify the path to the installation directory. For Linux, you have to install RealSense2 as described at the [Intel RealSense website](https://github.com/IntelRealSense/librealsense/).

\htmlonly <input class="toggle-box" id="fifth-3" type="checkbox" checked>
<label class="spoiler-link" for="fifth-3">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
!isEmpty(WITH_REALSENSE){

    win32 {
        RealSenseDistr = /home/stranger/depth_liveness/librealsense/install
        isEmpty(RealSenseDistr) {
            error("Empty path to RealSense directory")
    }

        contains(QMAKE_TARGET.arch, x86_64) {
            LIBS += -L$$RealSenseDistr/lib/x64
            LIBS += -L$$RealSenseDistr/bin/x64
        } else {
            LIBS += -L$$RealSenseDistr/lib/x86
            LIBS += -L$$RealSenseDistr/bin/x86
        }

        INCLUDEPATH += $$RealSenseDistr/include/
    }

    LIBS += -lrealsense2

    DEFINES += WITH_REALSENSE
}
...
\endcode
</div>

</ol>

\subsection fifth_depth_map Retrieving a Depth Map using OpenNI2 API / RealSense2 API 

<ol>

<li> At this stage, we need to retrieve a depth frame from an RGBD sensor using OpenNI2 API or RealSense2 API, depending on the camera used. We won't elaborate on retrieving the depth frames. Instead, we'll use the headers from one of the Face SDK demo programs (\ref sec_video_recognition_demo). In the profile of the project, specify the path to the folder <i>examples/cpp/video_recognition_demo/src</i> from Face SDK.

\htmlonly <input class="toggle-box" id="fifth-4" type="checkbox" checked>
<label class="spoiler-link" for="fifth-4">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
INCLUDEPATH += $$FACE_SDK_PATH/examples/cpp/video_recognition_demo/src
...
\endcode
</div>

<li> Specify the necessary headers to work with OpenNI2 and RealSense2 cameras. You can find the detailed information about retreving the depth frames in the specified files (<i>OpenniSource.h</i> and <i>RealSenseSource.h</i>).

\htmlonly <input class="toggle-box" id="fifth-5" type="checkbox" checked>
<label class="spoiler-link" for="fifth-5">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
!isEmpty(WITH_OPENNI2){
    HEADERS += OpenniSource.h
}
else {
    HEADERS += RealSenseSource.h
}
...
\endcode
</div>

<li> To use mathematical constants, define <i>_USE_MATH_DEFINES</i> (<i>cmath</i> is already imported in <i>OpenniSource.h</i> and <i>RealSenseSource.h</i>).

\htmlonly <input class="toggle-box" id="fifth-6" type="checkbox" checked>
<label class="spoiler-link" for="fifth-6">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
unix: LIBS += -ldl
win32: DEFINES += _USE_MATH_DEFINES
...
\endcode
</div>

</ol>

\subsection fifth_sensor Connecting the Depth Sensor for Frame Processing 

<ol>

<li> In previous projects, we retrieved the image from a webcam using the <i>QCameraCapture</i> object. However, in this project we have to retrieve both RGB and depth frames. To do this, let's create a new class <i>DepthSensorCapture</i>: <b>Add New > C++ > C++ Class > Choose… > Class name – DepthSensorCapture > Base class – QObject > Next > Project Management (default settings) > Finish</b>. 
<li> In <i>depthsensorcapture.h</i>, import the <i>ImageAndDepthSource</i> header. Also import <i>QSharedPointer</i> to handle pointers, <i>QThread</i> to process threads, <i>QByteArray</i> to work with byte arrays, <i>memory</i> and <i>atomic</i> to handle smart pointers and atomic types respectively. In <i>depthsensorcapture.cpp</i>, import the headers <i>OpenniSource</i> and <i>RealSenseSource</i> to retrieve the depth frames, and also import <i>worker.h</i> and <i>depthsensorcapture.h</i>. We use <i>assert.h</i> to handle errors and <i>QMessageBox</i> to display the error message.  

\htmlonly <input class="toggle-box" id="fifth-7" type="checkbox" checked>
<label class="spoiler-link" for="fifth-7">depthsensorcapture.h</label>\endhtmlonly
<div>
\code
#include "ImageAndDepthSource.h"

#include <QSharedPointer>
#include <QThread>
#include <QByteArray>
#include <memory>
#include <atomic>
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-8" type="checkbox" checked>
<label class="spoiler-link" for="fifth-8">depthsensorcapture.cpp</label>\endhtmlonly
<div>
\code
#if defined(WITH_OPENNI2)
#include "OpenniSource.h"
#elif defined(WITH_REALSENSE)
#include "RealSenseSource.h"
#endif

#include "worker.h"
#include "depthsensorcapture.h"

#include <assert.h>

#include <QMessageBox>
...
\endcode
</div>

<li> Define <i>RGBFramePtr</i>, which is a pointer to an RGB frame, and <i>DepthFramePtr</i>, which is a pointer to a depth frame. The <i>DepthSensorCapture</i> class constructor takes a parent widget and also a pointer to worker. The sensor data will be received in an endless loop. To prevent the main thread, where the interface is rendered, from waiting for completion of the cycle, we create another thread and move the <i>DepthSensorCapture</i> object into this new thread.  

\htmlonly <input class="toggle-box" id="fifth-9" type="checkbox" checked>
<label class="spoiler-link" for="fifth-9">depthsensorcapture.h</label>\endhtmlonly
<div>
\code
...
class Worker;

class DepthSensorCapture : public QObject
{
    Q_OBJECT

public:
    typedef std::shared_ptr<QImage> FramePtr;
    typedef std::shared_ptr<QByteArray> DepthPtr;

    explicit DepthSensorCapture(
        QWidget* parent,
        std::shared_ptr<Worker> worker);
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-10" type="checkbox" checked>
<label class="spoiler-link" for="fifth-10">depthsensorcapture.cpp</label>\endhtmlonly
<div>
\code
...
DepthSensorCapture::DepthSensorCapture(
    QWidget* parent,
    std::shared_ptr<Worker> worker) :
_parent(parent),
_worker(worker)
{
    #if defined(WITH_OPENNI2)
        depth_source.reset(new OpenniSource());
    #else
        depth_source.reset(new RealSenseSource());
    #endif

    thread.reset(new QThread());
    this->moveToThread(thread.data());

    connect(thread.data(), &QThread::started, this, &DepthSensorCapture::frameUpdatedThread);
}
...
\endcode
</div>

<li> In <i>DepthSensorCapture::start</i>, we start the thread, where the data is received, and stop it in <i>DepthSensorCapture::stop</i>. 

\htmlonly <input class="toggle-box" id="fifth-11" type="checkbox" checked>
<label class="spoiler-link" for="fifth-11">depthsensorcapture.h</label>\endhtmlonly
<div>
\code
...
class DepthSensorCapture : public QObject
{
    ...
    void start();
    void stop();
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-12" type="checkbox" checked>
<label class="spoiler-link" for="fifth-12">depthsensorcapture.cpp</label>\endhtmlonly
<div>
\code
...
void DepthSensorCapture::start()
{
    if (!thread->isRunning())
    {
        thread->start();
    }
}

void DepthSensorCapture::stop()
{
    if (thread->isRunning())
    {
        shutdown = true;

        thread->quit();
        thread->wait();
    }
}
...
\endcode
</div>

<li> In <i>DepthSensorCapture::frameUpdatedThread</i>, process a new frame from the sensor in an endless loop and pass it to <i>Worker</i> using <i>addFrame</i>. If an error occurs, an error message box will be displayed.  

\htmlonly <input class="toggle-box" id="fifth-13" type="checkbox" checked>
<label class="spoiler-link" for="fifth-13">depthsensorcapture.h</label>\endhtmlonly
<div>
\code
...
class DepthSensorCapture : public QObject
{
    ...
    signals:
        void newFrameAvailable();

    private
        void frameUpdatedThread();

    private:
        QWidget* _parent;

        std::shared_ptr<Worker> _worker;

        QSharedPointer<QThread> thread;
        QSharedPointer<ImageAndDepthSource> depth_source;

        std::atomic<bool> shutdown = {false};
};
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-14" type="checkbox" checked>
<label class="spoiler-link" for="fifth-14">depthsensorcapture.cpp</label>\endhtmlonly
<div>
\code
...
void DepthSensorCapture::frameUpdatedThread()
{
    while(!shutdown)
    {
        try
        {
            ImageAndDepth data;
            depth_source->get(data);

            _worker->addFrame(data);

            emit newFrameAvailable();
        }
        catch(std::exception& ex)
        {
            stop();

            QMessageBox::critical(
                _parent,
                tr("Face SDK error"),
                ex.what());

            break;
        }
    }
}
...
\endcode
</div>

<li> The <i>VideoFrame</i> object should contain an RGB frame from the sensor.

\htmlonly <input class="toggle-box" id="fifth-15" type="checkbox" checked>
<label class="spoiler-link" for="fifth-15">depthsensorcapture.cpp</label>\endhtmlonly
<div>
\code
...
Database::Database(...)
{
    ...
    VideoFrame frame;
        frame.frame() = DepthSensorCapture::RGBFramePtr(new QImage(image));
    ...
}
...
\endcode
</div>

<li> <i>In videoframe.h</i>, import the <i>depthsensorcapture</i> header to work with the depth sensor. 

\htmlonly <input class="toggle-box" id="fifth-16" type="checkbox" checked>
<label class="spoiler-link" for="fifth-16">videoframe.h</label>\endhtmlonly
<div>
\code
#include "depthsensorcapture.h"
...
\endcode
</div>

<li> The <i>IRawImage</i> interface allows to receive a pointer to the image data, its height and width. 

\htmlonly <input class="toggle-box" id="fifth-17" type="checkbox" checked>
<label class="spoiler-link" for="fifth-17">videoframe.h</label>\endhtmlonly
<div>
\code
...
class VideoFrame : public pbio::IRawImage
{
    public:
        VideoFrame(){}
        ...
        DepthSensorCapture::RGBFramePtr& frame();

        const DepthSensorCapture::RGBFramePtr& frame() const;
 
    private:
        DepthSensorCapture::RGBFramePtr _frame;
}

...

inline
DepthSensorCapture::RGBFramePtr& VideoFrame::frame()
{
    return _frame;
}
 
inline
const DepthSensorCapture::RGBFramePtr& VideoFrame::frame() const
{
    return _frame;
}
...
\endcode
</div>

<li> In the <i>runProcessing</i> method, we start the camera, and in the <i>stopProcessing</i> method, we stop the camera.

\htmlonly <input class="toggle-box" id="fifth-18" type="checkbox" checked>
<label class="spoiler-link" for="fifth-18">viewwindow.h</label>\endhtmlonly
<div>
\code
...
class ViewWindow : public QWidget
{
    ...
    private:
    Ui::ViewWindow *ui;

    QScopedPointer<DepthSensorCapture> _camera;
    ...
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-19" type="checkbox" checked>
<label class="spoiler-link" for="fifth-19">viewwindow.cpp</label>\endhtmlonly
<div>
\code
#include "depthsensorcapture.h"

ViewWindow::ViewWindow(...)
{
    ...
    _camera.reset(new DepthSensorCapture(
    this,
    _worker));

    connect(_camera.data(), &DepthSensorCapture::newFrameAvailable, this, &ViewWindow::draw);
}

void ViewWindow::stopProcessing()
{
    if (!_running)
        return;

    _camera->stop();
    _running = false;
}

void ViewWindow::runProcessing()
{
    if (_running)
        return;

    _camera->start();

    _running = true;
}
...
\endcode
</div>

<li> In <i>worker.h</i>, we import the <i>depthsensorcapture</i> header. The <i>SharedImageAndDepth</i> structure contains the pointers to an RGB frame and a depth frame from the sensor, and also the <i>pbio::DepthMapRaw</i> structure with the information about depth map parameters (width, height, etc.). The pointers are used in <i>Worker</i>. Due to some delay in frame processing, a certain number of frames is queued for rendering. To save the memory space, we store the pointers to frames instead of the frames.

\htmlonly <input class="toggle-box" id="fifth-20" type="checkbox" checked>
<label class="spoiler-link" for="fifth-20">worker.h</label>\endhtmlonly
<div>
\code
#include "depthsensorcapture.h"

    struct SharedImageAndDepth
    {
        DepthSensorCapture::RGBFramePtr color_image;
        DepthSensorCapture::DepthFramePtr depth_image;

        pbio::DepthMapRaw depth_options;
    };
...
\endcode
</div>

<li> Pass <i>SharedImageAndDepth</i> frame, which is RGB and depth frames for rendering, to the <i>DrawingData</i> structure. In <i>TrackingCallback</i>, we extract the image corresponding to the last received result from the frame queue.

\htmlonly <input class="toggle-box" id="fifth-21" type="checkbox" checked>
<label class="spoiler-link" for="fifth-21">worker.h</label>\endhtmlonly
<div>
\code
...
class Worker
{
    ...
    struct DrawingData
    {
        ...       
        SharedImageAndDepth frame;
    }

    void addFrame(const ImageAndDepth& frame);

    private:
        DrawingData _drawing_data;
        std::mutex _drawing_data_mutex;

        std::queue<std::pair<int, SharedImageAndDepth> > _frames;
        ...
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-22" type="checkbox" checked>
<label class="spoiler-link" for="fifth-22">worker.cpp</label>\endhtmlonly
<div>
\code
...
void Worker::TrackingCallback(
    const pbio::VideoWorker::TrackingCallbackData &data,
    void * const userdata)
{    
    ...
    // Get the frame with frame_id
    SharedImageAndDepth frame;
    {
        ...
    }
}

void Worker::addFrame(const ImageAndDepth& data)
{
    ...
}
...
\endcode
</div>

</ol>

\subsection fifth_videoworker_depth_map Processing the Depth Map with VideoWorker

<ol>

<li> In <i>Worker::Worker</i>, override the values of some parameters of the <i>VideoWorker</i> object to process the depth map, namely: 
<ul>
<li ><i>depth_data_flag</i> ("1" turns on depth frame processing to confirm face liveness); 
<li> <i>weak_tracks_in_tracking_callback</i> ("1" means that all samples, even the ones flagged as <i>weak=true</i>, are passed to <i>TrackingCallback</i>). 
</ul>
The <i>weak</i> flag becomes <i>true</i> if a sample doesn't pass certain tests, for example: 
<ul>
<li> if there are too many shadows on a face (insufficient lighting);
<li> if an image is fuzzy (blurred);
<li> if a face is turned at a great angle; 
<li> if a the size of a face in the frame is too small;
<li> if a fase hasn't passed the liveness test (for example, if it's taken from a photo or a video).
</ul>
You can find the detailed information about lighting conditions, camera positioning, etc. in \ref camera_recommendations.
As a rule, samples that haven't passed the tests, are not processed and not used for recognition. However, in this project we want to highlight all the faces, even if they're taken from the picture (haven't passed the liveness test). Therefore, we have to pass all samples to <i>TrackingCallback</i>, even if they're flagged as <i>weak=true</i>.

\htmlonly <input class="toggle-box" id="fifth-23" type="checkbox" checked>
<label class="spoiler-link" for="fifth-23">worker.cpp</label>\endhtmlonly
<div>
\code
...
Worker::Worker(...)
{
    ...
    vwconfig.overrideParameter("depth_data_flag", 1);
    vwconfig.overrideParameter("weak_tracks_in_tracking_callback", 1);
    ...
}
...
\endcode
</div>

<li> In the <i>FaceData</i> structure in <i>worker.h</i>, specify the enumeration <i>pbio::DepthLivenessEstimator</i>, which is the result of liveness estimation. All in all, there are four liveness statuses:
<ul>
<li> <b>NOT_ENOUGH_DATA</b> means that face information is insufficient. This situation may occur if the depth map quality is poor or the user is too close/too far from the sensor.
<li> <b>REAL</b> means that the face belongs to a real person.
<li> <b>FAKE</b> means that the face is taken from a picture or a video.
<li> <b>NOT_COMPUTED</b> means that the face wasn't checked. This situation may occur, for example, if the frames from the sensor are not synchronized (an RGB frame is received but a corresponding depth frame wasn't found in a certain time range).
</ul>
The liveness test result is stored in the <i>face.liveness_status</i> variable for further rendering.

\htmlonly <input class="toggle-box" id="fifth-24" type="checkbox" checked>
<label class="spoiler-link" for="fifth-24">worker.h</label>\endhtmlonly
<div>
\code
...
class Worker
{
public:
    // Face data
    struct FaceData
    {
        ...
        pbio::DepthLivenessEstimator::Liveness liveness_status;
    }
    ...
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-25" type="checkbox" checked>
<label class="spoiler-link" for="fifth-25">worker.cpp</label>\endhtmlonly
<div>
\code
...
void Worker::TrackingCallback(...)
{
    ...
    {
        ...
        // Update the information
        {
            const std::lock_guard<std::mutex> guard(worker._drawing_data_mutex);
            ...
            // Samples
            for(size_t i = 0; i < samples.size(); ++i)
            {
                ...
                face.liveness_status = data.samples_depth_liveness_confirmed[i];
            }
        }
    }
    ...
}
...
\endcode
</div>

<li> In <i>Worker::addFrame</i>, pass the last depth frame to Face SDK using the <i>VideoWorker::addDepthFrame</i> method and store it for further processing.  

\htmlonly <input class="toggle-box" id="fifth-26" type="checkbox" checked>
<label class="spoiler-link" for="fifth-26">worker.h</label>\endhtmlonly
<div>
\code
...
class Worker
{
    ...
    private:
        ...
        DepthSensorCapture::DepthFramePtr _last_depth_image;
        pbio::DepthMapRaw _last_depth_options;
        ...
}
...
\endcode
</div>

\htmlonly <input class="toggle-box" id="fifth-27" type="checkbox" checked>
<label class="spoiler-link" for="fifth-27">worker.cpp</label>\endhtmlonly
<div>
\code
...
void Worker::addFrame(const ImageAndDepth& data)
{
    ...
    const int stream_id = 0;
 
    SharedImageAndDepth frame;
 
    if (!data.depth_image.empty())
    {
        const pbio::DepthMapRaw& depth_options = data.make_dmr();
 
        _video_worker->addDepthFrame(
            depth_options,
            stream_id,
            data.depth_timestamp_microsec);

        _last_depth_image = std::make_shared<QByteArray>(
            (const char*)data.depth_image.data(),
            depth_options.depth_data_stride_in_bytes * depth_options.depth_map_rows);
        _last_depth_options = depth_options;
 
        frame.depth_image = _last_depth_image;
        frame.depth_options = depth_options;
    }
}
...
\endcode
</div>

<li> Prepare and pass the RGB image to Face SDK using <i>VideoWorker::addVideoFrame</i>. If the format of the received RGB image is BGR instead of RGB, the byte order is changed so that the image colors are displayed correctly.  If a depth frame wasn't received together with an RGB frame, the last received depth frame is used.  A pair of the depth and RGB frames is queued in <i>_frames</i> in order to find the data corresponding to the processing result in <i>TrackingCallback</i>.

\htmlonly <input class="toggle-box" id="fifth-28" type="checkbox" checked>
<label class="spoiler-link" for="fifth-28">worker.cpp</label>\endhtmlonly
<div>
\code
...
void Worker::addFrame(const ImageAndDepth& data)
{
    ...
    if (!data.color_image.empty())
    {
        QImage image(
            (const uchar*)data.color_image.data(),
            data.color_width,
            data.color_height,
            data.color_stride_in_bytes,
            QImage::Format_RGB888);

        if (data.color_format != pbio::IRawImage::FORMAT_RGB)
        {
            image = image.rgbSwapped();
        }
 
        frame.color_image = std::make_shared<QImage>(image);

        VideoFrame video_frame;
        video_frame.frame() = frame.color_image;

        const std::lock_guard<std::mutex> guard(_frames_mutex);

        const int frame_id = _video_worker->addVideoFrame(
            video_frame,
            stream_id,
            data.image_timestamp_microsec);

        if (data.depth_image.empty())
        {
            frame.depth_image = _last_depth_image;
            frame.depth_options = _last_depth_options;
        }

        _frames.push(std::make_pair(frame_id, frame));
    }

    _video_worker->checkExceptions();
}
...
\endcode
</div>

</ol>

\subsection fifth_visualization Visualizing RGB and Depth Maps. Displaying 3D Liveness Information

<ol>

<li> Let's modify <i>DrawFunction::Draw</i> in <i>drawfunction.cpp</i>. The <i>frame</i> field of the <i>Worker::DrawingData</i> structure contains the pointers to RGB frame data and depth frame data, as well as depth frame parameters (width, height, etc.). For convenience, we'll create the references <i>const QImage& color_image</i>, <i>const QByteArray& depth_array</i>, and <i>const pbio::DepthMapRaw& depth_options</i> to refer to these data. The RGB image and depth map will be displayed in <i>QImage result</i>, which can be considered as a sort of a "background" and contains both images (an RGB image at the top and a depth map at the bottom). Before that, we have to convert 16-bit depth values to 8-bit depth values to correctly display the depth map (in grayscale). In the <i>max_depth_mm</i> value, specify the maximum distance from the sensor to the user (usually, it's 10 meters).  

\htmlonly <input class="toggle-box" id="fifth-29" type="checkbox" checked>
<label class="spoiler-link" for="fifth-29">drawfunction.cpp</label>\endhtmlonly
<div>
\code
...
// static
QImage DrawFunction::Draw(
    const Worker::DrawingData &data,
    const Database &database)
{
    const QImage& color_image = *data.frame.color_image;
    const QByteArray& depth_array = *data.frame.depth_image;
    const pbio::DepthMapRaw& depth_options = data.frame.depth_options;

    QByteArray depth8_array;
    const uint16_t* depth_ptr = (const uint16_t*)depth_array.constData();
    const size_t elements_count_in_row = depth_options.depth_data_stride_in_bytes / sizeof(uint16_t);
    const size_t elements_count = depth_options.depth_map_rows * elements_count_in_row;
    const float max_depth_mm = 10000.f;
    for(size_t i = 0; i < elements_count; ++i)
    {
        const uint16_t depth_mm = depth_ptr[i] * depth_options.depth_unit_in_millimeters;
        const uint8_t depth_8bit = std::min<int>(255, depth_mm / max_depth_mm * 255 + 0.5);

        depth8_array += depth_8bit;
    }
    ...
}
...
\endcode
</div>

<li> Form the depth image from the converted values. Create the <i>result</i> object, which will be used to display an RGB image (at the top) and a depth map (at the bottom). Render these images.  

\htmlonly <input class="toggle-box" id="fifth-30" type="checkbox" checked>
<label class="spoiler-link" for="fifth-30">drawfunction.cpp</label>\endhtmlonly
<div>
\code
...
QImage DrawFunction::Draw(...)
{
    ...
    QImage depth_image(
        (const uchar*)depth8_array.constData(),
        depth_options.depth_map_cols,
        depth_options.depth_map_rows,
        elements_count_in_row,
        QImage::Format_Grayscale8);

    QImage result(
        QSize(std::max<int>(color_image.width(), depth_image.width()),
            color_image.height() + depth_image.height()),
        QImage::Format_RGB888);

    QPainter painter(&result);

    painter.drawImage(QPoint(0, 0), color_image);
    painter.drawImage(QPoint(0, color_image.height()), depth_image);
    ...
}
...
\endcode
</div>

<li> Display the liveness status next to the face depending on the information received from the liveness detector. Specify the label parameters (color, line, size). You'll see a bounding rectangle in the depth map so that you can make sure that the RGB frame and depth frame are aligned.  

\htmlonly <input class="toggle-box" id="fifth-31" type="checkbox" checked>
<label class="spoiler-link" for="fifth-31">drawfunction.cpp</label>\endhtmlonly
<div>
\code
...
QImage DrawFunction::Draw(...)
{
    ...
    for(const auto &face_data : faces_data)
    {
        ...
        if(face.frame_id == data.frame_id && !face.lost)
        {
            ...
            // Draw the bounding box
            pen.setWidth(3);
            pen.setColor(color);
            painter.setPen(pen);
            painter.drawRect(rect);

            if (!depth_image.size().isEmpty())
            {
                const QRect rect_on_depth(
                bounding_box.x, bounding_box.y + color_image.height(),
bounding_box.width, bounding_box.height);

                painter.drawRect(rect_on_depth);

                QString liveness_str = "Liveness status: ";
                switch(face.liveness_status)
                {
                    case pbio::DepthLivenessEstimator::NOT_ENOUGH_DATA:
                        liveness_str += "NOT ENOUGH DATA";
                        break;
                    case pbio::DepthLivenessEstimator::REAL:
                        liveness_str += "REAL";
                        break;
                    case pbio::DepthLivenessEstimator::FAKE:
                        liveness_str += "FAKE";
                        break;
                    default:
                        liveness_str += "NOT COMPUTED";
                }

                const int font_size = 12;

                painter.setFont(QFont("Arial", font_size, QFont::Medium));
                pen.setColor(Qt::black);
                painter.setPen(pen);
                painter.drawText(rect.topLeft() + QPoint(1, -pen.width()), liveness_str);

                pen.setColor(Qt::white);
                painter.setPen(pen);
                painter.drawText(rect.topLeft() + QPoint(0, -pen.width()), liveness_str);
            }
            ...
        }
        ...
    }
    ...
}
...
\endcode
</div>

<li> Run the project. You should see an RGB image and a depth map from the sensor. You should see the information about the detected face:  
<ul>
<li> <b>detection and recognition status</b> (which is indicated by the color of the bounding rectangle: <i>green</i> means that a person is detected and found in the database, <i>red</i> means that a person is not recognized or the face is taken from an image or a video);
<li> <b>information about the recognized person</b> (his/her image and name from the database);
<li> <b>liveness status</b>; <i>real</i> means that a person is real, <i>fake</i> means that a face is taken from an image or a video, <i>not_enough_data</i> means that the depth map quality is poor or a person is too close/too far away from the sensor, <i>not_computed</i> means that RGB and depth frames are not synchronized.
</ul>

</ol>

\htmlonly <style>div.image img[src="fifth_1.png"]{width:1000px;}</style> \endhtmlonly 
@image html fifth_1.png
