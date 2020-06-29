---
title: "Quadrilateral detection from polygon or segmentation masks"
date: 2017-11-13
categories: ["opencv", "image processing"]
collection: blog
---

In some applications we need to detect the quadrilateral objects in the image, for example detecting a document, an ID card, or anything whose the shape is quadrilateral. By using some techniques such as contour detection, edge detection, connected components etc. we can identifiy the object but it is not always a 4-edges polygon.

The popular object detection models aim at detecting the bounding box of the object. Some applications require detecting the objects with quadrilateral shape. In this case, we can use some customized object detection, instead of detecting the center and the width, height of the object, we can do the corner regression (I will cover this approach in another post). We can also use segmentation model to detect the  quadrilateral, however, the result is not always a 4-edges polygon. 

To overcome this problem, we can use some image processing techniques in post-processing step to get the final quadrilateral. In this post I present a method to detect quadrilateral from an binary object mask with opencv 4.

### Find the object contours
Firsly, we need to find the object contour, then reduce the number of contour vertices by an approximate method (the function approxPolyDP).

```python
    path = "path_to_object_mask"
    mask = cv2.imread(path, cv2.IMREAD_UNCHANGED)
    # Find object contours
    cnts, hiers = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)[-2:]
    area_max = 0
    #Find biggest contour
    biggest_cnt = []
    for cnt in cnts:
        area = cv2.contourArea(cnt)
        if (area_max < area):
            area_max = area
            biggest_cnt = cnt 
    
    #approximate the contour to a polygon with few vertices                        
    peri = cv2.arcLength(biggest_cnt, True)
    approx = cv2.approxPolyDP(biggest_cnt, 0.01 * peri, True)
    mask_approx = np.zeros((mask.shape[0],mask.shape[1]), dtype = np.uint8)
    cv2.fillPoly(mask_approx, pts =[approx], color=(255))
    plt.imshow(mask_approx)
```

Instead of using $$0.01 * peri$$ we can recursively use the function approxPolyDP to find correct value to approximate the contour to a 4-edges polygon. However, the final quadrilateral is not very correct because of the bad results from segmentation models on the object boundary.

### Using Hough Transform to find lines

Using Hough Transform to find 4 sides of the objects is a better solution because it can tolerante (ignore) some small errors of the segmentation model on the object boundary. OpenCV has 2 function : HoughLinesP and HoughLines where HoughLinesP is faster but HoughLines is better. 

```python
minLineLength = min (mask.shape[0],mask.shape[1])/3
lines = cv2.HoughLinesP(convexHull_mask, rho = 1,theta = 1*np.pi/180,threshold = 50,
                        minLineLength = minLineLength,maxLineGap = 50)
tmp_img = np.zeros((mask.shape[0],mask.shape[1]), dtype = np.uint8)
for i in range(lines.shape[0]):
    x1 = lines[i][0][0]
    y1 = lines[i][0][1]    
    x2 = lines[i][0][2]
    y2 = lines[i][0][3]    
    cv2.line(tmp_img,(x1,y1),(x2,y2),(255,0,0),2)    
plt.imshow(tmp_img)                            
```

### Find 4 corners from 4 detected lines.

The lines detected by Hough Transform are often not complete (because the imperfection of the segmentation models or the contour detection algorithm),  we need to figure out how to find the intersections (4 corners) of these lines..


```python
def findIntersection (params1, params2):
    x = -1
    y = -1
    det = params1[0] * params2[1] - params2[0] * params1[1]
    if (det < 0.5 and det > -0.5): #lines are approximately parallel
        return (-1, -1)
    else:
        x = (params2[1] * -params1[2] - params1[1] * -params2[2]) / det;
        y = (params1[0] * -params2[2] - params2[0] * -params1[2]) / det;
    return (int(x), int(y))

#line's equation computation
def calcParams(p1,p2):
    if p2[1] - p1[1] ==0:
        a = 0.0
        b = -1.0
    elif p2[0] - p1[0] ==0:
        a = -1.0
        b =0.0
    else:
        a = (p2[1] - p1[1]) / (p2[0] - p1[0])
        b = -1.0
    c = (-a * p1[0]) - b * p1[1]
    return a,b,c
        
corners = []
lines = np.squeeze(lines)    
tmp_img = mask.copy()
if len(lines) == 4:
    params = []
    for i in  range(4):
        params.append(calcParams([lines[i][0], lines[i][1]], [lines[i][2], lines[i][3]]))
    print (params)
    for i in range(len(params)):
        for j in range(i, len(params)):
            intersec = findIntersection(params[i], params[j])
            if intersec[1]>0 and intersec[0]>0 and intersec[1]< mask.shape[0] and intersec[0]< mask.shape[1] :
                print ("Corner: ", intersec)
                corners.append(intersec)
    for i in range(4):
        cv2.circle(tmp_img, corners[i], 5, (255), 5)                  
plt.axis('off')
plt.imshow(tmp_img)  
```


### Find 4 corners from many detected lines.

Maybe the lines detected by Hough Transform is bigger than 4 (this happens very often), we need to figure out how to filter these imperfect lines to get the final 4 lines, pls note that HoughLines returns the lines in the descending order of confidence scores). From this part I worked on C++ (with C++ version of previous codes), but it should be very easy to convert to Python

```c++
// We need to filter lines because  Hough Transform detect many duplicate lines which are just slightly different from each other.
void filterLines(const std::vector<cv::Vec2f> &lines, std::vector<cv::Vec2f> &outputLines) 
{			
    for (cv::Vec2f line : lines){
        float r1 = line[0];
        float t1 = line[1];
        bool existed = false;
        for (cv::Vec2f lineF : outputLines){
            float r2 = lineF[0];
            float t2 = line[1];
            if (abs(r1-r2) < 10 && abs(t1-t2) < CV_PI/36){
                existed = true;
                break;
            }
        }
        if (!existed){
            outputLines.push_back(line);
            // //Because the lines is in order of confidence, we take only first 4 lines
            // if (outputLines.size()==4)
            // 	break;
        }
    }
}		
```
