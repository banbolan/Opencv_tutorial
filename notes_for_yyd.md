Opencv
#### 1.读取图像
```
image = cv2.imread("XXX.jpg")
```
以下的image均是上述的对image的实例所使用的方法
#### 2.提取图像的形状
```
(h,w,d) = image.shape
```
高、宽、深（颜色）
深度有三个通道：蓝、绿、红
#### 3.展示图像
```
cv2.imshow("Image",image)
cv2.waitKey(0) #等按键否则图像很快关闭。
```
#### 4.像素
(B、G、R)
[0,255]255->白，0->黑
```
(B,G,R)=image[100,50]
print("R={},G={},B={}",format(R,G,B))
```
#### 5.划分出感兴趣的区域（ROIS:Regions of interest）
```
roi = image[60:160.320:420]
cv2.imshow("ROI",roi)
cv2.waitKey(0)
```
` image[startY:endY.startX:endX]`
#### 6.重构图像大小
重构至指定大小
```
resized = cv2.resize(image,(200,200))
cv2.imshow("Fixed Resizing",resized)
cv2.waitKey(0)

```
保持长宽比重构：
```
r = 300.0/w #计算伸缩比例
dim = (300,int(h*r))
resized = cv2.resize(image,dim)
cv2.imshow("Aspect Ratio Resize",resized)
cv2.waitKey(0)
```
使用`imtils.resize`保持长宽比:
```
resized = imutils.resize(image,width=300)
cv2.show("Imutils Resize",resized)
cv2.waitKey(0)
```
#### 7.旋转图像
**使用Opencv旋转图像**
```
center = (w//2,h//2)
M = cv2.getRotationMatrix2D(center,-45,1.0)
rotated = cv2.warpAffine(image,M,(w,h))
cv2.imshow("Rotation Image",rotated)
cv2.waitKey(0)
```
首先计算旋转中心：使用`//`以获得整数
M是由Opencv计算所得的旋转矩阵，`-45`是指顺时针方向旋转45度，角度为正时逆时针旋转，角度为负时顺时针旋转
**使用imutils旋转图像**
```
rotated = imutils.rotate(image,-45)
cv2.imshow("Imutils Rotations",rotated)
cv2.waitKey(0)
```
在保证旋转后的图像仍能够完全显示，而不被切割，使用如下代码：
```
rotated = imutils.rotate_bound(image,45)
cv2.imshow("Imutils Bound Rotation",rotated)
cv2.waitKey(0)
```

#### 8.使图像更平滑(模糊)
`GaussianBlur`高斯模糊：
```
blurred = cv2.GaussianBlur(image,(11,11),0)
cv2.imshow("Blurred",blurred)
cv2.waitKey(0)
```
高斯核越大，图像越模糊；高斯核越小，图像越不清晰
#### 9.在图片上画画
绘画前，常常先创建一个原图像的副本，然后在副本上绘画
```
output = image.copy()
cv2.rectangle(output,(320,60),(420,160),(0,0,255),2)
cv2.imshow("Rectangle",output)
cv2.waitKey(0)
```
在使用`cv2.rectangle`时所用到的参数：
* `img`:绘画所用到的图形
* `pt1`:左上方的开始绘制的第一个像素点
* `pt2`:右下方结束绘制的像素殿
* `color`:BGR元组
* `thickness`:线宽，负值时将得到一个实心的矩形

下述代码会获得一个实心蓝圆：
```
output = image.copy()
cv2.circle(output,(300,150),20,(255,0,0)-1)
cv2.waitKey(0)
```
所用到的参数依次是：
* `img`:图像
* `center`:圆圈中心
* `radius`:圆圈半径
* `color`:圆圈颜色
* `thickness`:线宽

下面画一条红线：
```
output = image.copy()
cv2.line(output,(60,20),(400,200),(0,0,255),5)
cv2.imshow("Line",output)
cv2.waitKey(0)
```
插入文本：
```
output = image.copy()
cv2.putText(output,"xxxxxxxxxxxxx",(10,25),
	cv2.FONT_HERSHET_SIMPLEX,0.7,(0,255,0),2)
cv2.imshow("Text,output")
cv2.waitKey(0)
```
参数分析如下：
* `img`:所用到的图像
* `text`:需要插入的文本
* `pt`:文本开始的像素殿
* `font`:字体
* `scale`:字体放大器
* `color`:文本颜色
* `thickness`:厚度

#### 10.转换为灰度图
```
image = cv2.imread(args["image"])
cv2.imshow("Image",image)
cv2.waitKey(0)

gray = cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)
cv2.imshow("Gray",gray)
cv2.waitKey(0)
```
#### 11.边角检测
```
edged = cv2.Canny(gray,30,150)
cv2.imshow("Edged",edged)
cv2.waitKey(0)
```
使用Canny算法以发现图片中的角点`cv2.Canny`:
* `img`:灰度图
* `minVal`:最小阈值
* `maxVal`:最大阈值
* `aperture_size`:Sobel核的大小，默认值是3
#### 12.阈值
阈值化可以去除图像中过亮或者过暗的区域，也可以去除图像的轮廓。

```
thresh = cv2.threshold(gray,225,255,cv2.THRESH_BINARY_INV)[1]
cv2.imshow("Thresh",thresh)
cv2.waitKey(0)
```

* 将`gray` 图片中比225大的像素设置为0，这一部分是图片的背景
* 将像素值小于225设置为255，获得图像的前景

上述代码将图像二值化，以下一步获得图像的边缘

#### 13.检测并绘出图像的边缘
```
cnts = cv2.findContours(thresh.copy(),cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_SIMPLE)
cnts = imutils.grab_contours(cnts)
output = image.copy()

for c in cnts:
	cv2.drawContours(output,[c],-1,(240,0,159),3)
	cv2.imshow("Contours",output)
	cv2.waitKey(0)
```
首先使用`cv2.findContours`侦测图像的边缘，使用`thresh.copy()`图像
然后对cnts中的每一个c绘画边缘
同时可以加入一些文本：
```
text = "I found {} objects!".format(len(cnts))
cv2.putText(output,text,(10,25),cv2.FONT_HERSHEY_SIMPLEX,0.7,(240,0,159),2)
cv2.imshow("Contours",output)
cv2.waitKey(0)
```
#### 14.腐蚀与膨胀（Erosions and Dilations）
常常被用来减少二值化的图像的噪声
下述代码减小了白色区域
```
mask = thresh.copy()
mask = cv2.erode(mask,None,iterations = 5)
cv2.imshow("Eroded",mask)
cv2.waitKey(0)
```
同样地，也可以扩大区域
```
mask = thresh.copy()
mask = cv2.dilate(mask, None, iterations=5)
cv2.imshow("Dilated", mask)
cv2.waitKey(0)
```
#### 15.遮蔽、位操作（masking and bitwise operations）
```
mask = thresh.copy()
output = cv2.bitwise_and(image,image,mask=mask)
cv2.imshow("Output",output)
cv2.waitKey(0)
```
使用`thresh`来作mask、使用逻辑位操作来获得最终的图像


