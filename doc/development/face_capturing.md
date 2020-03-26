# Capturer Class Reference

To capture faces, you should create a `Capturer` object using `FacerecService.createCapturer`, passing the path to the configuration file or the `FacerecService.Config` object. If you pass the path to the configuration file, the default settings will be used. By using `FacerecService.Config` you can override any numerical option inside the config file. Also, some parameters can be changed in the existing `Capturer` object with the `Capturer.setParameter` method.

Example 1: 

**C++**: ```pbio::Capturer::Ptr capturer = service->createCapturer("common_capturer4.xml");```  
**C#**: ```Capturer capturer = service.createCapturer("common_capturer4_lbf.xml");```  
**Java**: ```final Capturer capturer = service.createCapturer(service.new Config("common_capturer4_lbf.xml"));```  

Example 2: 

**C++**

```cpp
pbio::FacerecService::Config capturer_config("common_capturer4.xml");
capturer_config.overrideParameter("min_size", 200);
pbio::Capturer::Ptr capturer = service->createCapturer(capturer_config);
```

**C#**

```cs
FacerecService.Config capturerConfig = new FacerecService.Config("common_capturer4_lbf.xml");
capturerConfig.overrideParameter("min_size", 200);
Capturer capturer = service.createCapturer(capturerConfig);
```

**Java**

```java
FacerecService.Config capturerConfig = service.new Config("common_capturer4_lbf.xml");
capturerConfig.overrideParameter("min_size", 200);
final Capturer capturer = service.createCapturer(capturerConfig);
```

Example 3:

**C++**

```cpp
pbio::Capturer::Ptr capturer = service->createCapturer("common_capturer4.xml");
capturer->setParameter("min_size", 200);
capturer->setParameter("max_size", 800);
// capturer->capture(...);
// ...
capturer->setParameter("min_size", 100);
capturer->setParameter("max_size", 400);
// capturer->capture(...);
```

**C#**

```cs
Capturer capturer = service.createCapturer("common_capturer4_lbf.xml");
capturer.setParameter("min_size", 200);
capturer.setParameter("max_size", 800);
// capturer.capturer(...);
// ...
capturer.setParameter("min_size", 100);
capturer.setParameter("max_size", 400);
// capturer.capture(...);
```

**Java**

```java
Capturer capturer = service.createCapturer(service.new Config("common_capturer4_lbf.xml"));
capturer.setParameter("min_size", 200);
capturer.setParameter("max_size", 800);
// capturer.capturer(...);
// ...
capturer.setParameter("min_size", 100);
capturer.setParameter("max_size", 400);
// capturer.capture(...);
```

The type and characteristics of the capturer depend on the configuration file or the `FacerecService.Config` object passed to the `FacerecService.createCapturer` member function.

We recommend you to use the following configuration files:

* `common_capturer4_fda.xml` – our own frontal face detector, which works much better than the detectors from open source libraries, such as OpenCV or Dlib; 
* `common_video_capturer_fda.xml` – our own frontal face video tracker, works only with colored images.

_**Note:** We recommend you to use `VideoWorker` for face tracking on video streams. When `VideoWorker` is created with `matching_thread=0` and `processing_thread=0`, then the standard [Face Detector license](../components.md) is used._

The used sets of anthropometric points (see Anthropometric Points):

|config file|points set|
|-----------|----------|
|common_capturer4_fda.xml |fda|
|common_video_capturer_fda.xml|fda|

