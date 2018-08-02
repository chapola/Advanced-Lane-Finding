## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./Advanced_Lane_Lines.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 
1. Read images from camera_Cal folder using glob.glob('')
2. Convert to grayscale 
3. Create a objp points grid , Transpose and reshape it
4. find ChessboardCorners using cv2.findChessboardCorners on gray scale image
5. Add these corners to imgpoints array
6. Add objp to objpoints Array
7. Use the camera calibration method to calibrate these objpoints and imagepoints which return us coffitients matrix and dist matirx 
8. Save these matrix in a pickle file for latter use


[image1]: ./output_images/camera_calibation1.jpg "Camera Calibration"
[image2]: ./output_images/camera_calibation2.jpg "Camera Calibration"
[image3]: ./output_images/cam_undistorted_image1.jpg "Undistorted Image"



### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
[image1]: ./output_images/undistorted_image1.jpg "Undistorted Image"
[image2]: ./output_images/undistorted_image2.jpg "Undistorted Image"
[image3]: ./output_images/undistorted_image3.jpg "Undistorted Image"


To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
camera_cal/cAdvanced_Lane_Linesorner_found2.2jpg

1. Use Camera matrix and Distoration cofficients  which are calculated from 1st step
2. Read the image from test folder
3. Then Finally I call cv2.undistort() function using Camera matrix and Distoration cofficients

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.


I used a combination of color, dir_threshold, and gradient thresholds to generate a binary image in function thresholded_image() (thresholding steps at funtion abs_sobel_thresh() and dir_threshold()  in `Advanced_Lane_Lines.ipynb`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

[image1]: ./output_images/binary_image1.jpg "Binary Image"
[image2]: ./output_images/binary_image2.jpg "Binary Image"
[image3]: ./output_images/binary_image3.jpg "Binary Image"


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform  appears in code cell 147  in the file `Advanced_Lane_Lines.ipynb` .  I used the binary image as input as well as source (`src`) and destination (`dst`) points.  I chose the hardcoded the source and destination points in the following manner:

bottom_left = [320,720]
bottom_right = [920, 720]
top_left = [320, 1]
top_right = [920, 1]

dst = np.float32([bottom_left,bottom_right,top_right,top_left])

bottom_left = [320,720]
bottom_right = [920, 720]
top_left = [320, 1]
top_right = [920, 1]

dst = np.float32([bottom_left,bottom_right,top_right,top_left])


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

[image1]: ./output_images/warped0.jpg "Warped Image"
[image2]: ./output_images/warped1.jpg "Warped Image"
[image3]: ./output_images/warped2.jpg "Warped Image"


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
1. For identifying lane line pixel. First compute the Histogram. The peaks value of histgram tell the postion of lane line

2. I then perform a sliding window search, starting with the base likely positions of the 2 lanes, calculated from the histogram. I have used 9 windows of width 100 pixels.

3. Searching around a previously detected line


[image1]: ./output_images/lane_image0.jpg "Lane Image"
[image2]: ./output_images/lane_image1.jpg "Lane Image"

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

 I perform the polynomial fit in pixels and whereas the curvature has to be calculated in real world meters in funtion measure_radius_of_curvature() , we have to use a pixel to meter transformation and recompute the fit again.

The mean of the lane pixels closest to the car gives us the center of the lane. The center of the image gives us the position of the car. The difference between the 2 is the offset from the center.


I did this in lines # through # in my code in `Advanced_Lane_Lines.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `Advanced_Lane_Lines.py`   Here is an example of my result on a test image:
../test_image/tracked1

[image2]: ./output_images/tracked1.jpg "Lane Image"

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

There will be some frames where no lanes will be detected or the lanes might not make sense. We determine the bad frames if any of the following conditions are met:
No pixels were detected using the sliding window search or search around the previously detected line.
The average gap between the lanes is less than 0.7 times pr greater than 1.3 times the globally maintained moving average of the lane gap.
Averaging lanes
The lane for each frame is a simple average of 12 previously computed lanes. This is done in the get_averaged_line method in the code block below.
What to do if a bad frame is detected?
Perform a sliding window search again (this is done in the brute_search method in the code block below
If this still results in a bad frame then we fall back to the previous well detected frame.
Final Pipeline We combine all the code described in the code block above, plus the averaging and fallback techniques described in this block. The final code is in the pipeline_final method.

Video output (./project_video_output.mp4)
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
1. some problem in getting sliding window properlly. How to choose src and distnation point
2. Hickup or fluctuation in output video
3. In harder challage this pipeline is not working 
4. getting Bad frame is also very challenging  

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further. 

The pipeline seems to fail for the harder challenge video. This video has sharper turns.
 Take a better perspective transform: choose a smaller section to take the transform since this video has sharper turns and the lenght of a lane is shorter than the previous.





 
