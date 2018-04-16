---
layout: post
title:  "How to install OpenCV"
date:   2018-4-15 10:00:00 -0400
tag: cv
---

## Install required packages for OpenCV

- [Ref link](https://docs.opencv.org/2.4.13.6/doc/tutorials/introduction/linux_install/linux_install.html#required-packages)

```
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

## Download OpenCV

- Download link: [https://docs.opencv.org/](https://docs.opencv.org/)
- In this link, there are all versions of opencv. I use [opencv 2.4](https://docs.opencv.org/2.4.13.6.zip) usually.

## Building OpenCV from Source Using CMake

```
cd ~/opencv
mkdir release
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=~/opencv/release ..
make -j4
make install
```
> **Note:** CMAKE_INSTALL_PREFIX: given the path you want opencv to be installed

##  Setting OpenCV environment in the .bashrc file

```
echo export OpenCV_DIR=~/opencv/release/share/OpenCV >> ~/.bashrc
source ~/.bashrc
```

## Create a program using OpenCV

- Let’s use a simple program such as DisplayImage.cpp shown below.

```
#include <stdio.h>
#include <opencv2/opencv.hpp>

using namespace cv;

int main(int argc, char** argv )
{
    if ( argc != 2 )
    {
        printf("usage: DisplayImage.out <Image_Path>\n");
        return -1;
    }

    Mat image;
    image = imread( argv[1], 1 );

    if ( !image.data )
    {
        printf("No image data \n");
        return -1;
    }
    namedWindow("Display Image", WINDOW_AUTOSIZE );
    imshow("Display Image", image);

    waitKey(0);

    return 0;
}
```

- Create a CMake file

```
cmake_minimum_required(VERSION 2.8)
project( DisplayImage )

find_package( OpenCV REQUIRED )   # use this command to find opencv

add_executable( DisplayImage DisplayImage.cpp )
target_link_libraries( DisplayImage ${OpenCV_LIBS} )  # link opencv libraries
```

- Generate the executable

```
cd <DisplayImage_directory>
mkdir build
cd build
cmake ..
make -j4
```
- By now you should have an executable (called DisplayImage in this case). You just have to run it giving an image location as an argument, i.e.:

```
./DisplayImage lena.jpg
```

## [OpenCV Docs](https://docs.opencv.org/2.4.13.6/)

- If you still have some problems, you can visit the opencv docs website which has many helpful tutorials.
