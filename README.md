# Finding Lane Lines on the Road

The goal of this project is to detect lane lines on the road using a video feed.  The code will analyze each image frame in the feed and use computer vision techniques to detect the location of the lane lines.

Below is some examples of the code output:

![lane lines](/test_images_out/Image0_lanes.jpg "Lines")
![lane lines](/test_images_out/Image0_zones.jpg "Lines")

A few assumptions were taken when putting the project together:
* The line color is yellow or white, any lane lines other than this will not be detected
* Assumes the car is in a static, central location in a lane and moving forward.  If the car changes lanes or approaches an intersection where lane lines are not parallel with the forward motion of the car, the program may behave erratic

The code used is Python 3.5 and the following techniques are used for line detection:
* Color Selection
* Canny Edge Detection
* Region of Interest Selection
* Hough Transform Line Detection

***
The following Python libraries are required for the project:
* matplotlib.pyplot
* matplotlib.image
* Numpy
* OpenCV
* glob
* deque

***
## Reflection

The code was built using object oriented design.  Therefore a handful of classes are available for the video processing pipeline.

1. ImageHandler: this class is used to read, display and process functions on images.  It is built to handle an array of images.
2. ImageProcessor: Used to run the OpenCV processing functions on images.  This will prepare the images for the line detection
3. LineDetector: This class uses OpenCV functions to detect lines from images processed with the ImageProcessor class
4. LineProcessor: This class has functions that accept the raw lines detected in the LineDetector class and runs extrapolation algorithms on them.  Approximate lane locations are found using this class.
5. LaneDetector: This class uses the ImageProcessor, LineDetector and LineProcessor classes to detect and draw lane lines on images.
6. VideoProcessor: This class has functions to read a video file and process each frame as an image through the LaneDetector class.

***

The current code can process and output the following:
* Raw (segmented) lane lines
* Extrapolated approximate lane line locations
* Smoothing on extrapolated lines
* The center point between lane lines
* The area between two lane lines

***
The following is an example of the image processing pipeline that is used on each frame of a video input:

1. An image is read in its raw form.

![lane line image](/test_images_out/Image3.jpg "Lines")

2. The ImageProcessor class masks the image into the HSL Color space.  This helps mask out any changes in road color, such as when crossing a bridge, and focuses on the lane lines themselves.

![lane line image](/test_images_out/Image3_hls.jpg "HSL")

3. The ImageProcessor class then masks the HSL color image to only yellow and white hues.  This focuses out everything but lane lines.

![lane line image](/test_images_out/Image3_white_yellow.jpg "White Yellow")

4. The ImageProcessor class then uses Canny edge detection to find line edges.

![lane line image](/test_images_out/Image3_edges.jpg "Edges")

5. The ImageProcessor masks the region where the lane lines would be located, getting rid of any extra data such as road signs or bridges.

![lane line image](/test_images_out/Image3_roi.jpg "Region of Interest")

6. The LineDetector class uses Hough transform to get a list of line vertices where each edge was detected in the Canny algorithms.

![lane line image](/test_images_out/Image3_lines.jpg "Raw Lanes")

7. The LineProcessor class iterates through each line detected and calculates the slope to determine if the line is associated with the right or left lane line.  The function returns the longest lines detected for both right and left lane lines.  This line formula is then used to extrapolate the approximate location of each lane line.

![lane line image](/test_images_out/Image3_lanes.jpg "Lanes")

8. The LaneDetector class uses the ImageProcessor, LineDetector and LineProcessor classes to detect and draw lane lines on images.
9. The VideoProcessor class has functions to read a video file and process each frame as an image through the LaneDetector class.

***

### Potential Shortcomings

* The project works very well with daytime and clear weather video feeds.  Night time or poor weather conditions may cause the lane detecting algorithm to act erratic.  
* The algorithm may not work well during lane change transitions.
* Issues may be observed in urban driving conditions, where there are many road markings, like crosswalks or intersections.
* If a vehicle is nearby, the lane may be obscured which could show unpredictable results.

***

### Future Improvements

Further testing with different driving conditions would be the next step.  Testing at night with headlights or rainy conditions could expose new ways the project could be improved.  

A solution to monitoring lane changing and recognizing various road markings could be added, as well.  A thought on monitoring lane changing would be adding more masking regions to detect parallel lanes.  Also, line slope could be taken into account to make classification algorithms for lane lines vs other types of road markings.