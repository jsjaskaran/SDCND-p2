## Writeup

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

### In the following steps/explanation I'll explain how I achieve the above
**All of the code is in the Jupyter Notebook in the repository and all the output images are in the output_images dir**


### Camera Calibration 
The code for this step is in the 2nd cell of jupyter notebook. I started with looking through the calibration images to determine the number of corners in the x and y direction, this turned out to be 9 by 6 respectively. Then I initialized object points with x and y, having z as 0 since we're in 2D. Using glob, I read in the images from camera_cal directory. Convert each image to grayscale and then using OpenCV findChessboardCorners function, checks if ret is true i.e. corners are found and then showing them by drawing a figure. Images are in output_images directory. Later I use calibrateCamera function from OpenCV to find the calibration matrix.

### Applying Camera Calibration
With the reprojection error, camera matrix and distortion coefficients from above step, we use OpenCV undistort function to undistort all chessboard images.

### Color transforms, gradients, etc., to create a thresholded binary image
The code for this can be found in apply_process function. The idea of this function is to use a combination of the following methods: HLS color thresholding utilizing H, RGB thresholding utilizing R, Sobel X and Y, Sobel magnitude, and Sobel directional thresholding. Using these I created a binary image.

### Perspective Transform
The code for this in perspective_transform function. It includes source and destination points which are hardcoded to generate transform using OpenCV getPerspectiveTransform. I finally generated image using warpPerspective function from OpenCV using transform generated in above step.

### Identified lane-line pixels and fit their positions with a polynomial
After that I wrote sliding window implementation and some modifications to it too. Creating a histogram of bottom half of image and then finding peaks from left and right halves. The histogram max value peaks are taken for the binary image to find where the lanes lines might reside. From there a window around this region is searched and returned for the bottom half of the image. The fit_polynomial function is then used to fit a second order polynomial to the discovered center points. The output image and resulting fitted image are returned from this function.

### Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
I wrote two different functions to do these. From the first it seemed something was wrong so searched a lot on stackoverflow and found some answers and hence update sliding window function as well finding curvature and position of vehicle function. find_curv_pos_vehicle is the function. I firstly defined conversions in x and y from pixels space to meterr by considering the physical lane is about 30 meters long and 3.7 meters wide and the pixel space of lane is about (700,720). Then fit to a second polynominal to pixel positions in each lane line. Then used below math model to calculation of R_curve (radius of curvature).
```
left_curverad = ((1 + (2*left_fit_cr[0]*y_eval*ym_per_pix + left_fit_cr[1])**2)**1.5) / np.absolute(2*left_fit_cr[0])
right_curverad = ((1 + (2*right_fit_cr[0]*y_eval*ym_per_pix + right_fit_cr[1])**2)**1.5) / np.absolute(2*right_fit_cr[0])
```

### Output Video
[Result Video](./result.mp4)

### Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
 
 One of the problem I faced was with the curvature part, had to spend a lot of time implementing and searching about that.
 I think deep learning can help with finding lane lines much better than using OpenCV.
 Similar to the first project it seems that this is also wrong approach since we're somewhat defining the Region of Interest.
 Some countries might not even have that clear lane lines as shown in images, that might be problem.
 Sliding window goes through each frame, which is time consuming and memory using. 
Will try to improve the whole technique with some of the methods read online like applying a convolution on images etc.