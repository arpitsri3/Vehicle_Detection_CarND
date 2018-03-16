## Writeup Report

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./Visualization_P5/Vis1.png
[image2]: ./Visualization_P5/Vis2.png
[image3]: ./Visualization_P5/Vis3.png
[image4]: ./Visualization_P5/Vis4.png
[image5]: ./Visualization_P5/Vis5.png
[image6]: ./Visualization_P5/Vis6.png
[image6]: ./Visualization_P5/Vis7.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

For the project, as per the suggesstions in the classroom, I have played around with hog-features, spatial-features and histogram-features to train the classifier. The implementations for the same may be found from cells 6-9.

There are a couple of computational constraints which I had to keep in my mind while choosin a feature set which could produce decent results while being computationally less intensive:

1. The machine I had to work on has a really bad configuration.
2. Limited memory.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image2]

Here's also a visualization of the test images which the classifier had to predict cars in:

![alt text][image1]

Keeping in mind the constraints above and by chceking the number of features on using the three methods individually and also in combination, I observed that a largeer number of features or a 99% test accuracy doesn't gurantee that the result would definitely be better. After my number of features jumped to around 8000+ on combining the three and upto 2000-4000 on using a combination of two (while being not that much useful in improving the end result (on the test images)), increasing the feature extraction time too, I tried with only hog-features with a high pixel value per cell value. Thus, I could scale down my deatures drastically to 324 while also ensuring a better time period for training and pipeline.

I experimented, using- YUV, YCrCb and RGB colorspaces, with 'ALL' channels. The results being better with YUV colorspace i have used it in the final implementation. 

Here is an example of applying 'Hog' on the test images and one of a vehicle:

![alt text][image3]

![alt text][image7]

#### 2. Explain how you settled on your final choice of HOG parameters.

I have to admit that tuning the parameters was not easy but it was kind of nice to get an intuition on what would work, which is possibly one of the aims of this project.

I have used the following:

colorspace = 'YUV' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 12
pix_per_cell = 18
cell_per_block = 3
hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

As I mentioned before one of my aims was to keep the number of features as low as I can while maintaining a good accuracy of the classifier. I have this kind of habit/intuition where i try multiples of 3 at start and try to tune them. After hit and trial with the following:

colorspace = 'RGB' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 12
pix_per_cell = 9
cell_per_block = 3
hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

colorspace = 'YCrCb' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 12
pix_per_cell = 9
cell_per_block = 3
hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

colorspace = 'YUV' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 12
pix_per_cell = 18
cell_per_block = 3
hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

and

colorspace = 'HLS' # Can be RGB, HSV, LUV, HLS, YUV, YCrCb
orient = 12
pix_per_cell = 8
cell_per_block = 2
hog_channel = "ALL" # Can be 0, 1, 2, or "ALL"

I finally settled for the ones I have mentioned above as they gave me a good accuracy of 97% while keeping the feature vector value to 324.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

Again, I didn't try with 'rbf' kernel as I have read on the slack channel (and the classroom and stackoverflow :) ) that its relatively more computationally intensive as compared to Linear SVM with default values. 

The code for this is in the cells 10 and 11. I have only used 'hog-featues' for training.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I have adapted the same code as given in the classroom under the 'Hog-Sub-Sampling Search' section for the project. I have done the search and predict on three regions of the image with three different ystart and ystop values and 3 different scales, to detect both close and a little far vehicles. Here are the values:

ystart1 = 400
ystop1 = 656
scale1 = 1.5
    
ystart2 = 400
ystop2 = 500
scale2 = 1
    
ystart3 = 400
ystop3 = 450
scale3 = 0.9
    
ystart4 = 450
ystop4 = 500
scale4 = 0.9


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Here's a visualization of the output on test images:

![alt text][image4]

![alt text][image5]

![alt text][image6]

To optimize the performance while managing the hardware constraints, I have kept feature vector length low at 324.

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
My video can be found at (./test_videos_output/project_video.mp4)(With frame averaging on heatmaps (frames=9) ). I have also kept some video outputs at different stages for reference:
1.Without heatmap without multiscale
2.With Heatmap without scale
3.Without Heatmap with scale


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I ahve implemented this in the same way as suggested in the 'False/Multiple detections' lesson of the class.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps,  the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames and the resulting bounding boxes are drawn onto the last frame in the series:

![alt text][image4]

![alt text][image5]

![alt text][image6]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The biggest issue that I faced was finding a combination which would work decently on the hardware i had. Although I am personally not that happy with the result but I am kind of happy that the output video (although looses tracking on the white car for some 2 seconds at one point and flickers) turned out to be better than i had hoped to achieve with the constraints, considering only 300+ features. Since my initial result had a lot of false positives I had to run on multiple scales and then apply a heatmap threshold of 3 to weed out the false positives.

The result has a lot of room for improvement and hopefully i will try to improve it as soon as i have access to better configuration. 

