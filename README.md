# LaneLinesDetection
This project implements a lane detection technique on a set of real life captured videos of car driving on the freeways. To do this, the libraries like OpenCV2 - for using image processing algorithms, Moviepy to generate, edit videos, Numpy for all numerical operations, Matplotlib for plotting.  

The objective of the project is to accept some input videos which will consist of left and right lanes of different colors and kind. Then process these videos and output videos which detect and annotate the lane lines with red lines on top of it. 

#### See the performance by seeing the following videos attached in the repository.

Attached Input and attached output videos correspondence:

Input Video--->output video

solidWhiteRight.mp4 ---> white.mp4

solidYellowLeft.mp4 ---> yellow.mp4

challenge.mp4 ---> extra.mp4

# **Finding Lane Lines on the Road** 


### Reflection

### 1. Pipeline description: 


#### def Process_image(img):
This function accepts an image of type jpg and performs the following image processing steps to the image: 
1. It accepts the jpg image and then converts the rgb image to a grayscale image 
2. This grayscale image is smoothened using Gaussian blur filter with kernel size of 5
3. Then to find the edges of the image, a canny edge detection filter is run on top of the grayscale image. The low_threshold and high_threshold ratio is taken as 1:3. 
4. Then initialize the vertices  which would be later used to select the region of interest
5. A mask image is created to define the region of interest and this selection is done with the help of the chosen vertices. 
6. Thereafter, we ran a Hough Transform to find lines/ segments in the region of interest (ROI) . This yields us line segments if there are broken lines on the image or detects long straight lines if the region of interest has a solid white lane or a solid yellow lane. The Hough parameters were repeatedly adjusted to smoothen the detection process.  The starting and ending points of the line segments and solid lines are stored in the variable ‘lines.’

7. Finding Slopes of the left and right lanes 
Now since we know start and end points of segments, we can find the slope of the line and determine whether the line has a positive slope or a negative slope and store them separately so that we can distinguish whether the detected line falls belongs to right lane or left lane. These slopes are needed some thresholding because we know the right and left lane would never be horizontal or exactly vertical. This would take care of any deflection that happens when there a horizontal line is detected in an image due to some shadow or any other element in the region of interest.  After thresholding of slopes, we can take the mean of the slopes of lines with positive slopes and assign it to the left lane and mean of the slopes of the lines with negative slopes and assign it to the positive lane. 

8. Finding Intercepts of the left and Right lanes
For all the slopes of the segments which passed the slope thresholding, I found the intercepts by the formula: 
   
   
   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;y1 – (slope)*x1
                        
 Now we know *mean slope* of the lanes and their *mean intercepts*.

9. Find the starting and ending points for the lanes to be drawn by selecting Y coordinates from the ROI 
The corresponding x coordinates (xpos_max_corr )  can be found by substituting the max Y coordinate, slope and the intercept on the equation: 	y = mx+C
These points should be carefully paired so that they form lane lines.  (Refer comments)

10. Smooth when there are only one lanes detected
Lastly, we check if all the frames in the movie have positive and negative lines. If yes, then draw the lane lines using the step 10, otherwise draw the lane lines that you detected in the previous frame. 

    Cv2. addweighted() function is used to draw the lane lines on top of the color image. 



### 2. Potential Shortcomings and suggestions to improvements: 
1. Currently the algorithm draws lanes on the current frame only if the hough transform finds a line with positive slope ' AND' negative slope. If it doesn't detect two lines, it draws the lanes that it detected in the previous frames. This lane detection algorithm might go off grid or might never detect lanes, if the several frames in the video are consequently have only one lane detected. The lane lines that will be drawn will be very different from what the human eye sees in that current frame. One incremental solution to this problem is to let the algorithm draw that single lane detected when the multiple consequent frames are detecting only one line in the region of interest. Apart from that if we can develop an estimator which predicts the lane lines, it will smooth the lines more. 

2. Secondly, in the challenge video, the lanes lines touch each other once. This is because at the right turn the hough transform is connecting a dot from the left lane line with the a point in the right lane line. This can be smoothened by tuning the hough transform parameters. This can be eliminated by reducing the max gap parameter in Hough Transform. 


