# Duke Conservation Technology Drone

This repository contains DCT's software and [documentation](https://github.com/Duke-Conservation-Technology/dct_drone/wiki) for the animal tracking drone.

> **Warning:** This repository is under active development and thus everything is not quite cleaned up and ready for public release. We currently recommend only DCT members to work on this project due to unfinalized hardware decisions, but all are welcome to contribute and examine.

<div align="center"><img src="http://www.dukeconservationtech.com/uploads/9/4/7/7/94777994/drone-logo_1.png?350" width="220" alt="DCT drone logo"></div>

## Attention newcomers!

**Please check out the [Github Wiki](https://github.com/Duke-Conservation-Technology/dct_drone/wiki)** associated with this project. There is plenty of good reading over there that may be essential to you getting a grasp of the project. Some highlights to check out are:

- How to [build a new drone from scratch](https://github.com/Duke-Conservation-Technology/dct_drone/wiki/Building-a-Drone-from-Scratch)


- How to [run the tag tracking demo](https://github.com/Duke-Conservation-Technology/dct_drone/wiki/Tag-Tracking-Quick-Start)

Also, the Technical Overview below is a great way to get some bearings on this project.

## Overview

> **Looking for more?** Keep up with our project at [our website](http://www.dukeconservationtech.com/drone--qr-tracking.html).

Understanding the movement of individual animals gives ecologists and conservationists great insight to animal behavior and well-being. However, current techniques for tracking animals individually are expensive and/or require high amounts of labor to be effective. Advances in drone technology, photography, and computing now allow for a cheap, unique, visual tags to be identified and located using computer vision from the vantage point of a UAV.

We have been developing such a system for tracking tagged animals. Fiducial tags, such as the one below, are put on visible part of animals, which our drone will recognize and localize using computer vision. Fiducial tags are a great starting point because they are uniquely identifiable and easy for computers to find in images.

Target uses for this technology include tracking large numbers of individual animals within concentrated areas and as a remote tag-and-recapture system.

We have chosen to start with tracking sea turtles because tags placed on their shells can be easily observed from above by drone. In the future we hope to expand to more animals such as seals and to utilize neural networks for tagless animal identification.

<div align="center"><img src="https://github.com/Duke-Conservation-Technology/dct_drone/blob/master/tag_images/MarkerData_0.png?raw=true" width="150"></div>


## Installation

To make sure everything is set up correctly, please follow the [Building a Drone from Scratch](https://github.com/Duke-Conservation-Technology/dct_drone/wiki/Building-a-Drone-from-Scratch) guide. This will be the simplest and most direct way to get your hardware ready to run this repository and to operate the recommended hardware.

**If you know what you are doing,** all you need to get everything running is:

- Nvidia Jetson TX2 or TK1 set up via Nvidia Jetpack
- ROS Kinetic or Indigo
- The ROS package [`ar_track_alvar`](http://wiki.ros.org/ar_track_alvar)
- A drone connected to ROS via [`mavros`](http://wiki.ros.org/mavros)
- A working camera publishing data in ROS

Then install this repository into your `catkin_workspace` by running:

```
cd ~/catkin_workspace/src
git clone https://github.com/Duke-Conservation-Technology/dct_drone.git
cd ~/catkin_ws
catkin_make
source ~/.bashrc
```

------

# Technical Overview

We currently have two flavors of the animal tracking drone in development. These unfortunately use slightly different architectures, as compared below. The main elements of note are:

- **Computing Board**: Currently, all drones utilize a powerful computing board that is used to run computer vision and machine learning software and is used to make high level flight decisions. We have chosen to use single board computers from the Nvidia Jetson embedded computing line to perform these duties due to their small size, low power consumption, and excellent computing power all within a familiar Linux environment. For now we use the developer boards created by Nvidia, which are essentially large motherboards with the core processor only taking up a small part of it, but smaller boards are available from third parties that can cut size and weight, as discussed further on.

- **Data Pipeline**: We have also chosen to use the [Robot Operating System](http://www.ros.org/about-ros/) (ROS) to help speed up development and utilize good open-source code that is already available. For example, existing ROS packages let us track visual tags in 3D space out-of-the-box and then use that data in conjunction with GPS location. ROS is a flexible framework that helps computers and different programs (in C++ and/or Python) communicate with each other via libraries that can be imported into your programs, helps extend the drone's capabilities using pre-existing software, and comes paired with a whole host of helpful debugging, visualization, and simulating tools. ROS Indigo and ROS Kinetic are recent, well supported versions of ROS, with Kinetic now in favor for well supported software. ROS Kinetic cannot be run on the TK1, however, because Kinetic requires Ubuntu 16.04, which the TK1 does not support.

- **Flight Controller**: We chose to use the [Erle Brain 3](http://erlerobotics.com/blog/erle-brain-3/) flight controller because it comes out of the box with ROS support. The Erle Brain 3 is essentially a Raspberry Pi 3 with the additional sensors needed to control a drone, as well as pre-configured software that enables the Raspberry Pi run the [Ardupilot](http://ardupilot.org/) (APM) autopilot software and support Mavlink. ROS integration comes from the [Mavros](http://wiki.ros.org/mavros) package, which is pre-configured for use on the Erle Brain. This also means that additional processes (such as ROS nodes or other code) can be run on the Raspberry Pi alongside the flight controller.

  In hindsight, the Erle Brain should be replaced by a standard Pixhawk flight controller running APM, as all of the additional Linux capabilities provided by the  Erle Brain 3 can instead be covered by the Computing Board and because this will mean that we no longer have to deal with the dependency issued of the Linux/ROS version on the Erle Brain having to match that of the Computing Board. In addition, ROS connectivity can be easily obtained (and made compatible with existing code) by connecting the Computing Board to the Pixhawk via [Mavros](http://wiki.ros.org/mavros). By doing so, any Pixhawk drone could be given animal tracking capabilities just by connecting our device to it. 

- **Camera**: The TK1 uses the [See3CAM_CU130 USB Camera](https://www.e-consystems.com/UltraHD-USB-Camera.asp), which has a M12 lens and generally only works at 720p/1080p resolution due to the limitations of the TK1 and USB 3. The TX2 instead uses the [Leopard Imaging IMX377CS](https://www.leopardimaging.com/LI-JETSON-KIT-IMX377CS-X.html) CSI camera with CS-Mount lens. It plugs into the TX2's high speed MIPI camera port and is capable of pushing 4k video at over 15fps through ROS and OpenCV due to the efficiency of the camera ports and power of the TX2. 

  - The IMX377CS is not compatible with the TK1.
  - The IMX377CS lens can be [replaced or upgraded](https://www.back-bone.ca/product-category/lenses/cs-c-mount) with better lenses, allowing for different FOV's and quality. [C-Mount lenses are also compatible with CS-mounts](https://en.wikipedia.org/wiki/C_mount#CS_mount) such as on the IMX377CS, but note that C-Mount lenses will make the image FOV slightly wider. You can even convert professional DSLR camera lenses to C-Mount using [camera mount adapters](https://www.back-bone.ca/product-category/adapters/). Luckily these are all widely available because GoPro uses C-Mount lenses.

## Comparison of TK1 vs TX2 based drones

| **Drone Version** | **Computing Board**                      | **Linux Version**              | **Data Pipeline**                        | **Flight Controller**                    | **Camera**                               |
| ----------------- | ---------------------------------------- | ------------------------------ | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| TK1               | [Nvidia Jetson TK1](http://www.nvidia.com/object/jetson-tk1-embedded-dev-kit.html) developer kit | Ubuntu 14.04 (required by TK1) | [ROS Indigo](http://wiki.ros.org/indigo) | [Erle Brain 3](http://erlerobotics.com/blog/erle-brain-3/) using the [Escarlata](http://forum.erlerobotics.com/t/new-image-escarlata/929) OS image. | [See3CAM_CU130](https://www.e-consystems.com/UltraHD-USB-Camera.asp) USB Camera w/ M12 lens |
| TX2               | [Nvidia Jetson TX2](http://www.nvidia.com/object/embedded-systems-dev-kits-modules.html) developer kit | Ubuntu 16.04 (required by TX2) | [ROS Kinetic](http://wiki.ros.org/kinetic) | [Erle Brain 3](http://erlerobotics.com/blog/erle-brain-3/) using the [Frambuesa](http://forum.erlerobotics.com/t/update-frambuesa-os-for-erle-brain-3/3057) OS image. | [Leopard Imaging IMX377CS](https://www.leopardimaging.com/LI-JETSON-KIT-IMX377CS-X.html) CSI camera w/ CS-Mount lens. |

### Cost Breakdown

The cost of the systems, excluding the drone hardware itself (such as the frame, motors, and propellers) is below.

| **Drone Version** | **Computing Board\***                    | **Flight Controller**\*\*                | **Camera** | **Total** |
| ----------------- | ---------------------------------------- | ---------------------------------------- | ---------- | --------- |
| TK1               | [$200](https://www.amazon.com/NVIDIA-Jetson-TK1-Development-Kit/dp/B00L7AWOEC/ref=pd_sim_147_4?_encoding=UTF8&psc=1&refRID=RT6HX5ZXZFX38GFPJ0P2) | [$240](https://erlerobotics.com/blog/product/erle-brain-3/) | [$140](https://www.e-consystems.com/UltraHD-USB-Camera.asp) |$580 |            |           |
| TX2               | [$600](https://www.amazon.com/NVIDIA-Jetson-TK1-Development-Kit/dp/B00L7AWOEC/ref=pd_sim_147_4?_encoding=UTF8&psc=1&refRID=W3YE9JT302CBTVDRBBS6) | [$240](https://erlerobotics.com/blog/product/erle-brain-3/) | [$380](http://www.shop.leopardimaging.com/product.sc?productId=316&categoryId=44) |$1220 |            |           |

\* Nvidia provides educational discounts ([\$300 for TX2](http://www.nvidia.com/object/jetsontx2-edu-discount.html), [\$129 for TK1](http://www.nvidia.com/object/tk1-edu-discount.html)), but only one per `.edu` email address.

\*\* This is the cost of Erle Brain 3 alone, not including GPS, telemetry, external Wifi, SD card, anti-vibration bed, or extra cables. With these additions, cost can go up to \$620.

## Potential Improvements

- Replace the Erle Brain with a Pixhawk flight controller (as discussed above), and set up the Computing Board to connect to it via [Mavros](http://wiki.ros.org/mavros). This is especially important because Erle Robotics has removed all support for the OS versions that work with the TK1 and because using a Pixhawk will make the drone compatible with many more drones and help the TK1 and TX2 code be more similar.
  - **Note**: in the current architecture, the Erle Brain is set up as the ROS Master and provides Wifi connectivity to the drone by creating it's own access point. These functions will need to be moved to the Computing Board too. For starters, the `~/.bashrc` on the Computing Board will need to be edited to it no longer sets the ip address of the Erle Brain as the ROS Master location.
- Upgrade the TX2's lens with either [new C/CS-Mount lenses](https://www.back-bone.ca/product-category/lenses/cs-c-mount) or by converting DSLR camera lenses to C-Mount using [camera mount adapters](https://www.back-bone.ca/product-category/adapters/), as discussed previously.
- The TX2 is heavy compared to the TK1, but that weight can be significantly reduced by putting the TX2 chip on a smaller and light carrier board, such as the [Sprocket Carrier](http://connecttech.com/product/sprocket-carrier-nvidia-jetson-tx2-jetson-tx1/) or other [3rd party carrier boards](http://connecttech.com/product-category/form-factors/nvidia-jetson-tx2-tx1/). For further reductions, a [passive heatsink](http://connecttech.com/product/nvidia-jetson-tx2-tx1-passive-heat-sink/) or some other [smaller heatsink](https://devtalk.nvidia.com/default/topic/1015249/replace-the-heatsink-with-something-that-is-most-lightweight/) could be used while in flight since the drone will provide plenty of airflow.
- Consider replacing the TK1 camera with a CSI camera, such as the [e-CAM130_CUTK1](https://www.e-consystems.com/13MP-MIPI-Camera-nvidia-Jetson-TK1.asp#key-features). This should allow for higher frame rates at each resolution and will make the software for connecting to the camera the same on both devices (aka via `gstreamer` or `jetson_csi_cam` as done on the current TX2 setup).
- Eventually, it may be best to phase out the TK1 version of the drone if the TX2 is not cost prohibitive.
