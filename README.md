# Teleoperation with HTC Vive HMD and controllers

**Hardware requirements:**
- GPU based system (>= Scheda grafica NVIDIA GeForce RTX 2070 Max-Q)

**Software requirements:**:

- OS: Ubuntu 18.04 lts
- Nvidia GPU Drivers : 460.39
- CUDA : 11.2  
- PL: Python 2.7
- Compiler: cmake C/CXX GNU 7.5.0
- Framework: Ros Melodic   
- Steam : 1.0.0.68
- SteamVR : 1.16.8 

# Installation

- ### `pip` for python 2.7 installation:
    1. #### Install pip:
       ```sh
       $ sudo apt update
       $ sudo apt install python-pip
       ```
    2. #### Check pip version installed:
       ```sh
       $ pip --version
       ```
       
- ### Valve's OpenVR SDK installation:

    1. #### Download Valve's OpenVR SDK
       ```bash
       $ cd ~
       $ mkdir libraries
       $ cd libraries
       $ git clone https://github.com/ValveSoftware/openvr.git -b v1.3.22 
       ```
    2. #### Build the SDK
       ```sh
       $ cd openvr
       $ mkdir build
       $ cd build
       $ cmake -DCMAKE_BUILD_TYPE=Release ../
       $ make
       ```
       
- ### `pyopenvr` library installation:
    1. #### Use `pip2` to download and install `pyopenvr`
    ```bash
    pip install openvr==1.3.2201
    ```
  
- ### ROS Melodic installation:
    1. #### Setup your sources.list
       ```sh
       $ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
       ```
    2. #### Set up your keys
       ```sh
       $ sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
       ```
       If you experience issues connecting to the keyserver, you can try substituting hkp://pgp.mit.edu:80 or hkp://keyserver.ubuntu.com:80 in the previous command.
       Alternatively, you can use curl instead of the apt-key command, which can be helpful if you are behind a proxy server: 
       ```sh
       $     curl -sSL 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC1CF6E31E6BADE8868B172B4F42ED6FBAB17C654' | sudo apt-key add -
       ```
    3. #### Install ROS Melodic Full version
       ```sh
       $ sudo apt update
       $ sudo apt install ros-melodic-desktop-full
       ```
    4. #### Environment Setup
       ```sh
       $ echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
       $ source ~/.bashrc
       ```
    5. #### Dependencies for building packages
       ```sh
       $ sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
       ```
    6. #### Initialize Rosdep
       ```sh
       $ sudo apt install python-rosdep
       $ sudo rosdep init
       $ rosdep update
       ```
    7. #### Create a ROS Workspace
       ```sh
       $ mkdir -p ~/catkin_ws/src
       $ cd ~/catkin_ws/
       $ catkin_make 
       ```
    8. #### Local Environment Setup
       ```sh
       $ echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
       $ source ~/.bashrc
       ```
    9. #### Install additional ROS libraries
       ```sh 
       $ sudo apt-get install ros-melodic-tf -y
       $ sudo apt-get install ros-melodic-tf2* -y
       ``` 
    9. #### Install `vive_ros` package in the catkin_ws
       ```sh 
       $ cd ~/catkin_ws/src
       $ git clone https://github.com/CentroEPiaggio/vive_ros.git
       $ cd ..
       $ catkin_make
       ```
    9. #### Install `vive_ros` package in the catkin_ws
       ```sh 
       $ cd ~/catkin_ws/src/vive_ros
       $ sudo cp ./60-HTC-Vive-perms.rules /etc/udev/rules.d
       $ sudo udevadm control --reload-rules && sudo udevadm trigger
       ```               
- ### Steam and SteamVR installation:
    1. #### Download [Steam](https://store.steampowered.com) latest version. You should get the file steam_latest.deb in your ~/Downloads folder
    2. #### Run Steam 
       ```sh
       $ sudo dpkg --install ~/Downloads/steam_latest.deb
       $ steam
       ```
    3. #### Setup or log in into your Steam account and install SteamVR from the Steam store

# Usage

## Settings:

1.  The package can stream the positions and /tf for HTC controllers, HMD and lighthouses. In addition,
    it is possible to stream inside the HMD the images coming from a stereo-camera (Virtual and real).
    It is necessary to set the desired camera topics name in ```~/catkin_ws/src/vive_ros/launch/vive.launch``` as following:

    ```sh
    <arg name="image_left" default="/your_left_image_topic_name" />
    <arg name="image_right" default="/your_right_image_topic_name"/>
    ```

2. Set `/vive/world_offset` and `/vive/world_yaw` to change the pose of the `world_vive` reference frame.
    
    ```sh
    <rosparam param="/vive/world_offset">[0, 0, 0]</rosparam>
    <rosparam param="/vive/world_yaw">0.0</rosparam>
    ```

## Steam and HTC Vive setup:

1. Plug-in HTC Vive Base Stations
    
    a) Single base mode: select mode "a" with the button behind the base station

    b) Double base mode: select mode "b" on the first base and "c" on the second one, ensure the space between the two base stations
       is free.

2. Plug-in and turn on the HTC Vive HMD

3. Start Steam and SteamVR:
   
   Open a new terminal and run
   
   ```sh
    $ steam
   ```
   
4. Start SteamVR from Steam client

5. Check if the HMD is inside the point of view of the stations and it is tracked by the stations

6. Pair the HTC Joypads, check if they are tracked by the stations

## Launch ROS nodes:

1. Start roscore:
   Open a new terminal and run:
   
   ```sh
    $ roscore
   ```
2. Launch HMD and controllers nodes:
   
   Open a new terminal and run:
   
   ```sh
    $ roslaunch vive_ros vive.launch
   ```
 
4. The sensor frame orientations are shown in the images below.
   
   (DISCLAIMER) The origin of the reference frames drawn is not the real one.

    ![Alt text](images/lighthouse_frame.jpg?raw=true "lighthouse frame")
   
    ![Alt text](images/hmd_frame.jpg?raw=true "lighthouse frame")
   
    ![Alt text](images/controller_frame.jpg?raw=true "lighthouse frame")

## Controllers tracking without HMD:

It is possible to use track the controllers without connecting the HMD, but the controllers must be connected via USB.
To set this mode:

1. Enable a null (simulated) headset editing 
    
   ```sh
    gedit ~/.steam/steam/steamapps/common/SteamVR/resources/settings/default.vrsettings
   ```

2. Change the third line from ```"requireHmd" : true``` to ``` "requireHmd" : false ```

3. Add ```"activateMultipleDrivers" : true```

4. Add the line ```"forcedDriver": "null"``` beneath it.

5. Open ```default.vrsettings```

   ```sh
    $ gedit ~/.steam/steam/steamapps/common/SteamVR/drivers/null/resources/settings/default.vrsettings
   ```

6. Set ```"enable": true``` to enable it.

Now launch Steam and SteamVR and then the nodes as already explained.

