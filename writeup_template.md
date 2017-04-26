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

[img1]: ./images/chessboard_corners.png "Chessboard Corners"
[img2]: ./images/undistort_chessboard.png "Undistorted Chessboard"
[img3]: ./test_images/test3.jpg "Test3.jpg Test Image"
[img4]: ./images/pipeline_undistorted.png "Undistorted test3.jpg"
[img5]: ./images/pipeline_sobelx.png "Sobel X"
[img6]: ./images/pipeline_sobely.png "Sobel Y"
[img7]: ./images/pipeline_grad-magn.png "Gradient Magnitude Threshold"
[img8]: ./images/pipeline_hls.png "HLS S-Color Threshold"
[img9]: ./images/pipeline_binary_combined.png "Binary Pipeline Combined"
[img10]: ./images/perspective_undistorted_annotated.png "Perspective Undistored Annotated"
[img11]: ./images/perspective_warped_annotated.png "Perspective Warped Annotated"
[img12]: ./images/result.png "Result"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

An example of the result of finding the chessboard corners is:

![alt text][img1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][img2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][img3]

And the corrected version:
![alt text][img4]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I implemented the following threshold methods:
##### a. Sobel X
The code can be found in the abs_sobel_thresh method in the 3rd cell. The result is:
![alt text][img5]
##### b. Sobel Y
The code is the same method as for the Sobel X, except that the parameter 'orient' should be 'y'
![alt text][img6]
##### c. Gradient Magnitude Threshold
The code can be found in the mag_thresh method in the 3rd cell. The result is:
![alt text][img7]
##### d. HLS S-Channel Threshold
The code can be found in the threshold_binary method in the 3rd cell. The result is:
![alt text][img8]

The combination I used is:
* HLS S-channel threshold with a threshold of (170, 255)
* Sobel X operator with a kernel of 3 and threshold of (20, 100)
And then apply the OR operator.
The code can be found in the threshold_binary method in the 3rd cell.

The resulting combination is:
![alt text][img9]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspectiveTransform()`, which appears in  the 3rd cell of the `code.ipynb` file. I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[200,img_size[1]],
    [590,450],
    [695,450],
    [1150,img_size[1]]])
dst = np.float32(
    [[200,img_size[1]],
    [200,0],
    [1000,0],
    [1000,img_size[1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 200, 720      | 
| 590, 450      | 200, 0        |
| 695, 450      | 1000, 0       |
| 1150, 720     | 1000, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

* Undistored image with source points:
![alt text][img10]
* Warped image with destination points:
![alt text][img11]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The pipeline has been implemented on two locations:
* in cell 6 the manual steps to visualize every image
* in cell 7 the pipeline used for the video

![alt text][img12]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_annotated.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
