# Face Recognition in a Video Stream  

In this tutorial, you'll learn how to recognize faces in a video stream. For recognition, you can use a ready-made database of faces from the Face SDK distribution package. The database includes the images of several famous people. Recognized faces are highlighted with a green rectangle. The name and image of a recognized person are displayed next to his/her face in a video stream.  This tutorial is based on [Face Detection and Tracking in a Video Stream](face_detection_and_tracking_in_a_video_stream.md) and the corresponding project.  

You can find the tutorial project in Face SDK: *examples/tutorials/face_recognition_with_video_worker*

<p align="center">
<img width="600" src="../img/fourth_2"><br>
</p>

## Setting Up the Project

<li> In [Face Detection and Tracking in a Video Stream](face_detection_and_tracking_in_a_video_stream.md), we set only two parameters of Face SDK (a path to Face SDK and a configuration file name for the `VideoWorker` object). However, in this tutorial, we need to set several more parameters: we will add a path to the database, a configuration file name with a recognition method, and FAR. For convenience, we'll modify several files. Specify all the parameters in the `FaceSdkParameters` structure. In `facesdkparameters.h`, specify the path to the `video_worker_lbf.xml` configuration file. 

**facesdkparameters.h**
```cpp
struct FaceSdkParameters
{
    ...
    std::string videoworker_config = "video_worker_lbf.xml";
};
```

<li> Pass the `face_sdk_parameters` structure to the constructor of the `Worker` object.

**viewwindow.h**
```cpp
class ViewWindow : public QWidget
{
	Q_OBJECT

    public:
	    explicit ViewWindow(
		    QWidget *parent,
		    pbio::FacerecService::Ptr service,
		    FaceSdkParameters face_sdk_parameters);
    ...
    private:
    ...
        std::shared_ptr<Worker> _worker;

        pbio::FacerecService::Ptr _service;
};
```

**viewwindow.cpp**
```cpp
ViewWindow::ViewWindow(
    QWidget *parent,
    pbio::FacerecService::Ptr service,
    FaceSdkParameters face_sdk_parameters) :
QWidget(parent),
ui(new Ui::ViewWindow),
_service(service)
{
    ui->setupUi(this);
    ...
    _worker = std::make_shared<Worker>(
        _service,
        face_sdk_parameters);
    ...
};
```

**worker.h**
```cpp
#include "qcameracapture.h"
#include "facesdkparameters.h"
...
class Worker
{
    ...
    Worker(
        const pbio::FacerecService::Ptr service,
        const FaceSdkParameters face_sdk_parameters);
    ...
};
```

**worker.cpp**
```cpp
Worker::Worker(
    const pbio::FacerecService::Ptr service,
    const FaceSdkParameters face_sdk_parameters)
{
    pbio::FacerecService::Config vwconfig(face_sdk_parameters.videoworker_config);
    ...
}
```

3. In this project, we're interested only in face detection in a video stream (creating a bounding rectangle) and face recognition. Please note that in the first project (`detection_and_tracking_with_video_worker`), which you can use as a reference for this project, we also displayed anthropometric points and angles. If you don't want to display this info, you can just remove unnecessary visualization from the first project.

## Creating the Database of Faces

<li> First of all, we have to create a database of faces. To check face recognition, you can use the ready-made database from Face SDK. It includes images of three famous people (Elon Musk, Emilia Clarke, Lionel Messi).  To check recognition, you should copy the database to the project root folder (next to a .pro file), run the project, open an image from the database, and point a camera at the screen. You can also add your picture to the database. To do this, you have to create a new folder in the database, specify your name in a folder name, and copy your picture to the folder (in the same way as other folders in the database). 

<li> Create a new <i>Database</i> class to work with the database: <b>Add New > C++ > C++ Class > Choose... > Class name – Database > Next > Project Management (default settings) > Finish</b>. In <i>database.h</i>, include the headers <i>QImage</i> and <i>QString</i> to work with images and strings and <i>libfacerec.h</i> to integrate Face SDK.

\htmlonly <input class="toggle-box" id="fourth-6" type="checkbox" checked>
<label class="spoiler-link" for="fourth-6">database.h</label>\endhtmlonly
<div>
\code
#include <QImage>
#include <QString>

#include <facerec/libfacerec.h>

class Database
{
    public:

    Database();
}
\endcode
</div>

<li> In <i>database.cpp</i>, include the headers <i>database.h</i> and <i>videoframe.h</i> (implementation of the <i>IRawImage</i> interface, which is used by <i>VideoWorker</i> to receive the frames). Also include necessary headers for working with the file system, debugging, exception handling, and working with files. 

\htmlonly <input class="toggle-box" id="fourth-7" type="checkbox" checked>
<label class="spoiler-link" for="fourth-7">database.cpp</label>\endhtmlonly
<div>
\code
#include "database.h"
#include "videoframe.h"

#include <QDir>
#include <QDebug>

#include <stdexcept>
#include <fstream>
\endcode
</div>

<li> In <i>database.h</i>, add a constructor and set the path to the database. Specify the <i>Recognizer</i> object to create templates, the <i>Capturer</i> object to detect faces and <i>far</i>. What is FAR? FAR is frequency that the system makes false accepts. False accept means that a system claims a pair of pictures are a match, when they are actually pictures of different individuals. The <i>vw_elements</i> vector contains the elements of the <i>VideoWorker</i> database. The <i>thumbnails</i> and <i>names</i> vectors contain the previews of images and names of people from the database.

\htmlonly <input class="toggle-box" id="fourth-8" type="checkbox" checked>
<label class="spoiler-link" for="fourth-8">database.h</label>\endhtmlonly
<div>
\code
class Database
{
    public:
    ...
    // Create a database
    Database(
        const std::string database_dir_path,
        pbio::Recognizer::Ptr recognizer,
        pbio::Capturer::Ptr capturer,
        const float fa_r);

    std::vector<pbio::VideoWorker::DatabaseElement> vw_elements;
    std::vector<QImage> thumbnails;
    std::vector<QString> names;
}
\endcode
</div>

<li> In <i>database.cpp</i>, implement the <i>Database</i> constructor, which was declared in the previous subsection. The <i>distance_threshold</i> value means the recognition distance. Since this distance is different for different recognition methods, we get it based on the <i>FAR</i> value using the <i>getROCCurvePointByFAR</i> method. 

\htmlonly <input class="toggle-box" id="fourth-9" type="checkbox" checked>
<label class="spoiler-link" for="fourth-9">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(
    const std::string database_dir_path,
    pbio::Recognizer::Ptr recognizer,
    pbio::Capturer::Ptr capturer,
    const float fa_r)
{
    const float distance_threshold = recognizer->getROCCurvePointByFAR(fa_r).distance;
}
\endcode
</div>

<li> In the <i>database_dir</i> variable, specify the path to the database with faces. If this path doesn't exist, you'll see the exception <i>"database directory doesn't exist"</i>.  Create a new <i>person_id</i> variable to store the id of a person from the database (name of a folder in the database) and the <i>element_id</i> variable to store the id of an element in the database (an image of a person from the database). In the <i>dirs</i> list, create a list of all subdirectories of the specified directory with the database. 

\htmlonly <input class="toggle-box" id="fourth-10" type="checkbox" checked>
<label class="spoiler-link" for="fourth-10">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(
    const std::string database_dir_path,
    pbio::Recognizer::Ptr recognizer,
    pbio::Capturer::Ptr capturer,
    const float fa_r)
{
    ...

    QDir database_dir(QString::fromStdString(database_dir_path));

    if (!database_dir.exists())
    {
        throw std::runtime_error(database_dir_path + ": database directory doesn't exist");
    }

    int person_id = 0;
    int element_id_counter = 0;

    QFileInfoList dirs = database_dir.entryInfoList(
        QDir::AllDirs | QDir::NoDotAndDotDot,
        QDir::DirsFirst);
}
\endcode
</div>

@note
See more information about FAR and TAR values for different recognition methods in \ref verify_perf.

<li> In the loop <i>for(const auto &dir: dirs)</i>, process each subdirectory (data about each person). The name of a folder corresponds to the name of a person. Create a list of images in <i>person_files</i>.   

\htmlonly <input class="toggle-box" id="fourth-11" type="checkbox" checked>
<label class="spoiler-link" for="fourth-11">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(...)
{
    ...
    for(const auto &dir: dirs)
    {
        QDir person_dir(dir.filePath());

        QString name = dir.baseName();

        QFileInfoList person_files = person_dir.entryInfoList(QDir::Files | QDir::NoDotAndDotDot);
    }
}
\endcode
</div>

<li> In the nested loop <i>for(const auto &person_file: person_files)</i>, process each image. If an image doesn't exist, the warning <i>"Can't read image"</i> is displayed. 

\htmlonly <input class="toggle-box" id="fourth-12" type="checkbox" checked>
<label class="spoiler-link" for="fourth-12">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(...)
{
    ...
    for(const auto &dir: dirs)
    {
        ...        
        QFileInfoList person_files = person_dir.entryInfoList(QDir::Files | QDir::NoDotAndDotDot);
        
        for(const auto &person_file: person_files)
        {
            QString path = person_file.filePath();

            qDebug() << "processing" << path << "name:" << name;

            QImage image(path);
            if(image.isNull())
            {
                qDebug() << "\n\nWARNING: cant read image" << path << "\n\n";
                continue;
            }

            if (image.format() != QImage::Format_RGB888)
            {
                image = image.convertToFormat(QImage::Format_RGB888);
            }

            VideoFrame frame;
            frame.frame() = QCameraCapture::FramePtr(new QImage(image));
        }
    }
}
\endcode
</div>

<li> Detect a face in an image using the <i>Capturer</i> object. If an image cannot be read, a face can't be found in an image or more than one face is detected, the warning is displayed and this image is ignored. 

\htmlonly <input class="toggle-box" id="fourth-13" type="checkbox" checked>
<label class="spoiler-link" for="fourth-13">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(...)
{
    ...
    for(const auto &dir: dirs)
    {
        ...        
        QFileInfoList person_files = person_dir.entryInfoList(QDir::Files | QDir::NoDotAndDotDot);
        
        for(const auto &person_file: person_files)
        {
            ...
            // Detect faces
            const std::vector<pbio::RawSample::Ptr> captured_samples = capturer->capture(frame);

            if(captured_samples.size() != 1)
            {
                qDebug() << "\n\nWARNING: detected" << captured_samples.size() <<
                    "faces on" << path << "image instead of one, image ignored\n\n";
                continue;
            }
            const pbio::RawSample::Ptr sample = captured_samples[0];
        }
    }
}
\endcode
</div>

<li> Using the <i>recognizer->processing</i> method, create a face template, which is used for recognition. 

\htmlonly <input class="toggle-box" id="fourth-14" type="checkbox" checked>
<label class="spoiler-link" for="fourth-14">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(...)
{
    ...
    for(const auto &dir: dirs)
    {
        ...        
        QFileInfoList person_files = person_dir.entryInfoList(QDir::Files | QDir::NoDotAndDotDot);
        
        for(const auto &person_file: person_files)
        {
            ...
            // Create a template
            const pbio::Template::Ptr templ = recognizer->processing(*sample);
        }
    }
}
\endcode
</div>

<li> In the structure <i>pbio::VideoWorker::DatabaseElement vw_element</i>, specify all the information about the database element that will be passed for processing to the <i>VideoWorker</i> object (element id, person id, face template, recognition threshold). Using the <i>push_back</i> method, add an element to the end of the list.

\htmlonly <input class="toggle-box" id="fourth-15" type="checkbox" checked>
<label class="spoiler-link" for="fourth-15">database.cpp</label>\endhtmlonly
<div>
\code
Database::Database(...)
{
    ...
    for(const auto &dir: dirs)
    {
        ...        
        QFileInfoList person_files = person_dir.entryInfoList(QDir::Files | QDir::NoDotAndDotDot);
        
        for(const auto &person_file: person_files)
        {
            ...
            // Prepare data for VideoWorker
            pbio::VideoWorker::DatabaseElement vw_element;
            vw_element.element_id = element_id_counter++;
            vw_element.person_id = person_id;
            vw_element.face_template = templ;
            vw_element.distance_threshold = distance_threshold;

            vw_elements.push_back(vw_element);
            thumbnails.push_back(makeThumbnail(image));
            names.push_back(name);
        }

        ++person_id;
    }
}
\endcode
</div>

<li> In <i>database.h</i>, add the <i>makeThumbnail</i> method to create a preview of a picture from the database. 

\htmlonly <input class="toggle-box" id="fourth-16" type="checkbox" checked>
<label class="spoiler-link" for="fourth-16">database.cpp</label>\endhtmlonly
<div>
\code
class Database
{
    public:
        // Create a preview from a sample
        static
        QImage makeThumbnail(const QImage& image);
        ...
};
\endcode
</div>

<li> In <i>database.cpp</i>, implement the method using <i>makeThumbnail</i> to create a preview of a picture from the database, which will be displayed next to the face of a recognized person. Set the preview size (120 pixels) and scale it (keep the ratio if the image is resized).

\htmlonly <input class="toggle-box" id="fourth-17" type="checkbox" checked>
<label class="spoiler-link" for="fourth-17">database.cpp</label>\endhtmlonly
<div>
\code
#include <fstream>
...
QImage Database::makeThumbnail(const QImage& image)
{
    const float thumbnail_max_side_size = 120.f;

    const float scale = thumbnail_max_side_size / std::max<int>(image.width(), image.height());

    QImage result = image.scaled(
        image.width() * scale,
        image.height() * scale,
        Qt::KeepAspectRatio,
        Qt::SmoothTransformation);

    return result;
}
\endcode
</div>

<li> In the .pro file, set the path to the database. 

\htmlonly <input class="toggle-box" id="fourth-18" type="checkbox" checked>
<label class="spoiler-link" for="fourth-18">face_recognition_with_video_worker.pro</label>\endhtmlonly
<div>
\code
...
DEFINES += FACE_SDK_PATH=\\\"$$FACE_SDK_PATH\\\"

DEFINES += DATABASE_PATH=\\\"$${_PRO_FILE_PWD_}/base\\\"

INCLUDEPATH += $${FACE_SDK_PATH}/include
...
\endcode
</div>

<li> In <i>facesdkparameters.h</i>, set the path to the database and the value of FAR. 

\htmlonly <input class="toggle-box" id="fourth-19" type="checkbox" checked>
<label class="spoiler-link" for="fourth-19">facesdkparameters.h</label>\endhtmlonly
<div>
\code
struct FaceSdkParameters
{
    ...
    std::string videoworker_config = "video_worker_lbf.xml";

    std::string database_dir = DATABASE_PATH;
    const float fa_r = 1e-5;
};
\endcode
</div>
</ol>

\subsection fourth_search_display Searching a Face in the Database and Displaying the Result 

<ol>

<li> In <i>facesdkparameters.h</i>, set the path to the configuration file with the recognition method. In this project, we use the method 6v6 because it's suitable for video stream processing and provides optimal recognition speed and good quality. You can learn more about recommended recognition methods in \ref verify_api. 

\htmlonly <input class="toggle-box" id="fourth-20" type="checkbox" checked>
<label class="spoiler-link" for="fourth-20">facesdkparameters.h</label>\endhtmlonly
<div>
\code
struct FaceSdkParameters
{
    ...
    std::string videoworker_config = "video_worker_lbf.xml";

    std::string database_dir = DATABASE_PATH;
    const float fa_r = 1e-5;
    std::string method_config = "method6v6_recognizer.xml";
};
\endcode
</div>

@note
If you want to recognize faces in a video stream and you use low-performance devices, you can use the method 8.6. In this case, recognition speed is higher but recognition quality is lower compared to the method 6.6.

<li> In <i>worker.h</i>, add the variable <i>match_database_index</i> to the <i>FaceData</i> structure. This variable will store the database element, if a person is recognized, or <i>"-1"</i> if a person is not recognized. Add <i>Database</i> and a callback indicating that a person is recognized (<i>MatchFoundCallback</i>).

\htmlonly <input class="toggle-box" id="fourth-21" type="checkbox" checked>
<label class="spoiler-link" for="fourth-21">worker.h</label>\endhtmlonly
<div>
\code
#include "qcameracapture.h"
#include "facesdkparameters.h"
#include "database.h"
...
class Worker
{
public:

    struct FaceData
    {
        pbio::RawSample::Ptr sample;
            bool lost;
            int frame_id;
            int match_database_index;

            FaceData() :
                lost(true),
                match_database_index(-1)
            {
            }
    };
    ...
    pbio::VideoWorker::Ptr _video_worker;

    Database _database;
    ...
    static void TrackingLostCallback(
        const pbio::VideoWorker::TrackingLostCallbackData &data,
        void* const userdata);

    static void MatchFoundCallback(
        const pbio::VideoWorker::MatchFoundCallbackData &data,
        void* const userdata);

    int _tracking_callback_id;
    int _tracking_lost_callback_id;
    int _match_found_callback_id;
};
\endcode
</div>

<li> In <i>worker.cpp</i>, override the value of the parameter in the configuration file so that <i>MatchFoundCallback</i> is received for non-recognized faces too. Set the parameters of the <i>VideoWorker</i> object: in the first tutorial, we didn't recognize faces, that's why we set only the value of <i>streams_count</i>. Since in this project we're going to recognize faces in a video stream we have to specify in the constructor the path to the configuration file with the recognition method, and also the values of <i>processing_threads_count</i> (number of threads to create templates) and <i>matching_threads_count</i> (number of threads to compare the templates). In this project, we use only one stream (a webcam connected to our PC). Connect the database: pass the path to the database, create <i>Capturer</i> to detect faces and <i>Recognizer</i> to create templates, and also specify the <i>FAR</i> coefficient.  Using the <i>setDatabase</i> method, set the database for <i>VideoWorker</i>. Using the <i>addMatchFoundCallback</i> method, add the recognition event handler <i>MatchFound</i>.  

\htmlonly <input class="toggle-box" id="fourth-22" type="checkbox" checked>
<label class="spoiler-link" for="fourth-22">worker.cpp</label>\endhtmlonly
<div>
\code
Worker::Worker(
    const pbio::FacerecService::Ptr service,
    const FaceSdkParameters face_sdk_parameters)
{
    pbio::FacerecService::Config vwconfig(face_sdk_parameters.videoworker_config);
   
    vwconfig.overrideParameter("not_found_match_found_callback", 1);

    _video_worker = service->createVideoWorker(
        vwconfig,
        face_sdk_parameters.method_config,
        1,   // streams_count
        1,   // processing_threads_count
        1);  // matching_threads_count

    _database = Database(
        face_sdk_parameters.database_dir,
        service->createRecognizer(face_sdk_parameters.method_config, true, false),
        service->createCapturer("common_capturer4_lbf_singleface.xml"),
        face_sdk_parameters.fa_r);

    _video_worker->setDatabase(_database.vw_elements);
    ...

    _match_found_callback_id =
        _video_worker->addMatchFoundCallbackU(
            MatchFoundCallback,
            this);
}
\endcode
</div>

<li> In the destructor <i>Worker::~Worker()</i>, remove <i>MatchFoundCallback</i>.

\htmlonly <input class="toggle-box" id="fourth-23" type="checkbox" checked>
<label class="spoiler-link" for="fourth-23">worker.cpp</label>\endhtmlonly
<div>
\code
Worker::~Worker()
{
    _video_worker->removeTrackingCallback(_tracking_callback_id);
    _video_worker->removeTrackingLostCallback(_tracking_lost_callback_id);
    _video_worker->removeMatchFoundCallback(_match_found_callback_id);
}
...
\endcode
</div>

<li> In <i>MatchFoundCallback</i>, the result is received in form of the structure <i>MatchFoundCallbackData</i> that stores the information about recognized and unrecognized faces. 

\htmlonly <input class="toggle-box" id="fourth-24" type="checkbox" checked>
<label class="spoiler-link" for="fourth-24">worker.cpp</label>\endhtmlonly
<div>
\code
// static
void Worker::TrackingLostCallback(
    const pbio::VideoWorker::TrackingLostCallbackData &data,
    void* const userdata)
{
    ...
}

// static
void Worker::MatchFoundCallback(
    const pbio::VideoWorker::MatchFoundCallbackData &data,
    void* const userdata)
{
    assert(userdata);

    const pbio::RawSample::Ptr &sample = data.sample;
    const pbio::Template::Ptr &templ = data.templ;
    const std::vector<pbio::VideoWorker::SearchResult> &search_results = data.search_results;

    // Information about a user - a pointer to Worker
    // Pass the pointer
    Worker &worker = *reinterpret_cast<Worker*>(userdata);

    assert(sample);
    assert(templ);
    assert(!search_results.empty());
}
\endcode
</div>

<li> When a template for a tracked person is created, it's compared with each template from the database. If the distance to the closest element is less than <i>distance_threshold</i> specified in this element, then it's a match. If a face in a video stream is not recognied, then you'll see the message <i>"Match not found"</i>. If a face is recognized, you'll see the message <i>"Match found with..."</i> and the name of the matched person. 

\htmlonly <input class="toggle-box" id="fourth-25" type="checkbox" checked>
<label class="spoiler-link" for="fourth-25">worker.cpp</label>\endhtmlonly
<div>
\code
// static
void Worker::MatchFoundCallback(
    const pbio::VideoWorker::MatchFoundCallbackData &data,
    void* const userdata)
{
    ...    
    for(const auto &search_result: search_results)
    {
        const uint64_t element_id = search_result.element_id;

        if(element_id == pbio::VideoWorker::MATCH_NOT_FOUND_ID)
        {
            std::cout << "Match not found" << std::endl;
        }
        else
        {
            assert(element_id < worker._database.names.size());

            std::cout << "Match found with '"
                << worker._database.names[element_id].toStdString() << "'";
        }
    }
    std::cout << std::endl;
}
\endcode
</div>

<li> Save the data about the recognized face to display a preview. 

\htmlonly <input class="toggle-box" id="fourth-26" type="checkbox" checked>
<label class="spoiler-link" for="fourth-26">worker.cpp</label>\endhtmlonly
<div>
\code
// static
void Worker::MatchFoundCallback(
    const pbio::VideoWorker::MatchFoundCallbackData &data,
    void* const userdata)
{
    ...
    const uint64_t element_id = search_results[0].element_id;

    if(element_id != pbio::VideoWorker::MATCH_NOT_FOUND_ID)
    {
        assert(element_id < worker._database.thumbnails.size());

        // Save the best matching result for rendering
        const std::lock_guard<std::mutex> guard(worker._drawing_data_mutex);

        FaceData &face = worker._drawing_data.faces[sample->getID()];

        assert(!face.lost);

        face.match_database_index = element_id;
    }
}
\endcode
</div>

<li> Run the project. The recognition results will be displayed in the console. If a face is recognized, you'll see the face id and name of a recognized person from the database. If a face isn't recognized, you'll see the message <i>"Match not found"</i>.
</ol>

\htmlonly <style>div.image img[src="fourth_1.jpeg"]{width:400px;}</style> \endhtmlonly 
@image html fourth_1.jpeg

\subsection fourth_preview Displaying the Preview of the Recognized Face from the Database 

<ol>

<li> Let's make our project a little nicer. We'll display the image and name of a person from the database next to the face in a video stream.  
In <i>drawfunction.h</i>, add a reference to the database, because we'll need it when rendering the recognition results. 

\htmlonly <input class="toggle-box" id="fourth-27" type="checkbox" checked>
<label class="spoiler-link" for="fourth-27">drawfunction.h</label>\endhtmlonly
<div>
\code
#include "database.h"

class DrawFunction
{
public:
    DrawFunction();

    static QImage Draw(
        const Worker::DrawingData &data,
        const Database &database);
};
\endcode
</div>

<li> In <i>drawfunction.cpp</i>, modify the function <i>DrawFunction::Draw</i> by passing the database to it.

\htmlonly <input class="toggle-box" id="fourth-28" type="checkbox" checked>
<label class="spoiler-link" for="fourth-28">drawfunction.cpp</label>\endhtmlonly
<div>
\code
// static
QImage DrawFunction::Draw(
    const Worker::DrawingData &data,
    const Database &database)
{
    ...
    const pbio::RawSample& sample = *face.sample;
    QPen pen;
}
\endcode
</div>

<li> Save the bounding rectangle in the structure <i>pbio::RawSample::Rectangle</i>. Pass its parameters (x, y, width, height) to the <i>QRect rect</i> object. 

\htmlonly <input class="toggle-box" id="fourth-29" type="checkbox" checked>
<label class="spoiler-link" for="fourth-29">drawfunction.cpp</label>\endhtmlonly
<div>
\code
QImage DrawFunction::Draw(...)
{
    ...
    // Save the face bounding rectangle
    const pbio::RawSample::Rectangle bounding_box = sample.getRectangle();
    QRect rect(bounding_box.x, bounding_box.y, bounding_box.width, bounding_box.height);
}
\endcode
</div>

<li> Create a boolean variable <i>recognized</i> that indicates whether a face is recognized or unrecognized. If a face is recognized, the bounding rectangle is green, otherwise it's red.  

\htmlonly <input class="toggle-box" id="fourth-30" type="checkbox" checked>
<label class="spoiler-link" for="fourth-30">drawfunction.cpp</label>\endhtmlonly
<div>
\code
QImage DrawFunction::Draw(...)
{
    ...
    const bool recognized = face.match_database_index >= 0;

    const QColor color = recognized ?
        Qt::green :
        Qt::red;  // Unrecognized faces are highlighted with red

    // Display the face bounding rectangle
    {
        pen.setWidth(3);
        pen.setColor(color);
        painter.setPen(pen);
        painter.drawRect(rect);
    }
}
\endcode
</div>

<li> Get a relevant image from the database for a preview by <i>face.match_database_index</i>. Calculate the position of a preview in the frame. 

\htmlonly <input class="toggle-box" id="fourth-31" type="checkbox" checked>
<label class="spoiler-link" for="fourth-31">drawfunction.cpp</label>\endhtmlonly
<div>
\code
QImage DrawFunction::Draw(...)
{
    ...
        // Display the image from the database
        if (recognized)
        {
            const QImage thumbnail = database.thumbnails[face.match_database_index];

            // Calculate the preview position
            QPoint preview_pos(
                rect.x() + rect.width() + pen.width(),
                rect.top());
}
\endcode
</div>

<li> Draw an image from the database in the preview. Create the object <i>QImage face_preview</i> that is higher than <i>thumbnail</i> on <i>text_bar_height</i>. The original preview image is drawn in the position (0, 0). As a result, we get a preview with a black rectangle at the bottom with the name of a person. Set the font parameters, calculate the position of a text and display the text in the preview.

\htmlonly <input class="toggle-box" id="fourth-32" type="checkbox" checked>
<label class="spoiler-link" for="fourth-32">drawfunction.cpp</label>\endhtmlonly
<div>
\code
QImage DrawFunction::Draw(...)
{
    ...
    // Display the image from the database
    if (recognized)
    {
        ...
        const int text_bar_height = 20;

        QImage face_preview(
            QSize(thumbnail.width(), thumbnail.height() + text_bar_height),
            QImage::Format_RGB888);
        face_preview.fill(Qt::black);

        {
            const int font_size = 14;

            QPainter painter_preview(&face_preview);

            painter_preview.drawImage(QPoint(0, 0), thumbnail);

            painter_preview.setFont(QFont("Arial", font_size, QFont::Medium));
            pen.setColor(Qt::white);
            painter_preview.setPen(pen);
            painter_preview.drawText(
            QPoint(0, thumbnail.height() + text_bar_height - (text_bar_height - font_size) / 2),
            database.names[face.match_database_index]);
        }
    }
}
\endcode
</div>

<li> Draw <i>face_preview</i> in the frame using the <i>drawPixmap</i> method.

\htmlonly <input class="toggle-box" id="fourth-33" type="checkbox" checked>
<label class="spoiler-link" for="fourth-33">drawfunction.cpp</label>\endhtmlonly
<div>
\code
// static
QImage DrawFunction::Draw(...)
{
    ...
 
    // Display the image from the database
    if (recognized)
    {
        ...
        QPixmap pixmap;
        pixmap.convertFromImage(face_preview);

        painter.drawPixmap(preview_pos, pixmap);
    }
}
\endcode
</div>

<li> In <i>worker.h</i>, add a method that returns the reference to the database. 

\htmlonly <input class="toggle-box" id="fourth-34" type="checkbox" checked>
<label class="spoiler-link" for="fourth-34">worker.h</label>\endhtmlonly
<div>
\code
class Worker
{
public:
    ...

    void getDataToDraw(DrawingData& data);

    const Database& getDatabase() const
    {
        return _database;
    }
};
\endcode
</div>

<li> Modify the call to <i>DrawFunction::Draw</i> by passing the database to it. 

\htmlonly <input class="toggle-box" id="fourth-35" type="checkbox" checked>
<label class="spoiler-link" for="fourth-35">viewwindow.cpp</label>\endhtmlonly
<div>
\code
void ViewWindow::draw()
{
    ...
    const QImage image = DrawFunction::Draw(data, _worker->getDatabase());

    ui->frame->setPixmap(QPixmap::fromImage(image));
}
\endcode
</div>

<li> Run the project. If a face is recognized, it will be highlighted with a green rectangle and you'll see a preview of an image from the database and a person's name. Unrecognized faces will be highlighted with a red rectangle. 
</ol>

\htmlonly <style>div.image img[src="fourth_2.png"]{width:600px;}</style> \endhtmlonly 
@image html fourth_2.png
