# Finding Lane Lines on the Road

## Project Goals

The goals / steps of this project are as follows:
* Make a pipeline that finds lane lines on the road
* Produce a reflection report on the project

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

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

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
