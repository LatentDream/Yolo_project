### **Credits:**

**Joseph Redmon** [@pjreddie](https://www.github.com/pjreddie) for original Darknet(YOLOv4) version.

**Alexey** [@AlexeyAB](https://github.com/AlexeyAB) for modify Darknet(YOLOv4) version. (compatible with windows)

**Vittorio Mazzia** [@EscVM] (https://github.com/EscVM) for OIDv4_ToolKit data processing 

**The AI Guy** [@theAIGuysCode] (https://github.com/theAIGuysCode) for the amazing video one the subject and their tutoriel on how to install Darknet on window.

# Download and preprocess the data

1. Go to the OIDv4_ToolKit folder and install the requirements

2. Downloard a dataset of 1000 images of football

```
python main.py downloader --classes Football --type_csv train --limit 1000
```

3. convert the annotation

```
python convert_annotations.py
```

4. Move all the images and the data from the OIDv4_ToolKit/OID/Dataset/train to the darknet/data/obj folder

5. Add the custom file for ours project

Already done, but here his the file that was added:
* darknet/data/obj.names
* darknet/data/obj.data
* darknet/cfg/yolov3-custom.cfg

6. Generate the training file

from the darknet folder:

```
python generate_train.py
```

7. Download Pretrained Convolutional Weights

from : <https://pjreddie.com/darknet/yolo/>

(Ctrl+f: You can just download the weights for the convolutional layers here (76 MB).)
Put the file in the darknet/ folder

# Training the model for transfer learning 
It was done on Colab Pro with a Tesla P100-PCIE GPU due to Library issues when compiling Darknet on my windows machine

## Setup

1. Pre install
```
!apt update
!uname -m && cat /etc/*release
!gcc --version
!uname -r
```

2. Upload all the files from the transfer learning folder to the colab

3. Install opencv
```
%cd darknet/
!apt install libopencv-dev python-opencv ffmpeg
```

4. Compile Darknet

Compile Darknet with GPU=1 CUDNN=1 CUDNN_HALF=1 OPENCV=1 in the Makefile (or use the same settings with Cmake)

```
!sed -i 's/OPENCV=0/OPENCV=1/g' Makefile
!sed -i 's/GPU=0/GPU=1/g' Makefile
!sed -i 's/CUDNN_HALF=0/CUDNN_HALF=1/g' Makefile
!sed -i 's/CUDNN=0/CUDNN=1/g' Makefile

%pycat Makefile
```

5. convert the file created from window to the unix format (Might not be needed)

```
!sudo apt install dos2unix
!dos2unix ./data/train.txt
!dos2unix ./data/train.txt
!dos2unix ./data/obj.data
!dos2unix ./data/obj.names
!dos2unix ./cfg/yolov3-custom.cfg
```

6. Train the model (automatically save the weight)
```
!./darknet detector train data/obj.data cfg/yolov3-custom.cfg darknet53.conv.74
```
