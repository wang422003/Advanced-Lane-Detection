
# Advanced Lane Finding Project

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

[image1]: ./output_images/Cali.png "Undistorted"
[image2]: ./output_images/im.png "Road Transformed"
[image3]: ./output_images/Thresholding.jpg "Binary Example"
[image4]: ./output_images/bi.png "Warp Example"
[image5]: ./output_images/Sliding_Windows.png "Fit Visual"
[image6]: ./output_images/Image_with_Detected_Area_and_Curvature.png "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "Advanced Lane Finding Project.ipynb" (or in lines 18 through 96 of the file called `Advanced Lane Finding Project.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at pipeline 'Gradient and Color Thresholding' in `Draft_Project.ipynb`).  Here's an example of my output for this step.  (note: this is not actually from one of the final project file)

![alt text][image3]

Instead, I used a combination of L-Threshold and LAB B-Threshold. Because this combination provides a good result on detecting both lanes. And the code in the project still leaves space for changing the thresholding method.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp()`, which appears in lines 121 through 129 in the file `Advanced Lane Finding Project.py` (Advanced Lane Finding Project.py) (or, for example, in the 7th code cell of the IPython notebook).  The `unwarp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points, it outputs the transform matices `M` and `Minv`. The former is used to get transformed image and the other one is used to transform the image back to its original perspect.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([(575,464),
                  (707,464), 
                  (258,682), 
                  (1049,682)])
dst = np.float32([(450,0),
                  (w-450,0),
                  (450,h),
                  (w-450,h)])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 575, 464      | 450, 0        | 
| 707, 464      | w-450, 0      |
| 258, 682      | 450, h      |
| 1049, 682      | w-450, h        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines 765 through 803 in my code in `Advanced Lane Finding Project.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 818 through 883 in my code in `Advanced Lane Finding Project.py` in the function `draw_lane()` and `draw_data()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./Output_Videos/project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In this project, the processing on the normal video and the challenge video both are good, but it failed on harder challenge video. The problem may be the L-thresholding method and the setted detecting interval of the lanes. The lightness in the harder challenge video changed a lot, it might affect the outcoming if L-threshold. And sometimes one of the lanes disappeared in the view because of the large steering angel. 

To handel this problem, I might choose a combination of L-threshold, S-threshold and LAB B-threshold. And I would like to set the interval larger than it was and enable the disappearance of the lane.
