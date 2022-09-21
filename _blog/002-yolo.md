---
title: "Realtime object detection with YOLO part2: training [in Vietnamese]"
excerpt: "<a href='/blog/001-yolo/'><img width='49' height='26' src='https://pjreddie.com/media/image/yologo_2.png'></a> Train YOLO model to identify  poker cards."
date: 2017-03-15
categories: ["object detection", "deep learning"]
collection: blog
---

Nên đọc bài lý thuyết trước [here](https://nhuvan.github.io/blog/001-yolo/)  

Bài viết này hướng dẫn huấn luyện mô hình YOLO trên cở sở dư liệu của bạn. Bạn có thể huấn luyện 1 mô hình nhận dạng các quân bài trong 1 bức ảnh nếu bạn có một tập ảnh rất nhiều lá bài đã được đánh dấu vị trí các số (A, 2, 3 ... 10, J, Q, K) cùng với loại (cơ rô bích tép). Bạn có thể sủ dụng mô hình này để nhận dạng các quân bài poker và sử dụng các thuật toán để xác định action cần thiết để tăng khả năng chiến thắng.

Bài báo gốc về YOLO có thể  tìm ở đây [version 1](https://arxiv.org/pdf/1506.02640.pdf), [version 2](https://arxiv.org/pdf/1612.08242.pdf).

Code source, hướng dẫn chạy test và huấn luyện với dữ liệu ảnh Pascal Voc ở đây [here](http://pjreddie.com/darknet/yolo/).


## 1. Dependencies

Để huấn luyện YOLO, cần kiến thức cơ bản về  neural network, 1 ít kinh nghiệm lập trình C. 

## 2. Các bước chính

### 2.1 Cài đặt darknet
+ Làm theo hướng dẫn ở đây (dễ) [here](https://pjreddie.com/darknet/install/)
+ Hoặc :
```
git clone https://github.com/pjreddie/darknet
cd darknet
make
```

### 2.2 Test mô hình YOLO đă huấn luyện với Pascal images.
+ Download [weights file](https://pjreddie.com/media/files/yolo.weights)
+ run command ./darknet detect cfg/yolo.cfg yolo.weights data/dog.jpg

### 2.3 Chuẩn bị dữ liệu
+ File text poker.names chứa danh sách các classes (52 classes: A_cơ, 2_cơ, ... K_tép). Không cần resize các images.
+ Images các quân bài đã được đánh dấu, trong 1 folder
+ Một txt file (label file) cho mỗi image, chứa thông tinh quân bài đánh dấu. Phải cùng tên, chỉ khác .jpg -> .txt
+ Mỗi dòng trong file txt miêu tả 1 boundingbox trong image (hình chữ nhật bao quanh object = x_center, y_center, width, height) và class tương ứng. Tuy nhiên cần phải chia cho image_width và image_height để có dạng "class_id x_center/image_width y_center/image_height width/image_width height/image_height"
+ Một txt file chứa path của tất cả các images (train.txt)
+ Không bắt buộc: Có thể chia train.txt thành 2 file, train.txt chứa 8 phần các images và valid.txt chứa các images còn lại.
+ poker.data chứa các thông tin trên, ví dụ:
	```
	classes=52
	train  = /workspace/darknet/Poker_DevKit/train.txt
	valid  = /workspace/darknet/Poker_DevKit/valid.txt
	backup = backup #Nới lưu mô hình được huấn luyện
	names = data/Poker.names
	```
+ File .cfg của [YOLOv2](https://github.com/pjreddie/darknet/blob/master/cfg/yolo.cfg). Nhớ update số classes và filters của Convolutional Layer cuối. filters= (#classes + #coords + 1)*#num

- Một file mô hình (.weights) để finetune hoắc huấn luyện từ đầu. Có thể dùng file weights ở [đây](https://pjreddie.com/media/files/darknet19_448.conv.23)

## 3. Huấn luyện

+ ./darknet detector train cfg/voc.data cfg/yolo-voc.cfg darknet19_448.conv.23

## 4. Các tools hữu ích

Data và các tools dưới đây sẽ được update sau  

+ Annotation Tool : dùng để tạo ra các label file sủ dụng trong darknet (yolo)
+ Tool để xác định các anchor boxes cho tập ảnh mới (xem bài lý thuyết YOLO)
```
	[region]
	anchors = 1.08,1.19,  3.42,4.41,  6.63,11.38,  9.42,5.11,  16.62,10.52
```
+ Tool để theo dõi quá trình huấn luyện
+ Tool để đánh gía mô hình huấn luyện
