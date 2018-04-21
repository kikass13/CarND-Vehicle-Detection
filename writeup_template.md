## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # "Image References"
[image1]: ./output_images/HOG.png
[image3]: ./output_images/slide_window.png
[image4]: ./output_images/findCars.png
[image5]: ./output_images/heatmapLabels.png
[video1]: ./project_video.mp4



### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the output looks like.

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters in the classroom.

* orientations = 9 
* * is suitable enough, hving more directions does not benefit our hog image directly 
* pix_per_cell = 8 
* * same parameter as the one chosen in the classrom, having a small cell-size leads to a stronger discretized hog image, which i found usefull (small cars in a distance)
* cell_per_block = 2 
* * same parameter as the one chosen in the classroom

After playing around with the color spaces, i chose to mainly use the YUV space.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the given lecture code. i played around with the parameters until the accuracy was high enough and consistent.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?



As described in the lecture, i created a gridmap of windows, which slightly overlap each other (in this case by about 80% left and 80% top). The resulting windows were then fed to the classifier, which labeled them with car or non-car. each resulting car-window was drawn onto the original image to check wether or not the classifier works.  

![alt text][image3]

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. I also chose to create multiple sizes of the windows, to get a better representation of the different scales of cars (dependent on the distance). Here are some example images of the different :

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions. I also implemented a ringbuffer for the last 20 bounding boxes, so that my resulting heatmap took a little history of boundingBoxes into account. The history removes detected vehicles slower, but creates a consisten Box around vehicles without flickering.



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Because of the heatmap and the implemented ringbuffer, new classified vehicles will not die instantly. Also noise will get perpetuated and can distract from real observations. The different Image sizes for the sliding window are not properly configured, whereby different cameras (different FOV) will fully destroy the implementation of the window sizes.
