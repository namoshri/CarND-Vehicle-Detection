**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image0]: ./output_images/car.png
[image1]: ./output_images/not_car.png
[image2]: ./output_images/HOG_example.jpg
[image3]: ./output_images/sliding_window.jpg
[image4]: ./output_images/sliding_windows.jpg
[image5]: ./output_images/hog_subsampling.jpg
[image6]: ./output_images/bboxes_and_heat.png
[image7]: ./output_images/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. HOG features extraction from the training images.

The code for this step is contained cells upto #6. of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image0]
![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

#### 2. Finalize HOG parameters.

I tried various combinations of parameters and based on accuracy results I choose HoG parameters. Parameters contains in cell #9.

#### 3. Training a classifier using your selected HOG features and color features

LinearSVC used for classifier based on accuracy reports suggested for image classifier for other projects.
Different features used to provide classifier like computed binned color features, color histogram features and HoG features over all channels.
I tried multiple HoG parameters to finalize them and accuracy for highest with used parameter.

### Sliding Window Search

#### 1. sliding window search

A sliding window approach has been implemented, where overlapping tiles in each test image are classified as vehicle or non-vehicle. 
Implementation in available in cell #19. 
I used x and y start and stop position to control sliding within image.
Overlap parameter used to stride through image. 
Also sliding window size parameter used to slide through in control area.
Single image extractor used to extract feature in given window and Linear SVC prediction applied on window to classify.

Verified sliding window on test image:

![alt text][image3]

#### 2. Image pipeline using sliding window and HoG subsampling

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.
Used data normalize to remove false negatives for classifier.  Further HoG subsampling also used to get better performance in feature extraction and classification.
Implementation of HoG subsampling is available in cell#25.

Here are some example of pipeline images with sliding window:

![alt text][image4]

Example of HoG subsampling:
![alt text][image5]
---

### Video Implementation

#### 1. Video Pipeline
Here's a [link to my video result](./output_images/project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions. 
I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  
I constructed bounding boxes to cover the area of each blob detected. 
To avoid false positives, I sampled last N=10 results for consecutive frames that is applied in video pipeline.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here is heatmap example :

![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. I spent lot of time issues dealing with .png, .jpg and its variation seen in image and video processing.
   I should have first converted all data files into jpg and should have used mpimg.imread for reading them for better consistency throughout.
2. There are few false negatives seen in video, however that may be mainly because incorrect labelled data and can be improved with hard data mining.
3. It took >10 min to process 50 sec video. So fps result is very lagging. This defininately not acceptable in real time. Should look for finding other approaches and also improve current pipeline etc. 
