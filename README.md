# Setup

## 1. Require Libraries:

- Anaconda
- Python3
- Opencv 4.2.0
- pytesseract 4.0.0
- tesseract-vie
- tesseract-epo
- imutil

## 2. Install libraries:

### Libraries:

`sudo apt-get install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6`

### Anaconda:

`mkdir tmp`

`cd /tmp`

`curl -O https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh`

`bash Anaconda3-2019.03-Linux-x86_64.sh`

`source ~/.bashrc`

`conda create --name cccd_detection python=3`

`conda activate cccd_detection`

### OpenCV 4.2.0:

`conda install -c conda-forge opencv=4.2.0`

### Tesseract:

`conda install -c conda-forge pytesseract`

`sudo apt-get install tesseract-ocr-vie`

`sudo apt-get install tesseract-ocr-epo`

### Other python libs:

`conda install -c conda-forge imutils`

### Vietnamese spell checker
`sudo apt install python-dev libhunspell-dev`
`conda install -c conda-forge hunspell`

## 3. Application Require:

- Yolo files: `Yolo/yolo-<name>.names, .cfg, .weights
- Expected data: `cccd_infos.json` (place in the same folder with images)

```
  {
    "cccd_info":[
        {
            "file": "<file_name>",
            "name":"<Expected Name>",
            "number":"<Expected Number>"
        },
        {
            "file": "1.jpg",
            "name":"PHẠM NGỌC HÀ PHƯƠNG",
            "number":"056195000041"
        }
    ]
  }
```

- Select Yolo model and Test images folder:
  `c_DEFAULT_TEST_IMAGES_FOLDER = '/home/nexle-user/AI_ID/AI_Dataset/RawData/Best/'`

## 4. Run test:

- Run test at CCCD_Detector.py file: python CCCD_Detector.py
- Get result on <ImageFolder>/cccd_result.csv

### End 2 End mode:

- Run test:

```
python3 CCCD_DetectorE2E.py -f <string_image_path>
```

Example: python3 CCCD_DetectorE2E.py -f "/home/nexle-user/AI_ID/AI_ID_Detection/Applications/CCCDDetector_Python/24.jpg"

- Get result at file "result.json"

```
{
  "name": "\u0110\u1ed6 TRUNG QUANG DI\u1ec6U",
  "nonAccent": "DO TRUNG QUANG DIEU",
  "id": "044094002131"
}
```

# Project files:

```
|- Yolo
|    |- yolo-obj.names
|    |- yolo-obj.cfg
|    |- yolo-obj.weights
|- CCCD_Detector.py (main file)
|- CSV_Export.py (create and write csv file for result)
|- Json_Data.py (load expected data)
|- Tesseract_OCR.py (extract name, number from image)
|- Util.py (remove chars, increase contrast, remove blue text)
|- Warp.py (rotate image to horizontal)
```
