<!--yml
category: 视频
date: 2022-05-04 19:46:21
-->

# python使用pyav库提取视频关键帧_无名的小菜鸡的博客-CSDN博客_python视频提取关键帧

> 来源：[https://blog.csdn.net/lidc1004/article/details/117528327](https://blog.csdn.net/lidc1004/article/details/117528327)

### python使用pyav库提取视频关键帧

#### pyav库简介

PyAV是[FFmpeg][FFmpeg]库的python绑定。其目标是提供底层库的所有功能和控制，但是尽可能多地管理细节。PyAV用于通过容器、流、包、编解码器和帧直接而精确地访问您的媒体。它公开了该数据的一些转换，并帮助您从其他包(例如Numpy和Pillow)获取数据。具体安装方法参考GitHub

GitHub地址：[https://github.com/PyAV-Org/PyAV](https://github.com/PyAV-Org/PyAV)

官方网站：[https://pyav.org/docs/stable/](https://pyav.org/docs/stable/)

##### 使用pyav库提取关键帧

代码：

```
import av
import av.datasets
import os
import cv2
import argparse
import shutil
import math

videos_src_path = './video/'
videos_save_path = './img+/'
if not os.path.exists(videos_save_path):
    os.makedirs(videos_save_path)
file_count = 0
videos = os.listdir(videos_src_path)
for each_video in videos:
    file_count += 1
    each_video_name = each_video.split('.')[0]
    each_video_full_path = os.path.join(videos_src_path, each_video)
    img_path = os.path.join(videos_save_path, each_video_name)
    if os.path.exists(img_path):
        shutil.rmtree(img_path)
    os.mkdir(img_path)
    container = av.open(each_video_full_path)

    stream = container.streams.video[0]
    stream.codec_context.skip_frame = 'NONKEY'

    for frame in container.decode(stream):

        frame.to_image().save(img_path + '/' + 'night-sky.{:04d}.jpg'.format(frame.pts), quality=80)

print('\nvideo numbers:', str(file_count), '\nAll is done ,thanks') 
```

其中官方示例：[https://pyav.org/docs/stable/cookbook/basics.html#saving-keyframes](https://pyav.org/docs/stable/cookbook/basics.html#saving-keyframes)