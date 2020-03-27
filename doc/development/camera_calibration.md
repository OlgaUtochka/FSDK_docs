# Camera Calibration and Correction of Distortion

The CameraCalibrator object is used for camera calibration and correction of image distortion.

It can be created by calling FacerecService.createCameraCalibrator.

To correct the distortion, you must either perform a calibration or just load the calibrated parameters using the CameraCalibrator.loadCameraParameters member function.
After that, you can correct the image distortion using the CameraCalibrator.undistort member function.

For calibration you need to print a calibration template and fix it on a flat surface, preferably black.

There are three pattern types:

    asymmetric circles grid pattern
    chessboard pattern
    circles grid pattern

Here are examples of the patterns:

asymmetric circles grid pattern (rotated) 	chessboard pattern 	circles grid pattern
acircles_pattern_w4_h11_small.png
	
chessboard_pattern_w9_h6_small.png
	
circles_pattern_w12_h8_small.png
width 4
height 11 	width 9
height 6 	width 12
height 8

Full size images are stored in the share/calibration directory.

We recommend you to use the asymmetric circles grid pattern.

Before calibration you must initialize the CameraCalibrator object by calling the CameraCalibrator.initCalibration member function.

Then you must capture the pattern in different poses by the camera you want to calibrate and provide the captured frames in the CameraCalibrator.addImage.

You can get the approximate progress of pattern pose space coverage by the CameraCalibrator.getPatternSpaceCoverProgress member function.

You can get a tip what pattern pose is required using the CameraCalibrator.getTip member function.

Then call the CameraCalibrator.calibrate method.

After a successful calibration, you can see the result by calling the CameraCalibrator.undistort method. Then you can save the calibrated parameters using the CameraCalibrator.saveCameraParameters method, if the result suits you, or add more images and perform a calibration again.

To reset calibration, call the CameraCalibrator.initCalibration method again.
