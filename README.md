<B>Camera Calibration - Mono Camera</B>

First we need to create a camera node and publish the images on the master.

install usb_camera using 
<code>
	sudo apt-get install ros-*distro*-usb-cam
</code>

go to working directory and create a launch file named camera.launch 

<code>
	< launch>
  	 	 < node name="camera" pkg="usb_cam" type="usb_cam_node" clear_params="true" output="screen">
    	    < param name="video_device" value="/dev/video#ID"/>
        	< param name="image_width" value="640"/>
	        < param name="image_height" value="480"/>
    	    < param name="framerate" value="60"/>
        	< param name="pixel_format" value="yuyv" /> 
 	       < param name="brightness" value="30"/>
    	</ node>
	</ launch>
</code>
 
/dev/video#ID denotes device ID of your connected camera.
Adjust the params accordingly to get acceptable image.

source setup.bash 
<code>
	source devel/setup.bash
</code>
run the file using 

<code>
	roslaunch camera.launch
</code>

You should have camera running and publishing the images.


<B>cameracalibration - ROS</B>

We use ROS inbuilt cameracalibrator node to calibrate our camera.

For the above launch file and camera calibration using a 8x6 chessboard with 108mm squares

<code>
	rosrun camera_calibration cameracalibrator.py --size 8x6 --square 0.108 image:=/camera/image_raw camera:=/camera
</code>

Take the pics and click on calibrate to calibrate the camera.
Click save to save the camera configurations.


<B>OcamCalib - Matlab</B>

Install Matlab and download the OcamCalib source from 

http://www.vision.caltech.edu/bouguetj/calib_doc/download/toolbox_calib.zip

Extract the zip file.

Click pics of the calibration pattern and save them under the same folder.

run calib_gui , select anyone of the option "Standard" or "Memory efficient" 

On the next dialog first select the "Read Images"

Select "Extract Grid Corners" and assign window X & Y size : default 5.

Press Enter for automatic grid detection

Select the corner on each picture and set the distortion , if any.

Once done click on Calibration

Data will be displayed on the screen.

Click "Save" to save the .mat file.



<B>PTAM Calib</B>

clone the package in your ROS working directory
<code>
	git clone https://github.com/ethz-asl/ethzasl_ptam
</code>

build the project using 
<code>
	catkin_make
</code>

Note:In case of any error.
	You will need OpenCV 2.4.8 for this part.
	If you have any other version then copy all the missing files from /usr/local/lib/ to the specified directory and renaming the files to proper version.
	Example : If you have OpenCV v2.4.9 copy /usr/local/lib/libopencv_stitching.so.2.4.9
	to /usr/lib/x86_64-linux-gnu and rename it to libopencv_stitching.so.2.4.8

Edit cameracalibrator.launch file accordingly and PtamFixParams.yaml according to your camera.

Example 
cameracalibrator.launch
<code>
	< launch>
    	< node name="cameracalibrator" pkg="ptam" type="cameracalibrator" clear_params="true" output="screen" >
	    	< remap from="image" to="/camera/image_mono" />
      	      < remap from="pose" to="pose"/>
	 	   < rosparam file="$(find ptam)/PtamFixParams.yaml"/>
    	</ node>
	</ launch>

</code>

Run the camera.launch file which we prepared earlier and then launch camera calibrator

<code>
	roslaunch ptam cameracalibrator.launch
</code>

Place the calibration pattern in front of camera and wait for proper grid drawn on the pattern.
Capture around 20 images and click on "Optimize"
View all the images and save the camera info once error is minimum.





<B>Stereo Calibration Using TOOLBOX CALIB - MATLAB</B>


Install Matlab and download the TOOLBOX CALIB source from 

http://www.vision.caltech.edu/bouguetj/calib_doc/download/toolbox_calib.zip

Extract the zip file.

Click pics of the calibration pattern from both cameras and save them under the same folder.

run calib_gui , select anyone of the option "Standard" or "Memory efficient" 

On the next dialog first select the "Read Images"

Give the basename for your file ( eg : Left or Right )

Select "Extract Grid Corners" and assign window X & Y size : default 5.

Press Enter for automatic grid detection

Select the corner on each picture and set the distortion , if any.

Once done click on Calibration

Data will be displayed on the screen.

Click "Save" to save the .mat file.

Rename the Calib_Results.mat to Calib_Results_left.mat and Calib_Results_right.mat for Left and Right cameras respectively.

Run stereo_gui

Select "Load left and right calibration files"

Enter name for two .mat files

Click "Run stereo calib"

after calibration save the results.

Calibration_Results_stereo.mat will be generated.


