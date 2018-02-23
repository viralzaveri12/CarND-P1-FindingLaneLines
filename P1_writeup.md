# **Project 1 - Finding Lane Lines on the Road** 

---
**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report
---
[//]: # (Image References)

[image1]: ./2_test_images_Blur/whiteCarLaneSwitch.jpg "Gaussian Blur"
[image2]: ./3_test_images_Canny/whiteCarLaneSwitch.jpg "Canny Edge Detection"
[image3]: ./4_test_images_MaskedROI/whiteCarLaneSwitch.jpg "Masked Region of Interest"
[image4]: ./6_test_images_MergeRaw/whiteCarLaneSwitch.jpg "Hough Transform and Merge with Raw Lines"
[image5]: ./test_images_output/whiteCarLaneSwitch.jpg "Hough Transform and Merge with Solid Lines"

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Below are the steps to develop a pipeline that reads test images and videos and processes them with functions from Computer Vision library to eventually identify lane lines on the road. My pipeline consisted of 5 steps:

1. First, convert the images to grayscale and then apply Gaussian Blur. For the parameters, I used kernel_size = (5, 5).

![alt text][image1]

2. Next apply Canny edge detection. In line with the recommended 2:1 or 3:1 ratio of thresholds, I used low_threshold = 50 and high_threshold = 150.

![alt text][image2]

3. Then apply a mask to the image output of Canny edge detection. I used a simple trapezoid mask to obtain the region of interest.

![alt text][image3]

4. Apply Hough Transform to the masked image and merge to obtain raw lines on the image. I used values of rho = 2, theta = np.pi/180, threshold = 20, min_line_len = 50, and max_line_gap = 25.

![alt text][image4]

5. Lastly, apply averaging and extrapolation on the raw lines obtained from Hough transform to form a single line on the left and right lanes.

![alt text][image5]

After applying Hough transform, the draw_lines() function draws multiple lane lines. Also, some lane lines are only drawn partially. So, in order to draw a single line on the left and right lanes, I added the lane_lines() function as described below:
1. The lane_lines() function reads the raw lines created by Hough transform in Step 4.
2. A left lane should have a positive slope and a right lane should have a negative slope. However, since the y-axis is reversed, therefore, left lane would have a negative slope and a right lane would have a positive slope. 
3. Based on the slopes, collect left or right lines separately. Select higher slope threshold to filter out horizontal lines.
4. The lane_lines() function calculates the average slope and intercept for left and right lanes of each image.
5. To draw the lane lines, convert the slope and intercept into x and y coordinate points.


### 2. Identify potential shortcomings with your current pipeline

The output of the pipeline for the challenge video in this project itself suggests that this is a simple lane detection pipeline. Thus, it has a few potential shortcomings.

In the challenge video, road is slightly curved, has shadows from trees, road changes from concrete to asphalt (change in road color gradient), and video shows hood of the vehicle. Because of these factors, raw lines from Hough transform are all over the place. However, with averaging and extrapolation manipulation, final lane lines are far better with respect to tracking the lane lines but still slightly unstable.

1. This shows a better approach is needed to develop the pipeline for roads with curved lane lines and more tools to correctly identify shadows and road gradients.

2. The pipeline is not tested on images or videos with low lighting (night time driving) or in rainy conditions when roads are highly reflective and lanes are difficult to spot.

3. Also, how the pipeline would work if the lane lines on the road are faded has not been tested.

4. What about heavy traffic conditions, or an oversized truck in front of the vehicle, or a vehicle in adjacent lane driving on the lane marking making the lane lines difficult to spot?


### 3. Suggest possible improvements to your pipeline

1. To accurately detect the lane lines on a curved road, and if there is a way to incorporate steering wheel or wheel orientation as input, I would use a dynamically changing polygon region of interest. This would help eliminate any other lane markings being captured in a trapezoidal polygon not intended for our vehicle.

2. This pipeline uses a straight line fit even for the curved roads, which does not cover the lanes to full extent. Thus, to accurately detect the lane lines on a curved road, I would use some curve line fitting approach.