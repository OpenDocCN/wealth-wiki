<!--yml
category: 游戏
date: 2022-05-04 19:36:54
-->

# python实现连连看辅助（图像识别）_python_脚本之家

> 来源：[https://www.jb51.net/article/165588.htm](https://www.jb51.net/article/165588.htm)

```
import win32gui
import time
from PIL import ImageGrab , Image
import numpy as np
from pymouse import PyMouse

class GameAuxiliaries(object):
 def __init__(self):
 self.wdname = r'宠物连连看经典版2,宠物连连看经典版2小游戏,4399小游戏 www.4399.com - Google Chrome'
 # self.wdname = r'main.swf - PotPlayer'
 self.image_list = {}
 self.m = PyMouse()
 def find_game_wd(self,wdname):
 # 取得窗口句柄
 hdwd = win32gui.FindWindow(0,wdname)
 # 设置为最前显示
 win32gui.SetForegroundWindow(hdwd)
 time.sleep(1)

 def get_img(self):
 image = ImageGrab.grab((417, 289, 884, 600))
 # image = ImageGrab.grab((417, 257, 885, 569))
 image.save('1.jpg','JPEG')
 for x in range(1,9):
 self.image_list[x] = {}
 for y in range(1,13):
 top = (x - 1) * 38 + (x-2)
 left =(y - 1) * 38 +(y-2)
 right = y * 38 + (y-1)
 bottom = x * 38 +(x -1)
 if top < 0:
 top = 0
 if left < 0 :
 left = 0
 im_temp = image.crop((left,top,right,bottom))
 im = im_temp.crop((1,1,37,37))
 im.save('{}-{}.jpg'.format(x,y))
 self.image_list[x][y]=im

 # 判断两个图片是否相同。汉明距离，平均哈希
 def compare_img(self,im1,im2):
 img1 = im1.resize((20, 20), Image.ANTIALIAS).convert('L')
 img2 = im2.resize((20, 20), Image.ANTIALIAS).convert('L')
 pi1 = list(img1.getdata())
 pi2 = list(img2.getdata())
 avg1 = sum(pi1) / len(pi1)
 avg2 = sum(pi2) / len(pi2)
 hash1 = "".join(map(lambda p: "1" if p > avg1 else "0", pi1))
 hash2 = "".join(map(lambda p: "1" if p > avg2 else "0", pi2))
 match = 0
 for i in range(len(hash1)):
 if hash1[i] != hash2[i]:
 match += 1
 # match = sum(map(operator.ne, hash1, hash2))
 # match 值越小，相似度越高
 return match

 # 将图片矩阵转换成数字矩阵

 def create_array(self):
 array = np.zeros((10,14),dtype=np.int32)
 img_type_list = []
 for row in range(1,len(self.image_list)+1):
 for col in range(1,len(self.image_list[1])+1):
 # im = Image.open('{}-{}.jpg'.format(row,col))
 im = self.image_list[row][col]
 for img in img_type_list:
 match = self.compare_img(im,img)
 # match = test2.image_similarity_vectors_via_numpy(im,img)
 if match <15:
 array[row][col] = img_type_list.index(img) +1

 break
 else:
 img_type_list.append(im)
 array[row][col] = len(img_type_list)

 return array

 def row_zero(self,x1,y1,x2,y2,array):
 '''相同的图片中间图标全为空'''
 if x1 == x2:
 min_y = min(y1,y2)
 max_y = max(y1,y2)
 if max_y - min_y == 1:
 return True
 for y in range(min_y+1,max_y):
 if array[x1][y] != 0 :
 return False
 return True
 else:
 return False

 def col_zero(self,x1,y1,x2,y2,array):
 '''相同的图片同列'''
 if y1 == y2:
 min_x = min(x1,x2)
 max_x = max(x1,x2)
 if max_x - min_x == 1:
 return True
 for x in range(min_x+1,max_x):
 if array[x][y1] != 0 :
 return False
 return True
 else:
 return False

 def two_line(self,x1,y1,x2,y2,array):
 '''两条线相连，转弯一次'''
 for row in range(1,9):
 for col in range(1,13):
 if row == x1 and col == y2 and array[row][col]==0 and self.row_zero(x1,y1,row,col,array) and self.col_zero(x2,y2,row,col,array):
 return True
 if row == x2 and col == y1 and array[row][col]==0 and self.row_zero(x2,y2,row,col,array) and self.col_zero(x1,y1,row,col,array):
 return True
 return False

 def three_line(self,x1,y1,x2,y2,array):
 '''三条线相连，转弯两次'''
 for row1 in range(10):
 for col1 in range(14):
 for row2 in range(10):
 for col2 in range(14):
 if array[row1][col1] == array[row2][col2] == 0 and self.row_zero(x1,y1,row1,col1,array) and self.row_zero(x2,y2,row2,col2,array) and self.col_zero(row1,col1,row2,col2,array):
 return True
 if array[row1][col1] == array[row2][col2] == 0 and self.col_zero(x1,y1,row1,col1,array) and self.col_zero(x2,y2,row2,col2,array) and self.row_zero(row1,col1,row2,col2,array):
 return True
 if array[row1][col1] == array[row2][col2] == 0 and self.row_zero(x2,y2,row1,col1,array) and self.row_zero(x1,y1,row2,col2,array) and self.col_zero(row1,col1,row2,col2,array):
 return True
 if array[row1][col1] == array[row2][col2] == 0 and self.col_zero(x2,y2,row1,col1,array) and self.col_zero(x1,y1,row2,col2,array) and self.row_zero(row1,col1,row2,col2,array):
 return True
 return False

 def mouse_click(self,x,y):

 top = (x - 1) * 38 + (x - 2)
 left = (y - 1) * 38 + (y - 2)
 right = y * 38 + (y - 1)
 bottom = x * 38 + (x - 1)
 if top < 0:
 top = 0
 if left < 0:
 left = 0

 self.m.press(int(417+(left+right)/2) ,int(289+(top+bottom)/2) )

 def find_same_img(self,array):

 for x1 in range(1,9):
 for y1 in range(1,13):
 if array[x1][y1] == 0:
 continue
 for x2 in range(1,9):
 for y2 in range(1,13):
 if x1==x2 and y1 == y2:
 continue
 if array[x2][y2] == 0 :
 continue
 if array[x1][y1] != array[x2][y2] :
 continue
 if array[x1][y1] ==array[x2][y2] and (self.row_zero(x1,y1,x2,y2,array) or self.col_zero(x1,y1,x2,y2,array) or self.two_line(x1,y1,x2,y2,array) or self.three_line(x1,y1,x2,y2,array)):
 print("可消除！x{}y{} 和 x{}y{}".format(x1,y1,x2,y2))
 self.mouse_click(x1,y1)
 time.sleep(0.1)
 self.mouse_click(x2,y2)
 time.sleep(0.1)
 array[x1][y1]=array[x2][y2]=0

 def run(self):
 #找到游戏运行窗口
 self.find_game_wd(self.wdname)
 # 截图，切割成小图标
 self.get_img()
 # 将图片矩阵转换成数字矩阵
 array = self.create_array()
 print(array)
 # 遍历矩阵，找到可消除项，点击消除
 for i in range(10):
 self.find_same_img(array)
 print(array)

if __name__ == '__main__':
 ga = GameAuxiliaries()
 ga.run()
```

该程序其实未能完全实现辅助功能，主要是因为图片切割时未找到更好的规则，造成图片识别困难，缩放比例和判断阀值未找到一个平衡点，阀值太大，则将不同的图标识别为相同，阀值太小，相同的图标又判断为不一样。