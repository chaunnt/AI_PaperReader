# SETUP Darknet
Environments: 
- Ubuntu 18.04
- VGA NVDIA

Libraries dependence:
  - Opencv : 4.2.0 (built from source)
  - Cmake : 3.11
  - Cuda : 10.2
  - cuDNN : 7.0
  - Python : 3.6
  - Environmaent : virtualenv or anaconda 
  - Labeling tool : Vott 
  - OS : Ubuntu 18.04
  

## Labeling Dataset 
Now, we have 424 images is labeled in ftpserver.
Use Vott 1.72 tool for labeling. 
Then export dataset with Yolo format : Object Detection > Export Tags. Choose YoLo format then export.

Installation of VoTT: https://github.com/microsoft/VoTT/tree/v1.7.2

## Setup:
1. Build Opencv from source:
Install needded libraries. 
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential cmake unzip pkg-config
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
sudo apt-get install libjasper-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libgtk-3-dev
sudo apt-get install libatlas-base-dev gfortran
```
Get opencv source code :
```
cd ~
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.2.0.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.2.0.zip
```

Now, let’s unzip the archives:
```
unzip opencv.zip
unzip opencv_contrib.zip
```
Build source code by Cmake:
```
cd ~/opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE -D OPENCV_GENERATE_PKGCONFIG=YES    -D CMAKE_INSTALL_PREFIX=/usr/local     -D WITH_CUDA=OFF     -D INSTALL_PYTHON_EXAMPLES=OFF     -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules     -D OPENCV_ENABLE_NONFREE=ON     -D BUILD_EXAMPLES=OFF ..
make -j8
sudo make install
sudo ldconfig
```
2. Install Cuda 10.2:
Install
```
# Add NVIDIA package repositories
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804_10.1.243-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo apt-get update
wget http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt install ./nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
sudo apt-get update

# Install NVIDIA driver
sudo apt-get install --no-install-recommends nvidia-driver-418
# Reboot. Check that GPUs are visible using the command: nvidia-smi

# Install development and runtime libraries (~4GB)
sudo apt-get install --no-install-recommends \
    cuda-10-1 \
    libcudnn7=7.6.4.38-1+cuda10.1  \
    libcudnn7-dev=7.6.4.38-1+cuda10.1
```
Checking cuda:
```
nvidia-smi
```

3. Build Darknet:
Clone darknet repo : 
```
git clone https://github.com/AlexeyAB/darknet
```
Modify Makefile by applying this patch: 
```
diff --git a/Makefile b/Makefile
index 590db18..0c7d4f7 100644
--- a/Makefile
+++ b/Makefile
@@ -1,7 +1,7 @@
-GPU=0
-CUDNN=0
-CUDNN_HALF=0
-OPENCV=0
+GPU=1
+CUDNN=1
+CUDNN_HALF=1
+OPENCV=1
 AVX=0
 OPENMP=0
 LIBSO=0
@@ -60,7 +60,7 @@ CC=gcc
 endif
 
 CPP=g++
-NVCC=nvcc
+NVCC=/usr/local/cuda/bin/nvcc
 OPTS=-Ofast
 LDFLAGS= -lm -pthread
 COMMON= -Iinclude/ -I3rdparty/stb/include
@@ -86,8 +86,8 @@ endif
 ifeq ($(OPENCV), 1)
 COMMON+= -DOPENCV
 CFLAGS+= -DOPENCV
-LDFLAGS+= `pkg-config --libs opencv`
-COMMON+= `pkg-config --cflags opencv`
+LDFLAGS+= `pkg-config --libs opencv4`
+COMMON+= `pkg-config --cflags opencv4`
 endif
 
 ifeq ($(OPENMP), 1)
```
Build darknet:
```
make
```
4. Train data with darknet :

Download pre-trained weights for the convolutional layers and put to the directory `build\darknet\x64`
    * for `csresnext50-panet-spp.cfg` (133 MB): [csresnext50-panet-spp.conv.112](https://drive.google.com/file/d/16yMYCLQTY_oDlCIZPfn_sab6KD3zgzGq/view?usp=sharing)
    * for `yolov3.cfg, yolov3-spp.cfg` (154 MB): [darknet53.conv.74](https://pjreddie.com/media/files/darknet53.conv.74)
    * for `yolov3-tiny-prn.cfg , yolov3-tiny.cfg` (6 MB): [yolov3-tiny.conv.11](https://drive.google.com/file/d/18v36esoXCh-PsOKwyP2GWrpYDptDY8Zf/view?usp=sharing)
    * for `enet-coco.cfg (EfficientNetB0-Yolov3)` (14 MB): [enetb0-coco.conv.132](https://drive.google.com/file/d/1uhh3D6RSn0ekgmsaTcl-ZW53WBaUDo6j/view?usp=sharing)

Put labeled dataset to data directory.
Change file yolo-obj.cfg in dataset by guide in https://github.com/AlexeyAB/darknet/blob/master/README.md.
Train dataset:
```
./darknet detector train data/obj.data yolo-obj.cfg build/darknet/x64/darknet53.conv.74  -dont_show
```

## When should I stop training:
1. During training, you will see varying indicators of error, and you should stop when no longer decreases **0.XXXXXXX avg**:

  > Region Avg IOU: 0.798363, Class: 0.893232, Obj: 0.700808, No Obj: 0.004567, Avg Recall: 1.000000,  count: 8
  > Region Avg IOU: 0.800677, Class: 0.892181, Obj: 0.701590, No Obj: 0.004574, Avg Recall: 1.000000,  count: 8
  >
  > **9002**: 0.211667, **0.60730 avg**, 0.001000 rate, 3.868000 seconds, 576128 images
  > Loaded: 0.000000 seconds

  * **9002** - iteration number (number of batch)
  * **0.60730 avg** - average loss (error) - **the lower, the better**

  When you see that average loss **0.xxxxxx avg** no longer decreases at many iterations then you should stop training. The final avgerage loss can be from `0.05` (for a small model and easy dataset) to `3.0` (for a big model and a difficult dataset).

2. Once training is stopped, you should take some of last `.weights`-files from `darknet\build\darknet\x64\backup` and choose the best of them:

For example, you stopped training after 9000 iterations, but the best result can give one of previous weights (7000, 8000, 9000). It can happen due to overfitting. **Overfitting** - is case when you can detect objects on images from training-dataset, but can't detect objects on any others images. You should get weights from **Early Stopping Point**:

To get weights from Early Stopping Point:

  2.1. At first, in your file `obj.data` you must specify the path to the validation dataset `valid = valid.txt` (format of `valid.txt` as in `train.txt`), and if you haven't validation images, just copy `data\train.txt` to `data\valid.txt`.

  2.2 If training is stopped after 9000 iterations, to validate some of previous weights use this commands:

(If you use another GitHub repository, then use `darknet.exe detector recall`... instead of `darknet.exe detector map`...)

* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_7000.weights`
* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_8000.weights`
* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_9000.weights`

And comapre last output lines for each weights (7000, 8000, 9000):

Choose weights-file **with the highest mAP (mean average precision)** or IoU (intersect over union)
For more detail check https://github.com/AlexeyAB/darknet/blob/master/README.md.

## Test model:
```
./darknet detector test data/obj.data yolo-obj.cfg backup/yolo-obj_9000.weights 
```
Enter path of test image then check result in terminal or in predictions.jpg.

## Confluence: Where is kept ideas or architecture
  - https://nexlesoft.atlassian.net/wiki/spaces/AID/pages/406552581/Vietnamese+ID+card+OCR
  
## Reference :
  - https://www.pyimagesearch.com/2018/05/28/ubuntu-18-04-how-to-install-opencv/
  - https://github.com/AlexeyAB/darknet
  - https://www.tensorflow.org/install/gpu
