# yolov3_embeddedCompress
From this Project you can get fews method to compress and acceleration yolov3 network.

In the process of creating my project, I have referenced some git projects and papers in cvpr, 
 thanks to these guys. 

I will continue to update afterwards, please stay tuned.

[What tricks I used]
=========
Multiple Threads
------------
If you know multiple threads run in arm of X86 chips, you must know `Openmp`. 

Next picture is how Openmp runs. It has many tricks to ensure work well between threads.

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/orig.png)

The result of use `openmp` in project is:

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/0.png)


Kernel Mask
------------
It a regular method to decrease the computation of conv layers. But the key point is how to set which kernel is 
important and which kernel need to delete.

In this project, I referenced the paper of `Accelerating Convolutional Networks via Global & Dynamic Filter Pruning `

The accelerating result of use `kernel mask` in project is:

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/1.png)


Weights Prune
------------
Because this method is very simple, you just need to set weights < threshold to 0, so I don't need to introduce it anymore.

The accelerating result of use `< kernel mask & weights prune >` in project is:

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/2.png)


L1 Regularization
------------
L1 Regularization can be regard to another way to decrease kernels, the principle is like kernel decrease with BN 
parameters in other papers.

Yolo use L2 regularization as default, so you need to change it to L1 in code. This method has a disadvantage, you need to change cfg files
after every epoch end (after one epoch train you know how many kernels to leave in every conv layer)

If you want to know more about L2 and L1 regularization in yolo, you can go to [my blog](https://blog.csdn.net/Artyze/article/details/87934826)

The accelerating result of use  `L1 Regulatization` in project is:

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/result.png)

Quantization
------------
This module were imported from [AlexeyAB's github repo](https://github.com/AlexeyAB/yolo2_light)

As he introduced, this quantization method is referenced nvidia's TensorRT theory.

But when I test this module, it works not good, recently I added google's quantization method code to it.

The accelerating result of use  `Quantization` in project is:

![Image text](https://github.com/ArtyZe/yolo_segmentation/blob/master/3.png)

Depthwise Conv
-----------
The key point of Mobilenet, it has been merged in yolov3 by the author, I optimized the code so that `l.groups`
 can be used in every module.
 

[How to train the repo] 
=========
      1. analysis your original net, decide which module you need to use  
		  
      2. change makefile and open modules, for example, if you want to use image mask, you just need to set 
      `MASK=1`
  
	  3. start train
	  
	    ./darknet detector train [data_file path] cfg/yolov3.cfg [pretrain weights file] 
	     
	   4. start test
	   set 'GPU=0'
	   
	   ./darknet detector test [data_file path] cfg/yolov3.cfg [weights file] [image file to detect]
	   

[Something more]
=========
    1. I added F1 score test code, the command is :
    
    ./darknet detector f1 [data_file path] cfg/yolov3.cfg [weights file] 
    
    2. I also have some other modules such as `Hash Compress` `Google Quantization`  `Huffman Compress`, but I can't give all of them to you with other 
    reasons.
    
    3. When I test all the method in tiny net(not in VGG), it can decrease inference time by 30%~50% with very little f1 decrease,
    and if you want faster, use quantization, it will surprise you!!!!!
    

If you want to use my code, please let me know!!!!
  
