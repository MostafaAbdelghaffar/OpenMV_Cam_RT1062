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

#### Introduction:

The project is named **Janus**.
The project was originally designed for a medical application, implying that the cartridge is a medical cartridge containing microchannels through which various liquids, including different enzymes, flow.

The project is  inspired by the utilization of image processing and computer vision technologies to detect the presence of liquid inside a micro-sized channel in a cartridge.

The algorithm relies entirely on the [grayscale intensity level](https://i.stack.imgur.com/fdbAz.png) of each pixel in the captured frame from the camera. Thus, it depends on the light and its intensity.

#### Hardware:
- [OpenMV Cam RT1062](https://openmv.io/products/openmv-cam-rt)
- [Manual Linear Positioning Stage](https://www.amazon.co.uk/Positioning-Trimming-Micrometer-Displacement-Precision/dp/B0C4PPD2VW) for the [Cartridge](https://i.imgur.com/axXRbiQ.jpeg)

- A 3D Printed Stand/Holder for Camera
- [Light Panel](https://www.amazon.co.uk/COHEALI-Light-tracing-Stencil-Writing/dp/B0CBJPBF5P)
- [Water-Cooled Breadboards](https://www.thorlabs.com/newgrouppage9.cfm?objectgroup_id=4918)
- [Box](https://www.plastor.co.uk/euro-stacking-containers-with-and-without-lids-folding-containers/prime-economy-range-euro-container-cases/ref-bk-case43-32-prime-economy-euro-container-cases-400-x-300-x-335mm-with-hand-holes/) for controlling light

#### Software:

Apart from utilizing [OpenCV](https://opencv.org/) to gain a sufficient understanding of how to achieve the desired outcome, trainings were conducted using [OpenCV](https://opencv.org/) for image processing. Additionally, the following tools were employed.

- [OpenMV IDE](https://openmv.io/pages/download) using [MicroPython](https://micropython.org/)


#### Setup:
The following images are self-explanatory regarding how the hardware was set up.

Far view | Close view
------------ | -------------
<img src="Setup-1.jpeg" alt="drawing" width="200"/>| <img src="Setups2.jpeg" alt="drawing" width="200"/>

The project is focused on the left long region of the cartridge, and the following shows how it appears in OpenMV after adjusting the distance between the camera and the cartridge.

<img src="1.bmp" alt="drawing" width="200"/>

#### Algorithm & Code:
I will start with the Algorithm.
First let's talk about the constraits and the givens.

In the current stage, for the code to work, the camera has to capture the Channel has to be in a straight status  and the lens should be completely corrected vertically.




```python
import sensor
import time,machine
```
