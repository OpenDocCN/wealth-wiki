<!--yml
category: 游戏
date: 2023-09-17 14:52:41
-->

# 用opencv玩别踩白块 - CodeAntenna

> 来源：[https://codeantenna.com/a/cUOdXdNZw7](https://codeantenna.com/a/cUOdXdNZw7)

能玩，但是分数刷不高，主要受限于屏幕采集的速度
python版本：3.8

## 采集屏幕图像

这里选用的是mss，一个快速采集屏幕图像的库函数
[https://github.com/BoboTiG/python-mss](https://github.com/BoboTiG/python-mss)
可用`python -m pip install -U --user mss`进行安装

```
from mss import mss

def capture():
    with mss() as sct:
        img = np.array(sct.grab(bbox))
        return img 
```

这里的bbox是待采集的屏幕区域，默认是对整个屏幕进行采集

## 鼠标点击

这里选用的是`pyautogui`，可以很方便得实现鼠标点击以及键盘等操作
[https://github.com/asweigart/pyautogui](https://github.com/asweigart/pyautogui)

```
import pyautogui

pyautogui.click(x + 3, y + 60) 
```

## 图像处理

下面主要尝试了两种方法：

1.  对像素点进行判断
2.  对整个图像进行二值化等处理，寻找方块的具体位置
    **（完整代码见文末）**

### 像素点

采集一行图像，然后对一行图像的四个像素点进行判断，如果小于一个阈值，则点击该方块。

```
def processImage() :
    with mss() as sct:
        t1 = time.time()
        i = 0
        while True:
            img = sct.grab(bbox)

            for cordx in cords_x:
                if img.pixel(cordx, 0)[0] < 10:
                    pyautogui.click(start_x + cordx, start_y)
                    i = i + 1
                    break
                time.sleep(0.01) 
```

### 对整张图像进行处理

1.  先将图像转换为灰度图
2.  对图像进行腐蚀（方块消失时会对识别造成干扰）
3.  用opencv的`findContours`寻找轮廓，然后对轮廓进行判断
4.  选择最低一个方块进行点击

```
def processImage(img, cont, anterior):

    img = cv2.cvtColor(img, cv2.COLOR_RGBA2RGB)  
    img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    ret, img = cv2.threshold(img, 70, 255, cv2.THRESH_BINARY_INV)

    cv2.erode(img, None, iterations=100)

    contours, hierarchy = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE)
    click_list = []
    for c in contours:
        left_top = tuple(c[c[:, :, 1].argmin()][0])
        left_bottom = tuple(c[c[:, :, 1].argmax()][0])
        if left_bottom[1] - left_top[1] < 30:
            continue
        pointX = int(left_bottom[0]+7)
        pointY = int(left_bottom[1]-7)
        if pointX < 67 and img[pointY, pointX] == 0:
            continue

        click_list.append((pointX, pointY))

    if len(click_list) == 0:
        return

    click_list.sort(key=lambda x: x[1], reverse=True)
    x = click_list[0][0]
    y = click_list[0][1]
    if x == anterior:
        return anterior
    pyautogui.click(x + 3, y + 60)

    click_list.clear()

    return x 
```

## 完整代码

```
import numpy as np
from mss import mss
import cv2
import time
import pyautogui
import signal
import sys

start_x = 10
start_y = 150
bbox = (start_x, start_y, start_x + 600, start_y+1)
cords_x = [20, 85, 150, 215]
def test_time() :
    with mss () as sct :
        t1 = time.time()
        for i in range(100):
            img = sct.grab(bbox)
        t2 = time.time()
        print(t2 - t1)
def processImage() :
    with mss() as sct:
        t1 = time.time()
        i = 0
        while True:
            img = sct.grab(bbox)

            for cordx in cords_x:
                if img.pixel(cordx, 0)[0] < 10:
                    pyautogui.click(start_x + cordx, start_y)
                    i = i + 1
                    break
                time.sleep(0.01)
def main():
    processImage()

if __name__ == '__main__':
    main() 
```

```
import numpy as np
from mss import mss
from PIL import Image
import cv2
import time
import signal
import sys
import pyautogui
import copy
from pynput.mouse import Button, Controller

mouse = Controller()

bbox=(3, 60, 68, 180)
def capture():
    with mss() as sct:
        img = np.array(sct.grab(bbox))
        return img

def showArray(array):
    img = cv2.cvtColor(array, cv2.COLOR_BGR2RGB)

def panic_button(sig, frame):
    print('falous')
    sys.exit(0)

def processImage(img, cont, anterior):

    img = cv2.cvtColor(img, cv2.COLOR_RGBA2RGB)  
    img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    ret, img = cv2.threshold(img, 70, 255, cv2.THRESH_BINARY_INV)

    cv2.erode(img, None, iterations=100)

    contours, hierarchy = cv2.findContours(img, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_NONE)
    click_list = []
    for c in contours:
        left_top = tuple(c[c[:, :, 1].argmin()][0])
        left_bottom = tuple(c[c[:, :, 1].argmax()][0])
        if left_bottom[1] - left_top[1] < 30:
            continue
        pointX = int(left_bottom[0]+7)
        pointY = int(left_bottom[1]-7)
        if pointX < 67 and img[pointY, pointX] == 0:
            continue

        click_list.append((pointX, pointY))

    if len(click_list) == 0:
        return

    click_list.sort(key=lambda x: x[1], reverse=True)
    x = click_list[0][0]
    y = click_list[0][1]
    if x == anterior:
        return anterior
    pyautogui.click(x + 3, y + 60)

    click_list.clear()

    return x

def main():
    cont = 0

    anterior = -1
    while 1:
        t1 = time.time()
        img = capture()
        anterior = processImage(img, cont, anterior)

        print(time.time() - t1)
        cont += 1

if __name__ == '__main__':
    signal.signal(signal.SIGINT, panic_button)
    main() 
```