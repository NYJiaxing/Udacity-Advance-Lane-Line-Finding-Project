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

### Camera Calibration

#### 1. Calibrate Cameras with calibration pictures

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and the result like this ![alt text]().

In the same time I store the calibration parameters in "camera_mtx_dist.p" file to use in the future. And I test reuse the parameters in the image and the rest result as follow ![alt text]()

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I just import the parameters I got from previous step and apply the distortion correction to one of the test images like this one:
![alt text](/output_img/undistort_img.png)

#### 2. Create a thresholded binary image.  Provide an example of a binary image result.

The first step in find the binary image is transfer the RGB image to HSL space image. As we did in the lesson we can say the S channel contribute the most visible detection for the lane lines. So we just send the S channel data to the Sobel method and calaulate the gradient in x and y gradient then combine them together under certain threshold to get the final result of binary image.
Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text](/output_img/color_selection_img.png)

#### 3. Performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `bird_view_img()`, which appears in lines 1 through 8 in the file `Advance Lane finding.ipynb` (or, for example, in the 3rd code cell of the IPython notebook).  The `bird_view_img()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points then I use the 'cv2.getPerspectiveTransform()' to find the trasform matrix M.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 580, 460      | 200, 0        | 
| 280, 680      | 200, 680      |
| 1100, 680     | 1000, 680      |
| 700, 460      | 1000, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text](/output_img/bird_view_img.png)

#### 4. Identified lane-line pixels and fit their positions with a polynomial

For the lane line finding, I use the histogram hot pixle method to find the potential lane line on the picture and useing sliding windows moving upward in the image to determine where the lane lines go and remember all the lane line position. The function find_lane() is build to find all the lane line pixles on the bird-view image. The return value of this function is the pixle value of the lane line, so we use the second order polynomial to build the curve. The sliding window method is shown as below

![alt text](/output_img/find_lane_line_poly.png)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in 'Advance Lane Finding.ipynb'

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text](/output_img/final_output_img.png)

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
