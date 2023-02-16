# Stroma Vision Challenge ! olan yere link koy
Strom Vision technical interview. In this challenge we are asked to identify the nuts and bolts in the video and find the total number of detected objects.

![](https://github.com/Hasan-atc/Stroma/blob/main/detect.gif)

# COCO to YOLO 🔷
🔻 To convert the given dataset to Yolo format, [see the link](https://github.com/pylabel-project/samples/blob/main/coco2yolov5.ipynb).

# YoloV5 Train :large_blue_diamond:
:small_red_triangle_down: First, let's install the Yolov5 github repo on our device and install requirements.
> Clone repo and install requirements.txt in a [Python>=3.7.0](https://www.python.org/) environment, including [PyTorch>=1.7](https://pytorch.org/get-started/locally/).
````
git clone https://github.com/ultralytics/yolov5  # clone
cd yolov5/
pip install -r requirements.txt  # install
````
> Check out the [YoloV5](https://github.com/ultralytics/yolov5) repo for necessary information and version controls.

🔻 In this project, [1.13.1 PyTorch](https://pytorch.org/get-started/locally/) version and [11.6 Cuda](https://developer.nvidia.com/cuda-11-6-0-download-archive) version were used.
````
nvidia-smi # cuda version control

import torch
torch.__version__ # torch version control
````
![Screenshot from 2023-02-16 11-45-09](https://user-images.githubusercontent.com/74008306/219313889-6cf31c44-eb21-4977-816b-c156bed16d38.png)
![Screenshot from 2023-02-16 11-47-44](https://user-images.githubusercontent.com/74008306/219314342-a2a45de0-f143-49ad-8150-9f47bd1c049a.png)

:small_red_triangle_down: Starting the training according to the data set we have.
> The path, object classes and ids of our own dataset are specified in the [custom_data.yaml](https://github.com/Hasan-atc/Stroma_Vision_challenge/blob/main/yolov5/custom_data.yaml) file


````
cd yolov5/
python3 train.py --weights path/to/weights/file --cfg path/to/custom_data.yaml --epoch 500 --batch-size 32 --patience 0
````
> See for more detailed uses of the [arguments](https://github.com/ultralytics/yolov5/blob/master/train.py).

# Object Detection and Counting 🔷
🔻 Object detection with the weight file created after the training
````
cd yolov5/
python3 detect.py --weights path/to/train/weight --source file/path/to/detect/object --conf-thresh 0.6
````
> For more information, please check the [YoloV5 repo](https://github.com/ultralytics/yolov5)

🔻 Counting Detected Objects
> This system works embedded in the object detection algorithm of the YoloV5 model ✔️

The working logic of this algorithm is as follows:

- The variable s, which provides information about each frame in the detection system, also contains information about the objects detected in the frame. S variable :

  ![Screenshot from 2023-02-16 12-29-15](https://user-images.githubusercontent.com/74008306/219324654-00e86bbd-5edf-429e-8a55-93296b71d1a8.png)
  
- If any of the bolt and nut values or both at the same time are found in the variable S, the number of objects in the frame is added to the lists specific to the variables. If no objects are detected, 0 is added to the lists. Thus, empty frames are also examined.

  ````
  global sayac, toplam

            sayac = sayac + 1  # The counter is incremented by 1 for each frame in order to scroll through the list by the frame length of the video.

            bolt = 'bolt'
            nut = 'nut'

            if bolt in s and nut in s:
                c_bolt = s[1:s.find(bolt)].split()[-1]  # The amount of the detected object is found
                c_bolt = int(c_bolt)  # This amount is converted to an int value for calculation
                c_nut = s[1:s.find(nut)].split()[-1]
                c_nut = int(c_nut)
                bolt_liste.append(c_bolt) # This amount is transferred to the object specific list
                nut_liste.append(c_nut)

            elif bolt in s:
                c_bolt = s[1:s.find(bolt)].split()[-1]
                c_bolt = int(c_bolt)
                bolt_liste.append(c_bolt)
                nut_liste.append(0)

            elif nut in s:
                c_nut = s[1:s.find(nut)].split()[-1]
                c_nut = int(c_nut)
                nut_liste.append(c_nut)
                bolt_liste.append(0)

            else:
                bolt_liste.append(0)  # 0 is added if no object is detected on the frame
                nut_liste.append(0)
    ````

- The values assigned to these lists are then examined. At the end of this review, the list n. its value is n+1. If it is less than the value, the number of found objects is increased by 1. Thus, the objects leaving the frame and the new objects entering the frame are also examined.

  ````
    top_bolt = 0
    top_nut = 0

    for count in range(0, sayac):  
        if bolt_liste[count + 1] != bolt_liste[count]:  # If a new object is entered in the frame, the total number of objects is increased by 1
            if bolt_liste[count + 1] > bolt_liste[count]:
                top_bolt = top_bolt + 1

    for count in range(0, sayac):
        if nut_liste[count + 1] != nut_liste[count]:
            if nut_liste[count + 1] > nut_liste[count]:
                top_nut = top_nut + 1

    print("Bulunan Toplam Nesne : {}\n".format(top_bolt + top_nut))
    print("Bulunan Toplam Bolt : {}\n".format(top_bolt))
    print("Bulunan Toplam Nut : {}\n".format(top_nut))
  ````
  ![Screenshot from 2023-02-16 12-47-16](https://user-images.githubusercontent.com/74008306/219329564-352adc1a-e387-4ea9-b8bb-7fa457e74e0c.png)
  
  > See the detect.py file for detailed analysis!
