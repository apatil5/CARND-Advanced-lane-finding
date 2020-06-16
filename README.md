# CARND-Advanced-lane-finding

1) Camera calibration
Firstly Camera needs to be calibrated for the distortions present in the images. Typical steps to compute the distortion matrix and coefficients for a single image are as below: 
1. Manually count the number of visible corners in horizontal and vertical direction in the distorted images of the chess board 
2. Convert the distorted color image to gray scale.
3. The gray scale image is then used to find corners using command cv2.findChessboardCorners()
4. The command gives an array of corner indices as one of the output.
5. A mesh grid of dimension (nx , ny) is formed to represent real world chess board points on a plane. The mesh grid points are then reshaped into 3 dimensional array.
6. Calibration matrix and distortion matrix is then obtained using command cv2.calibrateCamera and Indices array and objective array as input. The code presented below is taken from Cell : 1 and line no. = 9-25

Example Image

However matrix and coefficients obtained by the calibration on a single image is not enough and it does not work. 

Therefore matrix and coefficients must be computed on the augmented arrays consisting of corner and objective points from all calibration images . For every calibration image provided in workspace, above steps are performed to find matrices. All the corner matrices are first joined together to form an augmented array and similarly an augmented Objective array is formed.

Binary images and combined binary images
I used binary images obtained from sobel operator in X direction along with S Channel and V channel. The thresholds were tuned to obtain maximum pixel points of curved striped lanes while discarding undesired pixels in the respective binary images. All three binary images where combined to obtained final binary 2D image to find lane. pixels. 
The code presented below is taken from Cell : 1 and line no. = 33-54
Sobel along X along X
S Channel of HLS Channel of HLS
V channel of HSV
V channel of HSV
Combined binar
Combined binary imagey image

Note : Combined binary images of the test images are stored in the folder output_images with name : cb_test*.jpg
Firstly, The combined binary image is masked to remove undesired pixels from the image. This is one of the most crucial step where pixel points separated between left and right lanes. Using cv2.HoughLinesP() command, lines are obtained in the masked image and then pixels corresponding to left and right lanes are separated based on the slope of the respective line. A gradient range (0.4 to 1.7 or -1.7 to -0.4) is finalized to find pixel of respective lanes. Further threshold and other parameters are tuned to meet the project requirement. Lastly, four appropriate corner points are obtained by applying polyfit function on right lane and left lane points respectively. The code presented below is taken from Cell : 1 and line no. = 56 - 87
Lastly four appropriate destination points are chosen and cv2.warpPerspective() function is used to get warped image
After perspective transformation step, lane pixels are obtained by implementing sliding window method. In sliding window method, lane pixels are searched inside a smaller window of dimensions 80 x 100. The search begins from the bottom of the image and initially the x coordinate of the center of first image is chosen to be the value obtained from histogram. Further the next window is slides horizontally based on the location of the center of the pixels inside the search window. This goes on in a loop until all the rows of the image is searched and desired pixels of both left and right lanes are obtained . The location of code for this part is Cell : 1 line no. 164 -222

Since the lane curvatures do not change much in subsequent frames, Localized search method is implemented by searching forthelanepixelsin thevicinityoflanecurveobtainedin previousframe.To accomplishthistask,global variablesareusedandlanecurvaturecoefficientsarestored.ThelocationofcodeforthispartisCell:1line no.224-264Inadditiontothatlanepixelsfromlastframesareadded/combinedwithlanepixelsofcurrentframetoobtainedrobustlanecurvature.Thisensuresanefficientpixel searchandlanecurvaturecomputation.Intheprojectvideotest,thelanepixelsaredetectedbylocalizedsearchmethodin almostalloftheframesexceptin veryfewdetectable frames.ThelocationofcodeforthispartisCell:1line no.160-161
Lane-line pixels and fit their positions with a polynomial
Perspective transform
Note : Warped binary
images of the test images
are stored in the folder
output_images with name :
warped_test*.jpg

Improvement Area:
The pipeline fails to find lane pixels for when the vehicle encounters bump or a jerk. Also if pipeline is vulnerable to changes in lane surfaces, shadows, tire marks, black patches,􀀃􀁒􀁕􀀃􀁙􀁈􀁋􀁌􀁆􀁏􀁈􀁖􀀃􀁌􀁑􀀃􀁉􀁕􀁒􀁑􀁗􀀃􀁒􀁕􀀃􀁄􀁇􀁍􀁄􀁆􀁈􀁑􀁗􀀃􀁏􀁄􀁑􀁈􀀃. It required lots of tuning of Hough lines parameters to get suitable pixels. Some times pixels are not easily obtained and pipeline fails to find the lanes. As solution to these problems, I have to abstract thoughts. First, a filter can be designed which can identify anomalous and undesired pixel by comparing the last frame. Second, a dynamic mask can be designed which can change its shape according to the information received on lanes from last frame.Examples of pipe line failure:

Example of final images from the out put video
the lane curvatures do not change much in subsequent frames, Localized search method is implemented by searching for the lane pixels in the vicinity of lane curve obtained in previous frame. To accomplish this task, global variables are used and lane curvature coefficients are stored. The location of code for this part is Cell : 1 line no. 224 -264In addition to that lane pixels from last frames are added/combined with lane pixels of current frame to obtained robust lane curvature. This ensures an efficient pixel search and lane curvature computation. In the project video test, the lane pixels are detected by localized search method in almost all of the frames except in very few detectable frames. The location of code for this part is Cell : 1 line no. 160-161
Once the lane curvature coefficients are obtained using polyfit function, Lane radius and position of the vehicle in real world is obtained by axis transformation. The transformation coefficients are referred from the lessons. The radius of curvature of each lane is computed for the bottom pixels of the image representing the position of car. The transformation steps are shown in code below :
The radius of curvature of the lane and the position of the vehicle with respect to center.
