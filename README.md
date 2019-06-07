Poker Vision:
Data-Preprocessing
Hello Franz,
I started a small project for poker game card detection. I started this project with just a small dataset for having about 41 classes and 320 images overall. To train a deep learning model, we have atleast 100 images per class. But unfortunately, i could only gather 320 images. So The next part i did to label the label by myself using labelImg. LabelImg is a tool to annotate data into VOC or COCO format. I have used the VOC format for the project. Here is the link on how to Use that:
https://github.com/tzutalin/labelImg

The next thing i did is data augmentation, data augmentation is a method to increase the data with a python script without labelling manually, I have used imgaug library for data augmentation. Here is the link of imgaug:
https://github.com/aleju/imgaug
For our project, i have rotated the image at 1 to 5 degree and change the brightness. Here are the steps:
1)First we have to convert xml file or annotation file into txt format, so to do this go to xmltolabels.ipynb
2)Now we have the images and xmls related to that, next thing is augment the data.I have added the comment section along with the code inorder to understand that. To do this click on augmentation.ipynb.
3)Now we have the augmented images and the text file related to that, but we are using pascal VOC format so we have to convert the text format into xml or annotation one.To do that go to labelstoxmls.ipynb

*Make sure to merge the data before doing next augmentation.* 

Now we have our data set, that means the base is ready now we bang the code.



Training:

Next thing or the most important thing we do is training our data. That is really cool. I have use the darkflow which is the tensorflow implementation of darknet YOLO. You need 4 steps to perform object detection in short. 
Now we will follow the steps to install darkflow and start our training, first you have to go to the below link and clone the repo....
$ git clone https://github.com/thtrieu/darkflow

1)Install prerequisite libraries
*Tensorflow(pip install tensorflow-gpu) *Atleast 1.0 version*
*Cython(pip install Cython)
*numpy(pip install numpy)
*opencv(pip install opencv-python)

2)clone the repository
$ git clone https://github.com/thtrieu/darkflow.git

3)go to darkflow folder,by typing cd darkflow

4)$ python3 setup.py build_ext --inplace

5)$ pip install .

Now, we have successfully installed darkflow.
The next step is to download pretrained weights of YOLO. Here’s the link to download weights.
https://drive.google.com/drive/folders/0B1tW_VtY7onidEwyQ2FtQVplWEU

Now we have installed weights for our model. Make a new folder named bin on inside darkflow folder and paste the weights over there. 
Next is we have to modify the labels.txt inside the darkflow folder according to our classes which we have total of 41.
Now the configurartion file,*The most important for our project*. Go to inside cfg folder and follow the steps:
I have use yolo weights so weight would be yolo.cfg(not exactly) for instance:
1)Create a copy of the configuration file yolo.cfg and rename it according to your preference yolo-voc-2c.cfg (It is crucial that you leave the original tiny-yolo-voc.cfg file unchanged).
2)In yolo-voc-2c.cfg, change classes in the [region] layer (the last layer) to the number of classes you are going to train for. In our case, classes are set to 41.
3)In yolo-voc-2c.cfg, change filters in the [convolutional] layer (the second to last layer) to num * (classes + 5). In our case, num is 5 and classes are 41 so 5 * (3 + 5) = 230 therefore filters are set to 230.

BOOOOM: 
We have modified everything according to our requirement,
Next or the second last step of training is to copy paste our data
To do that, make a folder train inside darfklow, and paste the image and xml folder.

The final step, open the terminal and go to darkflow folder and type:

flow --model cfg/yolo-voc-2c.cfg --load bin/yolo.weights --train --annotation train/(Annotations folder) --dataset train/(Image folder) --gpu 0.7
**for cpu training, no need to use --gpu

****Why should I leave the original tiny-yolo-voc.cfg file unchanged?
When darkflow sees you are loading tiny-yolo-voc.weights it will look for tiny-yolo-voc.cfg in your cfg/ folder and compare that configuration file to the new one you have set with --model cfg/tiny-yolo-voc-3c.cfg. In this case, every layer will have the same exact number of weights except for the last two, so it will load the weights into all layers up to the last two because they now contain different number of weights****
 
Now wait, until we reached the loss of 0.9-1.9. If the loss is ranging between some values, go to cfg file and decrease the learning rate by a factor 10 to the power -1. And type  
flow --model cfg/yolo-voc-2c.cfg --load -1 --train --annotation train/(Annotations folder) --dataset train/(Image folder) --gpu 0.7
To restart training from last checkpoint, use -1 as mentioned above.

Testing:
The weights has been saved at ckpt folder which is inside darkflow. Make sure to use all the three file i.e data0001, index,meta.(you will get to know once check point is start saving).
To test a demo on video, save a video on a darkflow folder and type:
 flow --model cfg/yolo-new.cfg --load (checkpoint number or -1) --demo videofile.avi --gpu 0.7.
 ***for images***
 
 
 flow --imgdir sample_img/ --model cfg/yourcfg --load (-1 or checkpoint number)

To save a video with predicted bounding box, add --saveVideo option.
 
 










