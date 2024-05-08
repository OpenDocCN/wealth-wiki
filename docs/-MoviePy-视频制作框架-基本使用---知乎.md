<!--yml
category: 视频
date: 2022-04-26 11:46:27
-->

# 「MoviePy 视频制作框架」基本使用 - 知乎

> 来源：[https://zhuanlan.zhihu.com/p/315364225](https://zhuanlan.zhihu.com/p/315364225)

## 内容介绍

本章带你学习基于 Python3 的 MoivePy 视频框架 的基本使用操作方法。

代码内容基于「MoivePy 视频框架」源码版本 1.0.3 ，更新内容会进行标记说明对应版本。

本文基于moviepy文档总结内容。觉得文档内容不够吃的话可以翻阅下面两份文档自行学习。

[moviepy中文文档](https://link.zhihu.com/?target=http%3A//doc.moviepy.com.cn/)

[moviepy英文文档](https://link.zhihu.com/?target=https%3A//zulko.github.io/moviepy/index.html)

## 文字处理部分

```
from moviepy.editor import *
```

```
Text = "你好，这里是文本字幕"
Fontsize = 70
TextColor = 'white'
txt_clip = TextClip( Text,fontsize = Fontsize,color = TextColor )

# txt : 显示的文本字符串。 
# filename : 显示的文件的文件名。 
# size ：文字背景的大小，结合method使用。 
# bg_color ：文字的背景颜色。 
# color ： 文字的颜色。 
# font ：字体选择，需要自定义。 
# stroke_color ：文字轮廓的颜色。 
# stroke_width ：文字线条的宽度。 
# method ：文字的样式,'label'和'caption'，其中'caption'配合size使用。 
# kerning ：字间距。 
# align ：'label'和'caption'设置有效，文字方向。'center','East','West', 'South','North' 
# transparent : 文字透明度
```

## 视频处理部分

```
from moviepy.editor import VideoFileClip, concatenate_videoclips
clip1 = VideoFileClip("data/base1280_720.mp4").resize((1280, 720)) # 重新设置视频大小
clip2 = VideoFileClip("data/base1280_720.mp4").subclip(50,60) # 剪切指定时间段 秒
clip3 = VideoFileClip("data/base1280_720.mp4").set_duration(5) # 剪切持续时间 秒
final_clip = concatenate_videoclips([clip1,clip2,clip3])
final_clip.write_videofile("result.mp4")

# filename: 文件名 支持格式有ffmpeg: .ogv, .mp4, .mpeg, .avi, .mov等。
# has_mask: 掩码mask，基本无用。
# audio: 剪切时是否保留音频，'True'和'False'。
# target_resolution: 视频分辨率，基本无用。
# resize_algorithm: 视频调整大小算法，基本无用。
# fps_source: 收集fps值，基本无用。
```

```
from moviepy.editor import VideoFileClip, clips_array, vfx
clip1 = VideoFileClip("data/base1280_720.mp4").set_duration(3).margin(10) # margin视频间的间距
clip2 = clip1.fx( vfx.mirror_x)
clip3 = clip1.fx( vfx.mirror_y)
clip4 = clip1.resize(0.60) # 设置60%大小
final_clip = clips_array([[clip1, clip2],
                          [clip3, clip4],
                          [clip1, clip2]])  # 通过列表的方式组合
final_clip.resize(width=480).write_videofile("my_stack.mp4")
```

*   **3.CompositeVideoClip 方法视频合成**

```
video = CompositeVideoClip([clip1,clip1,clip1],size=(1280, 720))
video.write_videofile("my_stack.mp4")
```

```
# 这个暂时没搞明白
# 以通过transition=my_clip选项来在剪辑之间加一个过场
```

```
video = CompositeVideoClip([clip1, # 0秒开始
                            clip2.set_start(2), # 2秒开始
                            clip3.set_start(3)]) # 3秒开始
video.write_videofile("my_stack.mp4")
```

```
video = CompositeVideoClip([clip1,
                            clip2.set_pos((45,150)),
                            clip3.set_pos((90,100))])
video.write_videofile("my_stack.mp4")
# 定位的方式方法 需要定义成变量否则无效 例如：clip2 = clip2.set_pos((0.4,0.7), relative=True)
clip2.set_pos((45,150)) # 45，150像素点位置
clip2.set_pos(("center","top"))  # 中上
clip2.set_pos(("center","center")) # 中中
clip2.set_pos(("center","bottom")) # 中下
clip2.set_pos(("left","top")) # 左上
clip2.set_pos(("left","center")) # 左中
clip2.set_pos(("left","bottom")) # 左下
clip2.set_pos(("right","top")) # 右上
clip2.set_pos(("right","center")) # 右中
clip2.set_pos(("right","bottom")) # 右下
clip2.set_pos((0.4,0.7), relative=True) # 使用缩放比例，宽40% 高70%
clip2.set_pos(lambda t: ('left', 250+t) ) # 水平居中向下移动

clip2 = clip2.set_pos((0.4,0.7), relative=True)
video = CompositeVideoClip([clip1,
                            clip2])
video.write_videofile("my_stack.mp4")
```

```
clip1.save_frame("frame.jpeg") # 保存视频第一帧的画面
clip1.save_frame("frame.png", t=2) # 保存视频第2秒的画面
```

```
# 结束在窗口按ESC否则卡死
my_clip.show() # 显示第一帧
my_clip.show(10.5) # 显示10.5秒这一帧
my_clip.show(10.5, interactive = True)  # 可以记录点击视频位置的坐标
```

```
clip1 = VideoFileClip("data/base1280_720.mp4").set_duration(3).margin(10) # margin视频间的间距
clip1.ipython_display() # 显示视频
ipython_display(clip1, autoplay=1, loop=1) # 自动循环
```

## 音频处理部分

```
audioclip = AudioFileClip("data/xxxx.mp3") # 读取MP3

videoclip = AudioFileClip("data/base1280_720.mp4") # 读取视频文件提取音频
audioclip = videoclip.audio
```

```
VolumeMultiple = 0.8
clip = clip.volumex(VolumeMultiple)
```

```
aclip1 = AudioFileClip("data/xxxx.mp3")
aclip2 = AudioFileClip("data/xxxx.mp3")

# 制作混合音轨
concat = concatenate_audioclips([aclip1, aclip2])
compo = CompositeAudioClip([aclip1.volumex(1.2), # 音量设置大小
                            aclip2.set_start(5)])# 音频起始时间
```

```
videoclip2 = videoclip.set_audio(my_audioclip)
```