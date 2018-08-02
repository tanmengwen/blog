---
layout: post
title:  "matplotlib绘图基本使用用例"
date:   2018-8-2 10:00:00 -0400
tag: python
---

## matplotlib画图

```
import numpy as np
import matplotlib.pyplot as plt

# Compute the x and y coordinates for points on a sine curve
x = np.arange(0, 3 * np.pi, 0.1)
y = np.sin(x)

# Plot the points using matplotlib
plt.plot(x, y, '-*')
plt.show()  # You must call plt.show() to make graphics appear.
```

```
import numpy as np
import matplotlib.pyplot as plt

# Compute the x and y coordinates for points on sine and cosine curves
x = np.arange(0, 3 * np.pi, 0.1)
y_sin = np.sin(x)
y_cos = np.cos(x)

# Plot the points using matplotlib
plt.plot(x, y_sin)
plt.plot(x, y_cos)
plt.xlabel('x axis label')
plt.ylabel('y axis label')
plt.title('Sine and Cosine')
plt.legend(['Sine', 'Cosine'])
plt.show()
```
```
import matplotlib as mpl
import numpy as np
from mpl_toolkits.mplot3d import Axes3D
import matplotlib.pyplot as plt

data = np.loadtxt("~/data.txt")

x = data[0:, 1]
y = data[0:, 2]
z = data[0:, 3]

fig = plt.figure()
ax = fig.gca(projection='3d')

ax.plot(x, y, z, label='parametric curve')
ax.legend()

plt.show()
```

## matplotlib读取显示保存图片
```
import matplotlib.pyplot as plt  # plt 用于显示图片
import matplotlib.image as mpimg # mpimg 用于读取图片
import numpy as np
 
lena = mpimg.imread('～/lena.jpg')
# 此时 lena 就已经是一个 np.array 了，可以对它进行任意处理
lena.shape #(512, 512, 3)
 
plt.subplot(2, 2, 1)
plt.imshow(lena) # 显示图片
plt.axis('off') # 不显示坐标轴

# 显示图片的第一个通道
lena_1 = lena[:,:,0]
plt.subplot(2, 2, 2)
plt.imshow(lena_1)

# 此时会发现显示的是热量图，不是我们预想的灰度图，可以添加 cmap 参数，有如下几种添加方法：
plt.subplot(2, 2, 3)
plt.imshow(lena_1, cmap='Greys_r')

plt.subplot(2, 2, 4)
img = plt.imshow(lena_1)
img.set_cmap('gray') # 'hot' 是热量图

plt.savefig('lena_new_sz.png')  #存储fig

plt.show()
```

## numpy加载保存txt

```
import numpy as np
import matplotlib.pyplot as plt

data = np.loadtxt("～/data.log")

plt.subplot(2, 2, 1)
plt.plot(data[:, 0], 'y-*', label="x1")
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 2)
plt.plot(data[:, 1], 'r-*', label="x2")
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 3)
plt.plot(data[:, 3], 'g-*', label="x3")
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 4)
plt.plot(data[:, 4], 'b-*', label="x4")
plt.legend()
plt.grid(True)

plt.xlabel('t')
plt.ylabel('value')
plt.title('data')
plt.show()

np.savetxt("data.txt", data, fmt='%f')
```

## 将图片保存为npy，读取显示

```
import matplotlib.pyplot as plt  # plt 用于显示图片
import matplotlib.image as mpimg # mpimg 用于读取图片
import numpy as np

lena = mpimg.imread('～/lena.jpg') 

np.save('lena', lena) # 会在保存的名字后面自动加上.npy

img = np.load('lena.npy') # 读取前面保存的数组

plt.imshow(img)
plt.show()
```

## scipy读取显示保存图片

```
import numpy as np
from scipy.misc import imread, imresize, imsave
import matplotlib.pyplot as plt

img = imread('～/lena.jpg')
img_tinted = img * [1, 0.95, 0.9]

# Show the original image
plt.subplot(1, 3, 1)
plt.imshow(img)

# Show the tinted image
plt.subplot(1, 3, 2)

# A slight gotcha with imshow is that it might give strange results
# if presented with data that is not uint8. To work around this, we
# explicitly cast the image to uint8 before displaying it.
plt.imshow(np.uint8(img_tinted))

plt.subplot(1, 3, 3)
img_resized = imresize(img, 0.5)
plt.imshow(img_resized)

imsave('lena_resized.png', img_resized)
imsave('lena_img.png', img)

plt.show()
```
