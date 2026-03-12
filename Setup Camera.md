# OV5647 IR Camera Setup for ROS 2 Jazzy (Ubuntu 24.04)

This guide outlines the process for configuring an OV5647 IR (NoIR) camera on a Raspberry Pi 4 running Ubuntu 24.04 Server and publishing the feed to a ROS 2 Jazzy topic.

---

## 1. Physical & Firmware Setup
Ubuntu 24.04 requires manual hardware overlay activation to recognize the CSI camera sensor.

1. Edit the config file:
   ```bash
   sudo nano /boot/firmware/config.txt
  ```

2. Add the following lines at the end of the file
   ```bash
  camera_auto_detect=0
  dtoverlay=ov5647
  dtoverlay=vc4-kms-v3d,cma-128  
  ```

2. Add the following lines at the end of the file
   ```bash
  camera_auto_detect=0
  dtoverlay=ov5647
  dtoverlay=vc4-kms-v3d,cma-128  
  ```
3. Reboot the system
4. Configure permissions
   ```bash
sudo usermod -aG video,render $USER 
  ```

## 2. Installing Hardware-Accelerated Libraries
Standard Ubuntu repositories lack the specific ISP tuning for Pi cameras. Use the Raspberry Pi Foundation repository to get the correct libcamera stack.
1.  Add the Raspberry Pi GPG key and Repository:
   ```bash
   curl -fsSL [https://archive.raspberrypi.org/debian/raspberrypi.gpg.key](https://archive.raspberrypi.org/debian/raspberrypi.gpg.key) | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/raspberrypi.gpg
  echo "deb [http://archive.raspberrypi.org/debian/](http://archive.raspberrypi.org/debian/) bookworm main" | sudo tee /etc/apt/sources.list.d/raspberrypi.list
  sudo apt update
  ```

2. Install the Camera Apps and ROS 2 Driver:
   ```bash
sudo apt install rpicam-apps-lite ros-jazzy-camera-ros
  ```

## 3. Running the ROS 2 Camera Node
The camera_ros node utilizes the Pi's internal ISP to process raw data. Use these parameters to crush visible light and highlight the IR Beacon.

1.  Run Node:
   ```bash
ros2 run camera_ros camera_node --ros-args \
  -p width:=1280 \
  -p height:=720 \
  -p format:="BGR888" \
  -p brightness:=-0.6 \
  -p contrast:=2.5 \
  -p auto_exposure:=false \
  -p frame_duration:=[33333,33333]
  ```
Parameter Breakdown:
- brightness:=-0.6: Suppresses the background/visible light.
- contrast:=2.5: Makes the IR beacon "pop" against the darkness.
- frame_duration: Fixed at 30fps to prevent "out of range" errors.














