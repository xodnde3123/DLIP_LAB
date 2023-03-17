# LAB: Grayscale Image Segmentation

Segment and count each nuts and bolts

​                                                                                                                                                                                                                                21700384 SongTaewoong

# I. Introduction

**Goal**: Count the number of nuts & bolts of each size for smart factory

There are 2 different size bolts and 3 different types of nuts. You are required to segment the object and count each parts

- Bolt M5
- Bolt M6
- Square Nut M5
- Hexa Nut M5
- Hexa Nut M6



![img](https://raw.githubusercontent.com/ykkimhgu/DLIP-src/main/LAB_grayscale/Lab_GrayScale_TestImage.jpg)



After analyzing histogram, applying thresholding and morphology, we can identify and extract the target objects from the background by finding the contours around the connected pixels.



# II. Procedure

You MUST include all the following in the report. Also, you have to draw a simple flowchart to explain the whole process

- Apply appropriate filters to enhance image
- Explain how the appropriate threshold value was chosen
- Apply the appropriate morphology method to segment parts
- Find the contour and draw the segmented objects.
  - For applying contour, see Appendix
- Count the number of each parts



# III. Preprocessing



## 1. Histogram

First, to apply an appropriate filter, the given image was converted to gray-scale and histogram was analyzed.

| ![img](https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/grayImg.png) | ![img](https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/hist_gray.png) |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                **Figure 1. Gray-scale image**                |             **Figure 2. Histogram of Figure 1**              |

From the **Figure 2** above, it can be seen that the frequency components are concentrated in the relatively low intensity part. This means that the contrast of the picture is low.

For appropriate object segmentation, high contrast between the background and the object is advantageous, so image preprocessing was performed using histogram equalization.



| ![img](https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/histequalImg.JPG) | ![img](https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/hist_equal.png) |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|     **Figure 3. Image applying histogram equalization**      |             **Figure 4. Histogram of Figure 3**              |

After applying histogram equalization, it can be seen that the contrast between the object and the background is increased. Also, it can be seen that the distribution of the histogram of the image is widened.

However, looking at the upper right part of Figure 3, it is expected that the threshold value will increase because the brightness of the background and the object are similar.



## 2. Filter

Filter is a key tool for image processing. Among them, an order-statistic (non-linear) filter is commonly used to reduce impulse noise.

If you look at **Figure 5**, you can see that salt & pepper (impulse) noise is present on the image. There is a median filter as an effective filter for this. it reduces impulse noise, avoids excessive smoothing, and preserves edges.

Referring to **Figure 6**, it can be seen that the impulse noise is reduced due to the median filter.

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/noiseAnalysis.JPG" width = "480" height = "360"> | <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/medianImg.JPG" width = "480" height = "360"> |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|        **Figure 5. Salt & pepper noise in Figure 3**         |       **Figure 6. Median filtered image in Figure 3**        |



# VI. Threshold

Thresholding methods include global thresholding and local thresholding. There are basic global thresholding and otsu's method for global thresholding. And local thresholding can apply various threshold values for each sub-window.

Considering the situation of the industrial site where the current technology is applied, the position and intensity of the bolts and nuts shown on the image continuously change, so if local thresholding is applied, it is difficult to obtain a generalized result. Therefore, global binary thresholding was applied.

The mathematical basis of global binary thresholding is as follows.

$$
f(x, y) : input\,image\\

g(x, y) : output\,image\\

T : threshold\,value\\\\

g(x, y)=1,\,if\,f(x, y)>T\\

g(x, y)=0,\,if\,f(x, y)\leq T
$$

 In this case, the value T can be selected through a histogram. Referring to **Figure 7**, the threshold value can be selected based on the part with very high intensity (yellow line).

**Figure 8** is an image derived when the threshold value is set to 240. If you look at the top center of the image, noise remains. Noise can be removed by raising the threshold value, but the reason I left it is for connecting all the edges of bolts and nuts that exist on the image. This will be explained in **Contour** below.

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/hist_med_mod.JPG" width = "480" height = "360"> | <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/thresholdImg.JPG" width = "480" height = "360"> |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|             **Figure 7. Histogram of Figure 6**              |                **Figure 8. Threshold image**                 |



# V. Contour

Contour is information about the boundary line of an area having the same color or the same intensity, and it is possible to grasp the outline or appearance of an object.

Contour can be found by various methods for the binarized image. In this lab, I used a method that finds the external line of the contour and returns only the points where the line can be drawn. The hole was also filled using the method of filling the inside of the found external line.

Generally, contour is used after applying morphology, but the reason why contour is used first is because of the square nut attached to the image.

When object segmentation is attempted using morphology in the threshold image above, the edge of the hexagonal nut below attached nuts is thinner than the attached square nut, so segmentation is difficult. To solve this, by making a filled image (**Figure 9**) using a *drawcontour()* function in *opencv*, the thickness was maintained but a filled image was made to make it easier to separate the nuts.

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/preContourImg.JPG" width = "480" height = "360">F |
| :----------------------------------------------------------: |
|                  **Figure 9. Filled image**                  |



# VI. Morphology

In general, small pieces of broken segments can be seen after applying thresholding. For example, there are unwanted objects of similar brightness or image noise. In this case, you can use morphology to remove or connect the pieces.

The morphology algorithm has a wide range of applications such as pruning and filling holes.

* Fundamental morphological processing

  - Dilation : expand the component

  - Erosion : shrink the component

* Combination of dilation and erosion

  - Opening : erosion -> dilation, eliminate thin protrusions
  - Closing : dilation -> erosion, eliminate small holes



**If you look at Figure 9**, there are noise and attached nuts in the image. Erosion was applied twice to separate the nut. After that, the opening was applied 6 times to remove the remaining image noise. As a result, the image of **Figure 10** was obtained.

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/morphImg.JPG" width = "480" height = "360"> |
| :----------------------------------------------------------: |
|           **Figure 10. Image applying morphology**           |



# VII. Result

By creating a contour for the image of **Figure 10**, object segmentation is possible using the width and length information of each contour.

The objects given in this lab are bolts and nuts. Since a bolt has a distinctly longer length than a nut, it can be easily distinguished using length information. Nuts are relatively similar in length, so they can be distinguished using width information. Objects distinguished through length and width information were classified by varying intensities as shown in **Figure 11**.

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/finalImg.JPG" width = "480" height = "360"> |
| :----------------------------------------------------------: |
|              **Figure 11. Object Segmentation**              |

| <img src = "https://github.com/xodnde3123/DLIP_LAB/blob/main/Material/LAB1/printImg.JPG" width = "240" height = "120"> |
| :----------------------------------------------------------: |
|         **Figure 12. Result of object segmentation**         |

It can be confirmed that segmentation was successfully performed through **Figure 11** and **Figure 12**.



# VIII. Flowchart



| <img src = "https://raw.githubusercontent.com/xodnde3123/DLIP_LAB/main/image/flowchart.JPG"> |
| :----------------------------------------------------------: |
|                   **Figure 12. Flowchart**                   |



# XI. Conclusion

In this lab, i can perform object segmentation on gray-scale images. At this time, it is important to select an appropriate filter by analyzing the histogram for a given image. In general image processing order, filtering, thresholding, morphology and contour are applied. However, as in the test image given in this lab, it is possible to process images more conveniently by applying an order suitable for a specific situation, such as nuts attached.



# X. Discussion

1. Convenient debugging using trackbar

   Many filters, thresholding methods, and morphological techniques had to be tried to perform the object segmentation for the given image. In this case, if you use the trackbar, the debugging time can be greatly reduced.

   - Added trackbar to change filter method
   - Efficient application of morphology using key input to the image derived using the trackbar



# Appendix



##  C++ code

```C
/*------------------------------------------------------/
* Class  : DLIP
* Author : Song Taewoong 
* Date   : 2022-03-28
* LAB    : Gray-scale Image Segmentation
------------------------------------------------------*/

#include <iostream>
#include <opencv2/opencv.hpp>

using namespace std;
using namespace cv;

#define NORMAL		(int) 0
#define BLUR		(int) 1
#define GAUSSIAN	(int) 2
#define MEDIAN		(int) 3
#define FILTER_2D	(int) 4
#define LAPLACIAN	(int) 5
#define HIST_EQUAL  (int) 6

#define ESC			(int) 27

// Global variables for Filtering
int key = 0;
int kernel_size_laplacian = 3;
int kernel_size = 3;
int filter_type = MEDIAN;

int scale = 1;
int delta = 0;
int ddepth = CV_16S;

int ddepth_2D = -1;
Point anchor = Point(-1, -1);

int numEr = 2;
int numDi = 0;

int numOp = 6;
int numCl = 0;

// Global variables for Threshold
int threshold_value = 240;
int threshold_type = 0;
int morphology_type = 0;

int const max_value = 255;
int const max_filtType = 5;
int const max_thresType = 6;
int const max_morpType = 4;
int const max_BINARY_value = 255;

// Global variables for Morphology
int element_shape = MORPH_RECT;		// MORPH_RECT, MORPH_ELIPSE, MORPH_CROSS
int n = 3;
Mat element = getStructuringElement(element_shape, Size(n, n));

Mat src, src_gray, src_He, src_laplacian, dst, dst_thres, dst_morph, dst_contour, result;

Mat kernel = Mat::ones(kernel_size, kernel_size, CV_32F) / (float)(kernel_size * kernel_size);

// Trackbar strings
String window_name = "Threshold & Morphology Demo";
String trackbar_filtType = "Filter Type: \n 0: Normal \n 1: Blur \n 2: Gaussian \n 3: Median \n 4: Filter_2D \n 5: Laplacian \n 6: Median2 \n 7: Med Gau";
String trackbar_type = "Thresh Type: \n 0: Binary \n 1: Binary Inverted \n 2: Truncate \n 3: To Zero \n 4: To Zero Invertd \n 5: Otsu \n 6: Adaptive";
String trackbar_value = "Thresh Value";
String trackbar_morph = "Morph Type 0: None \n 1: erode \n 2: dilate \n 3: close \n 4: open";

// Function headers ==========================================
void GlobalVariables(void);

void ImagePreprocessing(void);
void InitTrackbar(void);

void Filter_Demo(int, void*);
void Threshold_Demo(int, void*);
void Morphology_Demo(int, void*);
Mat  contour_Demo(const Mat& _src);
void plotHist(Mat src, string plotname, int width, int height);

int  KeyCommand(void);
void ImageProcessing(void);
void ShowImage(const String& title, Mat& img);
void PrintResult(void);
//============================================================


int main()
{
	// Create a window to display the results
	namedWindow(window_name, WINDOW_NORMAL);


	// Load an image
	src = imread("Lab_GrayScale_TestImage.jpg", IMREAD_COLOR);

	ImagePreprocessing();

	InitTrackbar();

	// Wait until user finishes program
	while (true) {
		key = waitKey(100);
		if (!KeyCommand()) break;
		
		ImageProcessing();
		
		ShowImage("Contour", result);
		PrintResult();
	}
}


void ImagePreprocessing(void) {
	// Convert the image to Gray
	cvtColor(src, src_gray, CV_BGR2GRAY);

	// Apply Histogram Equalization
	equalizeHist(src_gray, src_He);
}

void InitTrackbar(void) {
	// Create trackbar to choose type of threshold
	createTrackbar(trackbar_filtType, window_name, &filter_type, max_filtType, Filter_Demo);
	createTrackbar(trackbar_type, window_name, &threshold_type, max_thresType, Threshold_Demo);
	createTrackbar(trackbar_value, window_name, &threshold_value, max_value, Threshold_Demo);
	createTrackbar(trackbar_morph, window_name, &morphology_type, max_morpType, Morphology_Demo);

	// Call the function to initialize
	Filter_Demo(0, 0);
	Threshold_Demo(0, 0);
	Morphology_Demo(0, 0);
}

void Filter_Demo(int, void*)  // default form of callback function for trackbar
{
	/*
	 * 0: NORMAL		
	 * 1: BLUR			
	 * 2: GAUSSIAN		
	 * 3: MEDIAN		
	 * 4: FILTER_2D	
	 * 5: LAPLACIAN	
	*/

	switch (filter_type) {
	case 0: src_He.copyTo(dst);															break;
	case 1: blur(src_He, dst, cv::Size(kernel_size, kernel_size), cv::Point(-1, -1));	break;
	case 2: GaussianBlur(src_He, dst, Size(kernel_size, kernel_size), 0, 0);			break;
	case 3: medianBlur(src_He, dst, kernel_size);										break;
	case 4: filter2D(src_He, dst, ddepth_2D, kernel, anchor);							break;
	case 5: Laplacian(src_He, dst, ddepth, kernel_size_laplacian, scale, delta, cv::BORDER_DEFAULT);
		src_He.convertTo(src_He, CV_16S);
		src_laplacian = src_He - dst;
		src_laplacian.convertTo(src_laplacian, CV_8U);
		src_laplacian.copyTo(dst);
		break;
	}

	imshow(window_name, dst);
}

void Threshold_Demo(int, void*)	// default form of callback function for trackbar
{
	/*
	* 0: Binary
	* 1: Threshold Truncated
	* 2: Threshold to Zero
	* 3: Threshold to Zero Inverted
	* 4 : Otsu
	* 5 : Adaptive
	*/

	switch (threshold_type) {
	case 0: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 0); break;
	case 1: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 1); break;
	case 2: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 2); break;
	case 3: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 3); break;
	case 4: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 4); break;
	case 5: threshold(dst, dst_thres, threshold_value, max_BINARY_value, 8); break;
	case 6: adaptiveThreshold(dst, dst_thres, 255, ADAPTIVE_THRESH_MEAN_C, THRESH_BINARY, 13, 10); break;
	}
	imshow(window_name, dst_thres);
}

void Morphology_Demo(int, void*)  // default form of callback function for trackbar
{
	/*
	* 0: None
	* 1: Erode
	* 2: Dilate
	* 3: Close
	* 4: Open
	*/
	switch (morphology_type) {
	case 0: dst_thres.copyTo(dst_morph);	break;
	case 1: erode(dst_thres, dst_morph, element); break;
	case 2: dilate(dst_thres, dst_morph, element); break;
	case 3: morphologyEx(dst_thres, dst_morph, CV_MOP_OPEN, element); break;
	case 4: morphologyEx(dst_thres, dst_morph, CV_MOP_CLOSE, element); break;
	}
	imshow(window_name, dst_morph);
	contour_Demo(dst_morph);
}


Mat contour_Demo(const Mat& _src) {
	// Example code
	// dst: binary image

	int volM5 = 0;
	int volM6 = 0;
	int nutRecM5 = 0;
	int nutHexM5 = 0;
	int nutHexM6 = 0;

	vector<vector<Point>> contours;

	const Scalar black(0, 0, 0);
	const Scalar white(255, 255, 255);

	/// Find contours, 최외각 정보(type : CV_RETR_EXTERNAL)로 contour를 만듦
	findContours(_src, contours, CV_RETR_EXTERNAL, CV_CHAIN_APPROX_SIMPLE, Point(0, 0));

	/// Draw all contours excluding holes
	Mat drawing(_src.size(), CV_8UC1, Scalar(255));

	drawContours(drawing, contours, -1, Scalar(0), CV_FILLED);

	//namedWindow("contour", 0);
	//imshow("contour", drawing);

	// contour 면적, 경계길이를 활용하여 object segmentation
	if (contours.size() <= 21) {
		cout << " Number of coins are =" << contours.size() << endl;
		for (int i = 0; i < contours.size(); i++)
		{
			if (arcLength(contours[i], true) > 540) {
				drawContours(drawing, contours, i, Scalar(0), CV_FILLED);
				volM6++;
			}
			else if (arcLength(contours[i], true) > 400) {
				drawContours(drawing, contours, i, Scalar(75), CV_FILLED);
				volM5++;
			}
			else if (arcLength(contours[i], true) > 255) {
				drawContours(drawing, contours, i, Scalar(120), CV_FILLED);
				nutHexM6++;
			}
			else if (contourArea(contours[i]) > 3050) {
				drawContours(drawing, contours, i, Scalar(180), CV_FILLED);
				nutRecM5++;
			}
			else {
				drawContours(drawing, contours, i, Scalar(220), CV_FILLED);
				nutHexM5++;
			}
		}

		printf("=========== Object Segmentation ==========\n");
		printf("M5 Volt		= %d\n", volM5);
		printf("M6 Volt		= %d\n", volM6);
		printf("M5 Hex Nut	= %d\n", nutHexM5);
		printf("M6 Hex Nut	= %d\n", nutHexM6);
		printf("M5 Rect Nut	= %d\n", nutRecM5);
		printf("==========================================\n\n");
	}



	return drawing;
}


void plotHist(Mat src, string plotname, int width, int height) {

	/// Compute the histograms 
	Mat hist;
	/// Establish the number of bins (for uchar Mat type)
	int histSize = 256;
	/// Set the ranges (for uchar Mat type)
	float range[] = { 0, 256 };

	const float* histRange = { range };
	calcHist(&src, 1, 0, Mat(), hist, 1, &histSize, &histRange);


	double min_val, max_val;
	cv::minMaxLoc(hist, &min_val, &max_val);
	Mat hist_normed = hist * height / max_val;
	float bin_w = (float)width / histSize;		

	Mat histImage(height, width, CV_8UC1, Scalar(0));  
	for (int i = 0; i < histSize - 1; i++) {			
		line(histImage,									
			Point((int)(bin_w * i), height - cvRound(hist_normed.at<float>(i, 0))),				
			Point((int)(bin_w * (i + 1)), height - cvRound(hist_normed.at<float>(i + 1, 0))),   
			Scalar(255), 2, 8, 0);																
	}

	imshow(plotname, histImage);
}

int KeyCommand(void) {
	int result = 1;
	if (key == ESC) // wait for 'ESC' press for 30ms. If 'ESC' is pressed, break loop
	{
		cout << "ESC key is pressed by user\n";
		return 0;
	}

	else if (key == 'a' || key == 'A') numEr++;
	else if (key == 'z' || key == 'Z') numEr--;
	else if (key == 'q' || key == 'Q') numEr = 0;

	else if (key == 's' || key == 'S') numDi++;
	else if (key == 'x' || key == 'X') numDi--;
	else if (key == 'w' || key == 'W') numDi = 0;

	else if (key == 'd' || key == 'D') numOp++;
	else if (key == 'c' || key == 'C') numOp--;
	else if (key == 'e' || key == 'E') numOp = 0;

	else if (key == 'f' || key == 'F') numCl++;
	else if (key == 'v' || key == 'V') numCl--;
	else if (key == 'r' || key == 'R') numCl = 0;

	return 1;
}

void ImageProcessing(void) {
	dst_contour = contour_Demo(dst_thres);
	dst_contour = 255 - dst_contour;

	erode(dst_contour, dst_morph, element, Point(-1, -1), numEr);
	dilate(dst_morph, dst_morph, element, Point(-1, -1), numDi);

	morphologyEx(dst_morph, dst_morph, CV_MOP_OPEN, element, Point(-1, -1), numOp);
	morphologyEx(dst_morph, dst_morph, CV_MOP_CLOSE, element, Point(-1, -1), numCl);

	result = contour_Demo(dst_morph);
}

void ShowImage(const String& title, Mat& img) {
	namedWindow(title, WINDOW_NORMAL);
	imshow(title, img);
}

void PrintResult(void) {
	printf("================ Morphology ==============\n");
	printf("Erosion	 = %d\n", numEr);
	printf("Dilation = %d\n", numDi);
	printf("Opening  = %d\n", numOp);
	printf("Closing  = %d\n", numCl);
	printf("==========================================\n\n");
}
```

