# **Finding Lane Lines on the Road** 

## Writeup (Bryant Pong)

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My image pipeline performed the following steps:

1) Convert the input image from RGB to Grayscale
2) Perform a Gaussian Blur on the grayscale image with a kernel size of 5 to
   remove noise and smooth the image.
3) Perform Canny Edge Detection
4) Using the GIMP Image Program, I found 4 vertices that form a trapezoidal
   region of interest.  In this step this ROI is applied to the image.
5) Finally, the Hough Transform is performed and the resulting lines are
   averaged then drawn onto the left and right lanes.           

To average out the lines, I needed to come up with a way to generate the
line equation y = mx + b.  First, I took all the lines found by the Hough
Transform and eliminated any vertical lines or lines that was close to
being horizontal.  Next, I computed the slope of the remaining lines.  Since
the image coordinate system has the origin (0,0) in the upper-left corner,
lines with a positive slope belong to the right lane while lines with a 
negative slope belong to the left lane.  These lines were separated into
two lists (one for left and one for right).

Next, the position of the average line was computed for both the left
and right lanes.  This was done by calculating the average (X1, Y1) and
(X2, Y2) positions.  From these average calculations, the average slope (m)
could be found by the equation ( Y2 - Y1 ) / ( X2 - X1 ).

Next, the b (y-intercept) term needed to be computed for both lanes.  This
was done by rewriting the y = mx + b equation to the form b = y - mx.  We
already know the slope (m), so it is a simple matter of plugging in
(avgX1, avgY1) into this equation and solving for b.

With this information, we now have an equation to draw the average line for
both the left and right lanes.

The final piece of information we need is the starting and ending positions
of the line.  We know from the notebook that we need to draw a line from the
bottom of the image to the top of the ROI.  Therefore, we know that for both
lines, the bottom Y value is the height of the image (in our case, 540 pixels)
and the top Y value is the top of the ROI (in my case, Y = 330).  Using
our equation of the line, we can solve for the starting and ending X values
by rewriting the line equation into x = ( y - b ) / m.  

### 2. Identify potential shortcomings with your current pipeline

One shortcoming with the current pipeline lies in the fact that we are
using a line to mark lane lines.  Since lane lines can curve around turns,
using only a linear function to represent the lane line is inadequate to
handle these turns.    

Another shortcoming is in handling different ambient lighting conditions.
All of our sample videos and images are in broad direct sunlight (almost
ideal conditions from our camera).  However, if we were driving at sunset,
we will have less light and our lane lines may darken.  As a result, there
is a chance that the Canny Edge Detector may not identify as many edges
in poorer lighting conditions.       


### 3. Suggest possible improvements to your pipeline

To handle shortcoming 1, we can use a higher-order function to represent
our lanes.  In computer graphics, cubic splines are a popular representation
for lines; they give a much more flexible way to handle curves.  By
constructing a cublic spline when drawing lines, we can now handle curves
better.      

To handle the case of poor lighting or intense sunlight (i.e. driving towards
the sunset), dynamic thresholding can be implemented.  We can look at the
intensity of the input image and adjust the pixel intensities appropriately.      
