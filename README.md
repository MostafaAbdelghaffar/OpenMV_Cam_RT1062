---
Author: "Mostafa"
Project: "Janus"
Date: "09/05/2024"
---

##### Table of Contents:
1. [Introduction](#introduction)
1. [Hardware](#hardware)
1. [Software](#software)
1. [Setup](#setup)
1. [Algorithm and Code](#algorithm)


## Introduction:

The project, named Janus, is designed for medical applications, indicating that the cartridge contains microchannels through which various liquids, including different enzymes, flow.

The project is inspired by the utilization of image processing and computer vision technologies to detect the presence of liquid inside a micro-sized channel in a cartridge.

The algorithm relies entirely on the [grayscale intensity level](https://i.stack.imgur.com/fdbAz.png) of each pixel in the captured frame from the camera, thus depending on the light and its intensity.

## Hardware:
- [OpenMV Cam RT1062](https://openmv.io/products/openmv-cam-rt)
- [Manual Linear Positioning Stage](https://www.amazon.co.uk/Positioning-Trimming-Micrometer-Displacement-Precision/dp/B0C4PPD2VW) for the [Cartridge](https://i.imgur.com/axXRbiQ.jpeg)

- A 3D Printed Stand/Holder for Camera
- [Light Panel](https://www.amazon.co.uk/COHEALI-Light-tracing-Stencil-Writing/dp/B0CBJPBF5P)
- [Water-Cooled Breadboards](https://www.thorlabs.com/newgrouppage9.cfm?objectgroup_id=4918)
- [Box](https://www.plastor.co.uk/euro-stacking-containers-with-and-without-lids-folding-containers/prime-economy-range-euro-container-cases/ref-bk-case43-32-prime-economy-euro-container-cases-400-x-300-x-335mm-with-hand-holes/) for controlling light

## Software:

In addition to utilizing [OpenCV](https://opencv.org/) to gain a sufficient understanding of how to achieve the desired outcome, trainings were conducted using [OpenCV](https://opencv.org/) for image processing. Additionally, the project employed the following tools:


- [OpenMV IDE](https://openmv.io/pages/download) using [MicroPython](https://micropython.org/)


## Setup:
The following images are self-explanatory regarding how the hardware was set up.

Far view | Close view
------------ | -------------
<img src="https://i.imgur.com/ofMd0wS.jpeg" alt="drawing" width="200"/>| <img src="https://i.imgur.com/3lgbOvY.jpeg" alt="drawing" width="200"/>



The project is focused on the left long region of the cartridge. The camera needs to capture the channel in a straight and vertical orientation, and the lens should be aligned so the channel appears straight and vertical almost completely.

<img src="https://i.imgur.com/vlCgP9I.png" alt="drawing" width="300"/><br>


Givens:
- Lighting.
- Orientation of the channel
- Shape of the channel

Constraints: 
- N/A for now

In the current stage, for the code to work, the camera has to capture the Channel has to be in a straight and vertical orientation and the lens should be correlated so the channel should be straight and vertical almost completely.

## Algorithm:

Initially, the liquid detection via the grayscale intensity is based on the greyscale intensity level, ranging from 0 to 255, where 0 is black and 255 is white.

After observing, when the channel is empty, it tends towards white including the edges of the channel. Conversely, when it is full, it is significantly darker.

The algorithm leverages this to detect whether the channel contains liquids.

The setup and initialization of the camera (sensor) are as follows:

```python
sensor.reset()
sensor.set_pixformat(sensor.GRAYSCALE)  # Greyscale
sensor.set_vflip(True) #flipping vertically (the camera is positioned upside down)
sensor.set_framesize(sensor.QVGA)  # Resolution 640x480
sensor.skip_frames(time=2000) #Skipping frames to let the setup take place
```

For each frame, there are two copies created: one in an "RGB" format and one in the original state "Greyscale". The RGB is made for drawing colored boxes or lines, while the GREY is to gather data from the frame in grayscale.

```py
gray= sensor.snapshot()
img = sensor.snapshot().to_rgb565()
```

The first objective was to detect lines in the cartridge to detect the location of the channel. The function used i [Hough Line Transform](https://docs.openmv.io/library/omv.image.html).

Subsequently, from the data, calculate the maximum horizontal and vertical as well as minimum of pixels of the detected lines (assuming that the lines detected the channel), which are the corner positions of the channel.

```py
for l in gray.find_lines(threshold=3500, theta_margin=1, rho_margin=1):
            min_x = min(min_x, l.x1(), l.x2())  #left
            min_y = min(min_y, l.y1(), l.y2())  #top
            max_x = max(max_x, l.x1(), l.x2())  #right
            max_y = max(max_y, l.y1(), l.y2())  #bottom
```

Now, as the channel is detected, it is split into segments along the frame's height. For each segment of the channel, the mean value of its grayscale intensity will be extracted and stored in an array.

The same will be applied to a "reference segment" which is out of the channel, the cartridge background. This will act as our threshold to detect whether the channel is full or empty. The cartridge background is grey and falls around 125 units depending on the light intensity of the environment.

Now, we have an array of the mean values of each segment in the channel as well as the mean value of the reference rectangle that we created.

By dividing the array by the reference rectangle, we normalize the segment values based on the reference rectangle, then store them in an array.

The following is the observation on the values when it is empty as well as full:

![Analysis](https://i.imgur.com/ryAm08U.png)

As shown in the previous figure, it could be observed that there is a huge gap when the channel is empty compared to when it is full.

Based on this observation, a manual threshold value of 1.3 was set.

```py
Manual_Threshold = 1.3
```

The last comparison is made to detect the liquid based on the previous. To see whether the normalized segment value is above 1.3 or below.

[Liquid Presence Detector GIF](https://giphy.com/embed/3T8foUA9xtgXF2HIdz)


The previous GIF shows the end result of my application.