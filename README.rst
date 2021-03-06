#coding:utf-8


================================
PredNet in chainer
================================
Kenta Tanaka & Eiji Watanabe, 2020



================================
Overview
================================
This software learns a still image sequence generated from a video by a deep learning algorithm, and generates predicted video.



================================
[Special attention]
Required for operation
================================
sudo pip install tensorboard-chainer
(sudo pip install - U tensorboard-chainer)



================================
Test environment
================================
OS: Ubuntu 16.04.5LTS
Python: 2.7.12
GPU: Nvidia GTX1080ti
chainer==6.7.0
tensorboard-chainer==0.5.3



================================
Requirements
================================
* python-opencv
* g++
* python-dev
* libhdf5-dev
* PIL
* protobuf
* filelock
* cython
* setuptools
* chainer
* numpy
* cupy
* net
* cv2
* os
* sys
* argparse
* datetime
* setuptools
* tensorflow
* tensorboard-chainer
* 
* GPU driver, CUDA Toolkit and cudnn



================================
Preparing data
================================
Put the target movie file (mp4) in "data" folder.
Execute the following command to generate still images from the movie.

$ python generate_image.py data/YOUR_VIDEO -d data

To change the width of the image, use the -w option.

$ python generate_image.py data/YOUR_VIDEO -d data -w 160

The height can also be specified with -g option.

$ python generate_image.py data/YOUR_VIDEO -d data -w 160 -g 120

If no option is specified, the width is fixed to 160 and the height is fixed to 120.
Also, "train_list.txt" describing the list of files used for training
and "test_list.txt" describing the list of files used for testing are saved.
By default, the latter half of the video will be the test data.

$ python generate_image.py data/YOUR_VIDEO -d data -s 100
You can specify the number of frames to skip with -s option.

$ python generate_image.py data/YOUR_VIDEO -d data -n 50
With -n option, you can copy n frames of the same frame.



================================
Training
================================
Execute the following command for training.

$ python PredNet/main.py -i train_data/train_train_list.txt


To use GPU, execute as follows.

$ python PredNet/main.py -i train_data/train_list.txt -g 0
$ python PredNet/main.py -i train_data/train_list.txt -g 0 --tensorboard

The trained models will be saved in "models" folder.



================================
Prediction
================================
Next, generate predicted frames with the following command.

$ python PredNet/main.py -i train_data/test_list.txt --test --initmodel models/YOUR_MODEL -l NUMBER_OF_INPUT_IMAGES --ext NUMBER_OF_PREDICTED_IMAGES -g 0

Predicted images (test_#y_ 0.jpg) of all the images described in "test_list.txt" are generated in "result folder".
Furthermore, for each length of the input image, images (test_#y_1.jpg, test_#y_2.jpg, ...) corresponding to the number of predicted frames are generated.

[SAMPLE]
$ python PredNet/main.py -i test_data/test_list.txt --test --initmodel sample_models/fpsi_8v_500k.model -l 20 --ext 3 -g 0
$ python PredNet/main.py -i test_data/test_list.txt --test --initmodel sample_models/fpsi_8v_500k.model -l 20 --ext 3 -g 0 --tensorboard

a sample weight model:
https://doi.org/10.6084/m9.figshare.11931222.v3



================================
Options
================================
parser = argparse.ArgumentParser(
description='PredNet')
parser.add_argument('--images', '-i', default='', help='Path to image list file')
parser.add_argument('--sequences', '-seq', default='', help='Path to sequence list file')
parser.add_argument('--gpu', '-g', default=-1, type=int,
                    help='GPU ID (negative value indicates CPU)')
parser.add_argument('--root', '-r', default='.',
                    help='Root directory path of sequence and image files')
parser.add_argument('--initmodel', default='',
                    help='Initialize the model from given file')
parser.add_argument('--resume', default='',
                    help='Resume the optimization from snapshot')
parser.add_argument('--size', '-s', default='160,120',
                    help='Size of target images. width,height (pixels)')
parser.add_argument('--channels', '-c', default='3,48,96,192',
                    help='Number of channels on each layers')
parser.add_argument('--offset', '-o', default='0,0',
                    help='Center offset of clipping input image (pixels)')
parser.add_argument('--input_len', '-l', default=50, type=int,
                    help='Input frame length fo extended prediction on test (frames)')
parser.add_argument('--ext', '-e', default=10, type=int,
                    help='Extended prediction on test (frames)')
parser.add_argument('--bprop', default=20, type=int,
                    help='Back propagation length (frames)')
parser.add_argument('--save', default=10000, type=int,
                    help='Period of save model and state (frames)')
parser.add_argument('--period', default=1000000, type=int,
                    help='Period of training (frames)')
parser.add_argument('--xyz', default=0, type=int,
                    help='Save xyz_images, --xyz 1')
parser.add_argument('--test', dest='test', action='store_true')
parser.add_argument('--tensorboard', dest='tensorboard', action='store_true')
parser.set_defaults(test=False, tensorboard=False)
args = parser.parse_args()



================================
How to Use Tensorboard

https://github.com/neka-nat/tensorboard-chainer
================================
To use tensorboard, install "tensorboard-chainer" by execute the following command.

$ sudo pip install tensorflow

then
$ sudo pip install tensorboard-chainer
(for update, $ sudo pip install -U tensorboard-chainer)


To update "tensorboard-chainer",execute the following command.

$ sudo pip install -U tensorboard-chainer


To get tensorboard log, use --tensorboard option.
[SAMPLE]
$ python PredNet/main.py -i train_data/train_list.txt -g 0 --tensorboard
$ python PredNet/main.py -i train_data/test_list.txt --test --initmodel sample_models/fpsi_8v_500k.model -l 20 --ext 3 -g 0 --tensorboard


To use tensorboard-chainer, Execute the following command,

$ tensorboard --logdir runs

and access "http://localhost:6006" in your browser.




================================
Reference
================================
"https://github.com/neka-nat/" [Tanaka's site]
"https://coxlab.github.io/prednet/" [Original PredNet]
"https://github.com/quadjr/PredNet" [Implemented by chainer]
"https://doi.org/10.3389/fpsyg.2018.00345" [Illusion Paper]


