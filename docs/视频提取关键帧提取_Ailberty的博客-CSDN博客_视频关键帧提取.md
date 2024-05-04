<!--yml
category: 视频
date: 2022-05-04 19:47:04
-->

# 视频提取关键帧提取_Ailberty的博客-CSDN博客_视频关键帧提取

> 来源：[https://blog.csdn.net/Ailberty/article/details/109581016](https://blog.csdn.net/Ailberty/article/details/109581016)

# 视频提取关键帧提取

## 前言

正所谓做工作要做好记录，现在，我要开始记录啦。

## 一、什么是关键帧和为什么要提取关键帧？

1、每个视频都是一个图像序列，其内容比一张图像丰富很多,表现力强，信息量大。对视频的分析通常是基于视频帧，但视频帧通常存在大量冗余，对视频帧的提取也存在漏帧、冗余的现象。视频关键帧提取则主要体现视频中各个镜头的显著特征，通过视频关键帧提取能够有效减少视频检索所需要花费的时间，并能够增强视频检索的精确度。

> 2、关键帧定义：把图像坐标系中每个“视频帧”都叠加在一起，这时镜头中视频帧的特征矢量会在空间中呈现出一个轨迹的状态，而与轨迹中特征值进行对应的“帧”即可称之为关键帧[1]

3、视频具有层次化结构，由场景、镜头和帧三个逻辑单元组成。视频检索常基于帧进行，因此，提取视频的关键帧至关重要[2]

## 二、关键帧提取方法

1、关键帧提取思想：对视频序列采用镜头分割的方式，然后在镜头当中获得内容关键帧提取，接着利用“关键帧”来获得底层的形状、纹理和颜色等特征。

2、关键帧提取方法：

（1）全图像序列

​ 镜头边界方法是将镜头中的第一帧和最后一帧（或中间帧）作为关键帧。该方法简单易行，适于内容活动性小或内容保持不变的镜头，但未考虑镜头视觉内容的复杂性，限制了镜头关键帧的个数，提取的关键帧代表性不强，效果不够稳定。

（2）压缩视频

(3) 自定义k值聚类和内容分析的关键帧提取方法[2]

（4）a 基于抽样的关键帧提取[3]

​ 基于抽样的方法是通过随机抽取或在规定的时间间隔内随机抽取视频帧。这种方法简单不实用。

​ b 基于颜色特征的关键帧提取

​ c 基于运动分析的关键帧提取

​ d 基于镜头边界的关键帧提取(*)

```
 e 基于视频内容的关键帧提取(*) 
```

​ f 基于聚类的关键帧提取 (可能是最合适的一个了)，我要识别的类别已经确定。

（5）使用3D-CNN提取关键帧[4]

​ 他提出了一种基于语义的视频关键帧提取算法，该算法首先使用层次聚类算法对视频关键帧进行初步提取；然后结合语义相关算法对初步提取的关键帧进行直方图对比去掉冗余帧，确定视频的关键帧；最后通过与其他算法比较，本文提出的算法提取的关键帧冗余度相对小。

（6）首先利用卷积自编码器提取视频帧的深度特征，对其进行 K-means 聚类，在每类视频帧中采用清晰度筛选取出最清晰的视频帧作为初次提取的关键帧；然后利用点密度方法对初次提取的关键帧进行二次优化，得到最终提取的关键帧进行手语识别.[5]

(7) 视频关键帧提取方法一般可分为四大类：

​ 第一类：基于图像内容的方法

​ 第二类：基于运动分析的方法

​ 第三类：基于轨迹曲线点密度特征的关键帧检测算法

​ 第四类：目前主流方法：基于聚类的方法

（8）帧间差法

​ 源自: [以下代码出自zyb_as的github](https://github.com/monkeyDemon/AI-Toolbox/blob/master/preprocess%20ToolBox/keyframes_extract_tool/keyframes_extract_diff.py)

```
# -*- coding: utf-8 -*-
"""
帧间最大值法
Created on Tue Dec  4 16:48:57 2018
keyframes extract tool
this key frame extract algorithm is based on interframe difference.
The principle is very simple
First, we load the video and compute the interframe difference between each frames
Then, we can choose one of these three methods to extract keyframes, which are 
all based on the difference method:

1\. use the difference order
    The first few frames with the largest average interframe difference 
    are considered to be key frames.
2\. use the difference threshold
    The frames which the average interframe difference are large than the 
    threshold are considered to be key frames.
3\. use local maximum
    The frames which the average interframe difference are local maximum are 
    considered to be key frames.
    It should be noted that smoothing the average difference value before 
    calculating the local maximum can effectively remove noise to avoid 
    repeated extraction of frames of similar scenes.
After a few experiment, the third method has a better key frame extraction effect.
The original code comes from the link below, I optimized the code to reduce 
unnecessary memory consumption.
https://blog.csdn.net/qq_21997625/article/details/81285096
@author: zyb_as
""" 
import cv2
import operator
import numpy as np
import matplotlib.pyplot as plt
import sys
from scipy.signal import argrelextrema

def smooth(x, window_len=13, window='hanning'):
    """smooth the data using a window with requested size.

    This method is based on the convolution of a scaled window with the signal.
    The signal is prepared by introducing reflected copies of the signal 
    (with the window size) in both ends so that transient parts are minimized
    in the begining and end part of the output signal.

    input:
        x: the input signal 
        window_len: the dimension of the smoothing window
        window: the type of window from 'flat', 'hanning', 'hamming', 'bartlett', 'blackman'
            flat window will produce a moving average smoothing.
    output:
        the smoothed signal

    example:
    import numpy as np    
    t = np.linspace(-2,2,0.1)
    x = np.sin(t)+np.random.randn(len(t))*0.1
    y = smooth(x)

    see also: 

    numpy.hanning, numpy.hamming, numpy.bartlett, numpy.blackman, numpy.convolve
    scipy.signal.lfilter

    TODO: the window parameter could be the window itself if an array instead of a string   
    """
    print(len(x), window_len)
    # if x.ndim != 1:
    #     raise ValueError, "smooth only accepts 1 dimension arrays."
    #
    # if x.size < window_len:
    #     raise ValueError, "Input vector needs to be bigger than window size."
    #
    # if window_len < 3:
    #     return x
    #
    # if not window in ['flat', 'hanning', 'hamming', 'bartlett', 'blackman']:
    #     raise ValueError, "Window is on of 'flat', 'hanning', 'hamming', 'bartlett', 'blackman'"

    s = np.r_[2 * x[0] - x[window_len:1:-1],
              x, 2 * x[-1] - x[-1:-window_len:-1]]
    #print(len(s))

    if window == 'flat':  # moving average
        w = np.ones(window_len, 'd')
    else:
        w = getattr(np, window)(window_len)
    y = np.convolve(w / w.sum(), s, mode='same')
    return y[window_len - 1:-window_len + 1]

class Frame:
    """class to hold information about each frame

    """
    def __init__(self, id, diff):
        self.id = id
        self.diff = diff

    def __lt__(self, other):
        if self.id == other.id:
            return self.id < other.id
        return self.id < other.id

    def __gt__(self, other):
        return other.__lt__(self)

    def __eq__(self, other):
        return self.id == other.id and self.id == other.id

    def __ne__(self, other):
        return not self.__eq__(other)

def rel_change(a, b):
   x = (b - a) / max(a, b)
   print(x)
   return x

if __name__ == "__main__":
    print(sys.executable)
    #Setting fixed threshold criteria
    USE_THRESH = False
    #fixed threshold value
    THRESH = 0.6
    #Setting fixed threshold criteria
    USE_TOP_ORDER = False
    #Setting local maxima criteria
    USE_LOCAL_MAXIMA = True
    #Number of top sorted frames
    NUM_TOP_FRAMES = 50

    #Video path of the source file
    videopath = 'pikachu.mp4'
    #Directory to store the processed frames
    dir = './extract_result/'
    #smoothing window size
    len_window = int(50)

    print("target video :" + videopath)
    print("frame save directory: " + dir)
    # load video and compute diff between frames
    cap = cv2.VideoCapture(str(videopath)) 
    curr_frame = None
    prev_frame = None 
    frame_diffs = []
    frames = []
    success, frame = cap.read()
    i = 0 
    while(success):
        luv = cv2.cvtColor(frame, cv2.COLOR_BGR2LUV)
        curr_frame = luv
        if curr_frame is not None and prev_frame is not None:
            #logic here
            diff = cv2.absdiff(curr_frame, prev_frame)
            diff_sum = np.sum(diff)
            diff_sum_mean = diff_sum / (diff.shape[0] * diff.shape[1])
            frame_diffs.append(diff_sum_mean)
            frame = Frame(i, diff_sum_mean)
            frames.append(frame)
        prev_frame = curr_frame
        i = i + 1
        success, frame = cap.read()   
    cap.release()

    # compute keyframe
    keyframe_id_set = set()
    if USE_TOP_ORDER:
        # sort the list in descending order
        frames.sort(key=operator.attrgetter("diff"), reverse=True)
        for keyframe in frames[:NUM_TOP_FRAMES]:
            keyframe_id_set.add(keyframe.id) 
    if USE_THRESH:
        print("Using Threshold")
        for i in range(1, len(frames)):
            if (rel_change(np.float(frames[i - 1].diff), np.float(frames[i].diff)) >= THRESH):
                keyframe_id_set.add(frames[i].id)   
    if USE_LOCAL_MAXIMA:
        print("Using Local Maxima")
        diff_array = np.array(frame_diffs)
        sm_diff_array = smooth(diff_array, len_window)
        frame_indexes = np.asarray(argrelextrema(sm_diff_array, np.greater))[0]
        for i in frame_indexes:
            keyframe_id_set.add(frames[i - 1].id)

        plt.figure(figsize=(40, 20))
        plt.locator_params(numticks=100)
        plt.stem(sm_diff_array)
        plt.savefig(dir + 'plot.png')

    # save all keyframes as image
    cap = cv2.VideoCapture(str(videopath))
    curr_frame = None
    keyframes = []
    success, frame = cap.read()
    idx = 0
    while(success):
        if idx in keyframe_id_set:
            name = "keyframe_" + str(idx) + ".jpg"
            cv2.imwrite(dir + name, frame)
            keyframe_id_set.remove(idx)
        idx = idx + 1
        success, frame = cap.read()
    cap.release() 
```

运动分析流光法进行关键帧提取

源自：[AillenAnthony](https://github.com/AllenAnthony/Key-Frame)的github

```
# Scripts to try and detect key frames that represent scene transitions
# in a video. Has only been tried out on video of slides, so is likely not
# robust for other types of video.

# 1\. 基于图像信息
# 2\. 基于运动分析(光流分析)

import cv2
import argparse
import json
import os
import numpy as np
import errno

def getInfo(sourcePath):
    cap = cv2.VideoCapture(sourcePath)
    info = {
        "framecount": cap.get(cv2.CAP_PROP_FRAME_COUNT),
        "fps": cap.get(cv2.CAP_PROP_FPS),
        "width": int(cap.get(cv2.CAP_PROP_FRAME_WIDTH)),
        "heigth": int(cap.get(cv2.CAP_PROP_FRAME_Heigth)),
        "codec": int(cap.get(cv2.CAP_PROP_FOURCC))
    }
    cap.release()
    return info

def scale(img, xScale, yScale):
    res = cv2.resize(img, None,fx=xScale, fy=yScale, interpolation = cv2.INTER_AREA)
    return res

def resize(img, width, heigth):
    res = cv2.resize(img, (width, heigth), interpolation = cv2.INTER_AREA)
    return res

#
# Extract [numCols] domninant colors from an image
# Uses KMeans on the pixels and then returns the centriods
# of the colors
#
def extract_cols(image, numCols):
    # convert to np.float32 matrix that can be clustered
    Z = image.reshape((-1,3))
    Z = np.float32(Z)

    # Set parameters for the clustering
    max_iter = 20
    epsilon = 1.0
    K = numCols
    criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, max_iter, epsilon)
    labels = np.array([])
    # cluster
    compactness, labels, centers = cv2.kmeans(Z, K, labels, criteria, 10, cv2.KMEANS_RANDOM_CENTERS)

    clusterCounts = []
    for idx in range(K):
        count = len(Z[labels == idx])
        clusterCounts.append(count)

    #Reverse the cols stored in centers because cols are stored in BGR
    #in opencv.
    rgbCenters = []
    for center in centers:
        bgr = center.tolist()
        bgr.reverse()
        rgbCenters.append(bgr)

    cols = []
    for i in range(K):
        iCol = {
            "count": clusterCounts[i],
            "col": rgbCenters[i]
        }
        cols.append(iCol)

    return cols

#
# Calculates change data one one frame to the next one.
#
def calculateFrameStats(sourcePath, verbose=False, after_frame=0):  # 提取相邻帧的差别
    cap = cv2.VideoCapture(sourcePath)#提取视频

    data = {
        "frame_info": []
    }

    lastFrame = None
    while(cap.isOpened()):
        ret, frame = cap.read()
        if frame == None:
            break

        frame_number = cap.get(cv2.CAP_PROP_POS_FRAMES) - 1

        # Convert to grayscale, scale down and blur to make
        # calculate image differences more robust to noise
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)      # 提取灰度信息
        gray = scale(gray, 0.25, 0.25)      # 缩放为原来的四分之一
        gray = cv2.GaussianBlur(gray, (9,9), 0.0)   # 做高斯模糊

        if frame_number < after_frame:
            lastFrame = gray
            continue

        if lastFrame != None:

            diff = cv2.subtract(gray, lastFrame)        # 用当前帧减去上一帧

            diffMag = cv2.countNonZero(diff)        # 计算两帧灰度值不同的像素点个数

            frame_info = {
                "frame_number": int(frame_number),
                "diff_count": int(diffMag)
            }
            data["frame_info"].append(frame_info)

            if verbose:
                cv2.imshow('diff', diff)
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    break

        # Keep a ref to this frame for differencing on the next iteration
        lastFrame = gray

    cap.release()
    cv2.destroyAllWindows()

    #compute some states
    diff_counts = [fi["diff_count"] for fi in data["frame_info"]]
    data["stats"] = {
        "num": len(diff_counts),
        "min": np.min(diff_counts),
        "max": np.max(diff_counts),
        "mean": np.mean(diff_counts),
        "median": np.median(diff_counts),
        "sd": np.std(diff_counts)   # 计算所有帧之间, 像素变化个数的标准差
    }
    greater_than_mean = [fi for fi in data["frame_info"] if fi["diff_count"] > data["stats"]["mean"]]
    greater_than_median = [fi for fi in data["frame_info"] if fi["diff_count"] > data["stats"]["median"]]
    greater_than_one_sd = [fi for fi in data["frame_info"] if fi["diff_count"] > data["stats"]["sd"] + data["stats"]["mean"]]
    greater_than_two_sd = [fi for fi in data["frame_info"] if fi["diff_count"] > (data["stats"]["sd"] * 2) + data["stats"]["mean"]]
    greater_than_three_sd = [fi for fi in data["frame_info"] if fi["diff_count"] > (data["stats"]["sd"] * 3) + data["stats"]["mean"]]

    # 统计其他信息
    data["stats"]["greater_than_mean"] = len(greater_than_mean)
    data["stats"]["greater_than_median"] = len(greater_than_median)
    data["stats"]["greater_than_one_sd"] = len(greater_than_one_sd)
    data["stats"]["greater_than_three_sd"] = len(greater_than_three_sd)
    data["stats"]["greater_than_two_sd"] = len(greater_than_two_sd)

    return data

#
# Take an image and write it out at various sizes.
#
# TODO: Create output directories if they do not exist.
#
def writeImagePyramid(destPath, name, seqNumber, image):
    fullPath = os.path.join(destPath, "full", name + "-" + str(seqNumber) + ".png")
    halfPath = os.path.join(destPath, "half", name + "-" + str(seqNumber) + ".png")
    quarterPath = os.path.join(destPath, "quarter", name + "-" + str(seqNumber) + ".png")
    eigthPath = os.path.join(destPath, "eigth", name + "-" + str(seqNumber) + ".png")
    sixteenthPath = os.path.join(destPath, "sixteenth", name + "-" + str(seqNumber) + ".png")

    hImage = scale(image, 0.5, 0.5)
    qImage = scale(image, 0.25, 0.25)
    eImage = scale(image, 0.125, 0.125)
    sImage = scale(image, 0.0625, 0.0625)

    cv2.imwrite(fullPath, image)
    cv2.imwrite(halfPath, hImage)
    cv2.imwrite(quarterPath, qImage)
    cv2.imwrite(eigthPath, eImage)
    cv2.imwrite(sixteenthPath, sImage)

#
# Selects a set of frames as key frames (frames that represent a significant difference in
# the video i.e. potential scene chnges). Key frames are selected as those frames where the
# number of pixels that changed from the previous frame are more than 1.85 standard deviations
# times from the mean number of changed pixels across all interframe changes.
#
def detectScenes(sourcePath, destPath, data, name, verbose=False):
    destDir = os.path.join(destPath, "images")

    # TODO make sd multiplier externally configurable
    #diff_threshold = (data["stats"]["sd"] * 1.85) + data["stats"]["mean"]
    diff_threshold = (data["stats"]["sd"] * 2.05) + (data["stats"]["mean"])

    cap = cv2.VideoCapture(sourcePath)
    for index, fi in enumerate(data["frame_info"]):
        if fi["diff_count"] < diff_threshold:
            continue

        cap.set(cv2.CAP_PROP_POS_FRAMES, fi["frame_number"])
        ret, frame = cap.read()

        # extract dominant color
        small = resize(frame, 100, 100)
        cols = extract_cols(small, 5)
        data["frame_info"][index]["dominant_cols"] = cols

        if frame != None:
            writeImagePyramid(destDir, name, fi["frame_number"], frame)

            if verbose:
                cv2.imshow('extract', frame)
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    break

    cap.release()
    cv2.destroyAllWindows()
    return data

def makeOutputDirs(path):
    try:
        #todo this doesn't quite work like mkdirp. it will fail
        #fi any folder along the path exists. fix
        os.makedirs(os.path.join(path, "metadata"))
        os.makedirs(os.path.join(path, "images", "full"))
        os.makedirs(os.path.join(path, "images", "half"))
        os.makedirs(os.path.join(path, "images", "quarter"))
        os.makedirs(os.path.join(path, "images", "eigth"))
        os.makedirs(os.path.join(path, "images", "sixteenth"))
    except OSError as exc: # Python >2.5
        if exc.errno == errno.EEXIST and os.path.isdir(path):
            pass
        else: raise

if __name__ == '__main__':

    parser = argparse.ArgumentParser()

    # parser.add_argument('-s','--source', help='source file', required=True)
    # parser.add_argument('-d', '--dest', help='dest folder', required=True)
    # parser.add_argument('-n', '--name', help='image sequence name', required=True)
    # parser.add_argument('-a','--after_frame', help='after frame', default=0)
    # parser.add_argument('-v', '--verbose', action='store_true')
    # parser.set_defaults(verbose=False)

    parser.add_argument('-s','--source', help='source file', default="dataset/video/wash_hand/00000.mp4")
    parser.add_argument('-d', '--dest', help='dest folder', default="dataset/video/key_frame")
    parser.add_argument('-n', '--name', help='image sequence name', default="")
    parser.add_argument('-a','--after_frame', help='after frame', default=0)
    parser.add_argument('-v', '--verbose', action='store_true')
    parser.set_defaults(verbose=False)

    args = parser.parse_args()

    if args.verbose:
        info = getInfo(args.source)
        print("Source Info: ", info)

    makeOutputDirs(args.dest)

    # Run the extraction
    data = calculateFrameStats(args.source, args.verbose, int(args.after_frame))
    data = detectScenes(args.source, args.dest, data, args.name, args.verbose)
    keyframeInfo = [frame_info for frame_info in data["frame_info"] if "dominant_cols" in frame_info]

    # Write out the results
    data_fp = os.path.join(args.dest, "metadata", args.name + "-meta.json")
    with open(data_fp, 'w') as f:
        data_json_str = json.dumps(data, indent=4)
        f.write(data_json_str)

    keyframe_info_fp = os.path.join(args.dest, "metadata", args.name + "-keyframe-meta.json")
    with open(keyframe_info_fp, 'w') as f:
        data_json_str = json.dumps(keyframeInfo, indent=4)
        f.write(data_json_str) 
```

(9) [使用ffmpeg进行关键帧提取](https://blog.csdn.net/justloveyou_/article/details/88076675)

代码见于[此处](https://github.com/aryan-jadon/Video-Summarization-using-Keyframe-Extraction)

(10) 使用K-means聚类法

源代码见于[此处](https://github.com/AilbertOne/Video-key-frame-extraction)

```
filenames=dir('images/*.jpg');
%file_name = fly-1;
num=size(filenames,1);  %输出filenames中文件图片的个数
key=zeros(1,num);  %一行，num列都是0  [0,0,0,0,0,0,0,0,0,0,0,0,....0,0,0,0]
cluster=zeros(1,num);   %[0,0,0,0,0,0,0,0,0,0,0,0,....0,0,0,0]
clusterCount=zeros(1,num);  %各聚类有的帧数   [0,0,0,0,0,0,0,0,0,0,0,0,....0,0,0,0]
count=0;        %聚类的个数    

%threshold=0.75;  %阈值越大帧越多
%airplane这个视频阈值设为0.93比较合适   0.95更好
%********************************************************阈值**************************************************************%
threshold=0.91;  %阈值
centrodR=zeros(num,256);   %聚类质心R的直方图   第一帧图片256个初始化全部为0，第二帧也是，其余帧都是  %%%后面相似度大加入一帧后会对其进行调整
centrodG=zeros(num,256);   %聚类质心G的直方图
centrodB=zeros(num,256);   %聚类质心B的直方图

if num==0
    error('Sorry, there is no pictures in images folder!');
else
    %令首帧形成第一个聚类
    img=imread(strcat('images/',filenames(1).name));
    count=count+1;    %产生第一个聚类
    [preCountR,x]=imhist(img(:,:,1));   %red histogram    得到红色的直方图一共256个数值，每个数值有多少作为直方图的高度
    [preCountG,x]=imhist(img(:,:,2));   %green histogram
    [preCountB,x]=imhist(img(:,:,3));   %blue histogram

    cluster(1)=1;   %设定第一个聚类选取的关键帧初始为首帧  cluster变为了（1,0,0,0,0,......,0,0,0）cluster(1)是改变了第一个元素
    clusterCount(1)=clusterCount(1)+1;%clusterCount(1)为0，加1，变为1,最终 clusterCount(1)为[1,0,0,0,.....,0,0,0]
    centrodR(1,:)=preCountR; % centrodR本来是num（帧个数）行，256列，全部为0.。现在第一行为第一帧的红色直方图各个数值的高度
    centrodG(1,:)=preCountG;
    centrodB(1,:)=preCountB;

    visit = 1;
    for k=2:num
        img=imread(strcat('images/',filenames(k).name));  %循环读取每一帧，首先是第2帧
        [tmpCountR,x]=imhist(img(:,:,1));   %red histogram  得到红色分量直方图  第二幅图片的红色直方图
        [tmpCountG,x]=imhist(img(:,:,2));   %green histogram
        [tmpCountB,x]=imhist(img(:,:,3));   %blue histogram

        clusterGroupId=1;  %新定义的一个变量clusterGroupId为1
        maxSimilar=0;   %新定义，相似度

        for clusterCountI= visit:count          %目前 count为1   定义新变量clusterCountI  I来确定这一帧归属于第一个聚类还是第二个聚类
            sR=0;
            sG=0;
            sB=0;
            %运用颜色直方图法的差别函数
            for j=1:256
                sR=min(centrodR(clusterCountI,j),tmpCountR(j))+sR;%，j从1到256，第一帧中R的所有值256个亮度  以及第二帧的红色直方图所有高度值  进行比较选最小的
                sG=min(centrodG(clusterCountI,j),tmpCountG(j))+sG;
                sB=min(centrodB(clusterCountI,j),tmpCountB(j))+sB;
            end
            dR=sR/sum(tmpCountR);
            dG=sG/sum(tmpCountG);
            dB=sB/sum(tmpCountB);
            %YUV,persons are sensitive to Y
            d=0.30*dR+0.59*dG+0.11*dB;  %运用颜色直方图法的差别函数  定义了d  差别函数
            if d>maxSimilar
                clusterGroupId=clusterCountI;
                maxSimilar=d;
            end
        end

        if maxSimilar>threshold
            %相似度大，与该聚类质心距离小
            %加入该聚类，并调整质心
            for ii=1:256    
                centrodR(clusterGroupId,ii)=centrodR(clusterGroupId,ii)*clusterCount(clusterGroupId)/(clusterCount(clusterGroupId)+1)+tmpCountR(ii)*1.0/(clusterCount(clusterGroupId)+1);
                centrodG(clusterGroupId,ii)=centrodG(clusterGroupId,ii)*clusterCount(clusterGroupId)/(clusterCount(clusterGroupId)+1)+tmpCountG(ii)*1.0/(clusterCount(clusterGroupId)+1);
                centrodB(clusterGroupId,ii)=centrodB(clusterGroupId,ii)*clusterCount(clusterGroupId)/(clusterCount(clusterGroupId)+1)+tmpCountB(ii)*1.0/(clusterCount(clusterGroupId)+1);
            end
            clusterCount(clusterGroupId)=clusterCount(clusterGroupId)+1;
            cluster(k)=clusterGroupId;   %第k帧在第clusterGroupId个聚类里面   cluster(3)等于1或者2，，也就是属于第一个聚类或者第二个聚类
        else
            %形成新的聚类，增加一个聚类质心
            count=count+1;
             visit = visit+1;
            clusterCount(count)=clusterCount(count)+1;
            centrodR(count,:)=tmpCountR;
            centrodG(count,:)=tmpCountG;
            centrodB(count,:)=tmpCountB;
            cluster(k)=count;   %第k帧在第count个聚类里面   否则 cluster(k)就在新建的聚类中
        end
    end

    %至此，所有帧都划进相应的聚类，一共有count个聚类，第k帧在第cluster(k)聚类中
    %现欲取出每个聚类中离质心距离最近，即相似度最大的作为该聚类的关键帧
    maxSimilarity=zeros(1,count);
    frame=zeros(1,count);
    for i=1:num
        sR=0;
        sG=0;
        sB=0;
        %运用颜色直方图法的差别函数
        for j=1:256
            sR=min(centrodR(cluster(i),j),tmpCountR(j))+sR;%每一帧和聚类质心进行比较，取最小值 
            sG=min(centrodG(cluster(i),j),tmpCountG(j))+sG;
            sB=min(centrodB(cluster(i),j),tmpCountB(j))+sB;
        end
        dR=sR/sum(tmpCountR);
        dG=sG/sum(tmpCountG);
        dB=sB/sum(tmpCountB);
        %YUV,persons are sensitive to Y
        d=0.30*dR+0.59*dG+0.11*dB;
        if d>maxSimilarity(cluster(i))
            maxSimilarity(cluster(i))=d;
            frame(cluster(i))=i;
        end
    end

    for j=1:count
        key(frame(j))=1;
        figure(j);
        imshow(strcat('images/',filenames(frame(j)).name));
    end
end

keyFrameIndexes=find(key) 
```

这种方法在878帧图片中提取出198帧，冗余度还是比较高。

(11) 使用CNN来保存图片在使用聚类法提取关键帧，这种方法由于TensorFlow环境搭配的有问题，没有实际运行，在这给出[链接](https://github.com/Raunak13/Boardsnapped)

和[这个](https://github.com/khare19yash/Key-Frame-Extraction)

## 三、整理结果

博主使用一分十五秒00000.MP4视频， 共1898帧，分为七类，帧差最大值帧间差法提取的效果很好。k-means聚类法效果不好，原因在于（1）代码是copy 别人的，没有进行优化，（2）博主对帧间聚类方法理论研究浅薄，指导不了实践。

k-means聚类 提出484帧

帧差最大值帧间差法提取出35帧

## 参考资料：

[1]苏筱涵.深度学习视角下视频关键帧提取与视频检索研究[J].网络安全技术与应用,2020(05):65-66.

[2]王红霞,王磊,晏杉杉.视频检索中的关键帧提取方法研究[J].沈阳理工大学学报,2019,38(03):78-82.

[3]王俊玲,卢新明.基于语义相关的视频关键帧提取算法[J/OL].计算机工程与应用:1-10[2020-11-04].http://kns.cnki.net/kcms/detail/11.2127.TP.20200319.1706.018.html.

[4] 张晓宇,张云华.基于融合特征的视频关键帧提取方法.计算机系统应用,2019,28(11):176–181\. http://www.c-s-a.org.cn/1003-3254/7163.html

[5] [1]周舟,韩芳,王直杰.面向手语识别的视频关键帧提取和优化算法[J/OL].华东理工大学学报(自然科学版):1-8[2020-11-05].https://doi.org/10.14135/j.cnki.1006-3080.20191201002.

附录：

1、[数字视音频处理知识点小结](https://blog.csdn.net/zju_fish1996/article/details/54124505?biz_id=102&utm_term=%E5%B8%A7%E9%97%B4%E5%B7%AE%E6%B3%95%E5%92%8C%E8%81%9A%E7%B1%BB%E6%B3%95%E5%9C%A8%E6%8F%90%E5%8F%96%E8%A7%86%E9%A2%91%E5%85%B3%E9%94%AE%E5%B8%A7%E4%B8%AD%E7%9A%84%E5%8C%BA%E5%88%AB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-54124505&spm=1018.2118.3001.4449)

[2020-11-04].http://kns.cnki.net/kcms/detail/11.2127.TP.20200319.1706.018.html.

[4] 张晓宇,张云华.基于融合特征的视频关键帧提取方法.计算机系统应用,2019,28(11):176–181\. http://www.c-s-a.org.cn/1003-3254/7163.html

[5] [1]周舟,韩芳,王直杰.面向手语识别的视频关键帧提取和优化算法[J/OL].华东理工大学学报(自然科学版):1-8[2020-11-05].https://doi.org/10.14135/j.cnki.1006-3080.20191201002.
[6] https://me.csdn.net/cungudafa这位小姐姐的博客

附录：

1、[数字视音频处理知识点小结](https://blog.csdn.net/zju_fish1996/article/details/54124505?biz_id=102&utm_term=%E5%B8%A7%E9%97%B4%E5%B7%AE%E6%B3%95%E5%92%8C%E8%81%9A%E7%B1%BB%E6%B3%95%E5%9C%A8%E6%8F%90%E5%8F%96%E8%A7%86%E9%A2%91%E5%85%B3%E9%94%AE%E5%B8%A7%E4%B8%AD%E7%9A%84%E5%8C%BA%E5%88%AB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-54124505&spm=1018.2118.3001.4449)