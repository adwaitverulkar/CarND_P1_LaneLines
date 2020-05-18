# Finding Lane Lines on the Road

## Project Goals

The goals / steps of this project are as follows:
* Make a pipeline that finds lane lines on the road
* Produce a reflection report on the project

## Reflection

### 1. Lane Detection Pipeline

My pipeline consisted of 8 steps.

1. Convert image to grayscale
2. Apply gaussian filter
3. Detect edges using Canny function
4. Add a mask to filter out unwanted edges
5. Apply Hough transform to extract lines from edge pixels
6. Separate left and right lane lines based on slope
7. Fit a single line through each side of lane
8. Overlay best-fit lane lines over original image

Steps 6 and 7 are performed in the `draw_lines` function.

### 2. Lane Line Regression and Extrapolation

The following steps have been implemented in the draw_lines function.

#### I. Left and right lane segregation

After applying Hough Transform, we get a 2D array containing the endpoints of the detected lines. The slope of these lines is calculated using the following code.

```
if (x2 != x1):
	slope = ((y2-y1)/(x2-x1))
```
Sometimes Hough transform might return lines that have infinite slopes. Separate code can be written to draw such lines, but in this project such lines have been omitted by the condition stated above.

Based on how the coordinate frames on an image are positioned, the left lane line should have a negative slope while the right lane line will have a positive slope. Segregated points can be appended to different arrays for left and right lane abscissa and ordinates.

#### II. Fitting a single line through each lane

The previous step will give a cluster of points representing the left and right lanes. However, a single best estimate of the lane position is required. This can be done using the `polyfit` function in NumPy, by providing a polynomial of degree 1.

```
left_coeffs = np.polyfit(x_left, y_left, 1)
right_coeffs = np.polyfit(x_right, y_right, 1)
```

Higher order polynomials can be used to better represent the lane lines, but for the sake of simplicity, this has not been implemented in the project.

#### III. Extrapolating lane lines

After getting the coefficients of the lane lines, the endpoint coordinates of the detected lane can be calculated. The lines have been made to extend from the bottom of the image to the topmost point detected after Hough transform. For left lane lines, the code is as follows.

```
y1 = img.shape[0] # Extrapolate to the end of image
x1 = int((y1 - left_coeffs[1])/left_coeffs[0])
y2 = min(y_left) # Extrapolate to farthest point detected
x2 = int((y2 - left_coeffs[1])/left_coeffs[0])
```

The lines can be extrapolated to a fixed horizon, but by doing so a very small angular error in lane detection will result in significant deviation from the actual lane position.


### 2. Shortcomings and Improvements

A prominent shortcoming identified for this pipeline was detection of "false positives". Specifically, on the video "solidYellowLeft.mp4", there are frames where lines other than lanes are present and these are picked up as lanes with slopes drastically different from the lane line slopes. There are several workarounds possible, the simplest of which has been implemented in the project. A threshold on slope has been applied for any detected line to be classified as a lane, and lines below this threshold are not drawn on the image.

```
if((x2 - x1) > 0 and (y2-y1)/(x2-x1) < -0.5):
	cv2.line(img, (x1, y1), (x2, y2), color, thickness)
```

This leads us to another shortcoming, which is a possible consequence of the above solution. In some frames, no lines are drawn. This might be because the lines are not above the implemented threshold or it might be because no lines were detected in the first place after Hough Transform. This might be due to stringent Hough Transform Parameters. Possible improvements include, relaxing the Hough Transform Parameters. Another approach would be to maintain a history of the previous frame lane line characteristics and using this information to estimate the lane line position in the next frame instead of performing a blind search in each frame.

Another major improvement would be representation of lane lines using higher order polynomials like a quadratic or a cubic spline. This would help significantly on the challenge video, as the lanes in that video are curved.
