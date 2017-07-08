
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

[image1]: ./writeup_images/undistort_output.png "Undistorted"
[image2]: ./writeup_images/test6.jpg "Road Transformed"
[image3]: ./writeup_images/binary_combo_example.jpg "Binary Example"
[image4]: ./writeup_images/ROI_image.jpg "ROI" 
[image5a]: ./writeup_images/warp_confirmation.jpg "Warp Example"
[image5b]: ./writeup_images/warped_straight_lines.jpg "Warp Example"
[image6]: ./writeup_images/color_fit_lines.jpg "Fit Visual"
[image7]: ./writeup_images/example_output.jpg "Output" 
[image8]: ./processed_project_video.gif "Video"
[video]: ./processed_project_video.mp4 "Video"


The code for the following steps are  contained in the IPython notebook located in "./CarND-Advanced-Lane-Lines .ipynb". 

### Camera Calibration


In code cell 2, I calculated camera matrix and the distortion coefficients using the `calculate_camera_matrix_and_disturtion_coefficients()` function. I first of all created an object point `objp` which represents a chess board fixed on the x,y plane with z = 0.Then I looped through the calibration images and applied `cv2.findChessboardCorners()` function on each image. Whenerver corners are detected I saved a copy of an object point `objp` to an object points array `objpoints` and saved the detected corners of an image to an image points array `imgpoints`.I passed the object points array `objpoints` and image points array `imgpoints` as inputs to `cv2.calibrateCamera()` function which returns camera matrix `mtx` and distortion coefficients `dist`. I applied the camera matrix `mtx` and distortion coefficients `dist` to an image using `cv2.undistort()` function and got the following result:
![alt text][image1]

### Distortion correction
In code cell 3, I applied the calculated camera matrix `mtx` and distortion coefficients `dist` to test images using `cv2.undistort()` function and below is the result on a test image:
![alt text][image2]

### Application of color transforms and gradients

From code cells 4 to 19 , I explored various colour channels and calculated sobel in the horizontal direction , sobel in the vertical direction using the `cv2.Sobel()` function and then  calculated the magnitude and direction of their gradient. Of all the colour channels, I choose L channel from the HSL color space as it performs well under changing lighting conditons and S channel from the HSV colour space as it generally perfomes well compared to other channels in identifying lane lines. I applied a threshold to the outputted binary image with minimum and maximum values set to 10 and 150 respectively.

I combined the thresholded binary image from the L channel and S channel to get the following result:

![alt text][image3]

I also applied a `isolate_region_interest()` function in code cell 20,  to isolate only lane lines in the thresholded binary image. Below is an example:

![alt text][image4]

### Perspective transform

I derived perspective tranform `M` in code cell 21, by using `cv2.getPerspectiveTransform()` function which takes in an array of source points `src` and destination points `dst`. Below are the harcoded values used for source points `src` and destination points `dst`:

```python
src = np.float32([(257, 685), (1100, 685), (583, 460),(720, 460)])
dst = np.float32([(200, 720), (1080, 720), (200, 0), (1080, 0)])
```

I then warped an image using `cv2.warpPerspective()` function , which takes in an image and perspective tranform `M` as inputs. To verify that my perspective transform was working as expected I drew the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.Below is the result:
![alt text][image5a]


Also the binary output of the warped image is shown below:

![alt text][image5b]


### Detect lane pixels and fitting with a polynomial

In code cell 24 , I applied a sliding window search using `find_window_centroids()` function and passing in a warped image as input. I then used `window_centers()` function to fit the detected lane pixels with a polynomial. Below is an example:
![alt text][image6]


### Calculate radius of curvature and position of vehicle with respect to center

In code cell 25 , I used  `pos_from_center()` function to calculate the center position of vehicle with respect to center and `get_curvature()` function to calculate radius of curvature of the lane lines.


### final image with identified lane area

In code cell 27, I used `create_overlay()` function to create an overlay and perform a perspective tranform using `cv2.getPerspectiveTransform()` function with an inverse perspective tranform `Minv` as input.The resulting overlay covers the identified lane area. Here is an example of applying the `create_overlay()` function on a test image:
![alt text][image7]

---

### Pipeline (video)

In code cell 29 , I created a pipeline to output an image with an overlay showing detected lane region and text showing lane curvature and vehicle center position.I passed each frame of the project video through the pipeline and obtained the result below:

![alt text][image8]

Here's a [link to the processed video][video]

---

### Discussion
The output from the pipeline generally performed well on straight roads and slight curves. It also perfomed well under different lighting conditions.
Though, the pipeline performs poorly on sharp curves, in situations where only one lane line is visible in the image(frame). Also its fails to differentiate between dark lines(edge of road or newly tarred sections of the road) and lane lines.It performs poorly on inclined roads.

To make a more robust pipeline, I would improve the thresholded binary image to identify only lanes lines (i.e differentiate between dark edges and lane lines). I would also improve the pipeline to be able to identify single lane lines in an image(frame). I would implement an adjustable second order polynomial fitting of lane lines for different road scenarios such as flat and inclined roads.





