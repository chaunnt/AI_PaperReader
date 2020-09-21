# Tools and libraries

VoTT 1.7.2 (We use 1.7.2 because it supports YOLO format)

https://github.com/microsoft/VoTT/releases/tag/v1.7.2

# How to label
1. Open tool and select a folder to load image

![VoTT 1.7.2](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2009-14-21.png?raw=true)

2. Add your classes. Each class is a name of object that we want to detect. For example: name, number, dob, hometown, address for CCCD dataset.

![Add your classes](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2009-14-46.png?raw=true)

3. Label your dataset. 

(on the left) VoTT support many type of labeling for example: Points, Polygons, Rectangles (Recommended).

We can use number shortcut key (see in list classes at below) to type on keyboard for labeling faster

IMPORTANT !! we will draw a rectangle to object that we want to detect only, especially focus on special features of object.

For example: in this card, I use a rectangle to cover all curves and numbers of ID NUMBER. That means I want my model to focus on these curves in an image, especially, combine with numbers inside curves, it will be a feature points of this ID NUMBER => other cards / papers do not have this feature.

![Label your dataset](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2009-57-59.png?raw=true)

The more style of labeling dataset, the more accuracy that model will archieve. (BEWARE: careful with bad images - low resolution, blur...etc)

![Label your dataset](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2010-04-05.png?raw=true)

4. Export dataset to train model

Go to Menu (on top) > Object Detection > Export Tags

(Remember to click "Ctrl + S" to save project and all labeled data before exporting)

Select:

- format that we want to export (in this case, we use YOLO)

- select destination folder (in this case, we let it as default)

![Export dataset](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2010-29-43.png?raw=true)

5. Review your exported dataset

We usually have 3 things: 

- a JSON file to store all labels (coordinates and name). Save it, we will need it for later when we need to label more data or update / export old data.

- an original folder

- a generated folder that store exported data

![Review your exported dataset](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2010-32-44.png?raw=true)

in this case, we have a dataset in YOLO format includes:

- Data folder: folder that store training data

- cfg file: file config for training process

![YOLO format](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2010-32-58.png?raw=true)

we just copy it and paste in to darknet binary folder because darknet already support this format. Otherwise, please check your code. 

let's take a peek into data folder

![Dataset](https://github.com/chaunnt/AI_PaperReader/blob/master/Docs/images/Screenshot%20from%202020-09-21%2010-33-02.png?raw=true)

we have few things:

- obj: folder contains data for training includes all images and its labeled data

- obj.data: data file define number of class, train plan, test plan, and output folder after training

- obj.names: list of classes

- test.txt: test plan, these listed images will be used for testing purpose during training process.

- train.txt: train plan, these listed images will be used for training purpose during training process.
 






