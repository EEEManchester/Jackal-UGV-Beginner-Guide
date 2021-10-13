# Accessing Jackal ROS remotely.md
A guide for setting up remote access for the ROS running in a Jackal UGV

# Prerequisite
1. A Jackal
2. A remote PC running Ubuntu 18.04, with WiFi enabled
3. A spare PC monitor, mouse and keyboard for Jackal

# 1. Installing ROS-melodic and Jackal metapackages
You will need active internet connection in this step to download the needed packages.

You may follow the [official ROS tutorials](http://wiki.ros.org/melodic/Installation/Ubuntu) to install ROS-melodic on your PC and skip to step 1.6; otherwise, for new ROS users or a fresh Ubuntu installtion, the following steps should be easier to follow ([ref](http://wiki.ros.org/melodic/Installation/Ubuntu)).

### 1.1. Setup your sources.list

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

### 1.2. Set up your keys
```
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

### 1.3. Installation
```
sudo apt update
sudo apt install ros-melodic-desktop-full
```

### 1.4. Environment setup
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 1.5. Dependencies for building packages
```
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
sudo rosdep init
rosdep update
```

### 1.6. Install Jackal metapackages
```
sudo apt-get install ros-melodic-jackal-simulator ros-melodic-jackal-desktop ros-melodic-jackal-navigation
```


# 2. Connect to Jackal
In this section, we will establish wireless connection between your PC and the Jackal

### 2.1 Hardware setup
#### 2.1.1 Power up your Jackal
Press the power button on the HMI panel located at the rear of the vehicle.

#### 2.1.2 Connect to the Integrated Computor
- Push the Internal Access Latch downwards. The Internal Access Latch is located on the front under the cover.
- Undo the User Tray Thumbscrews.
- You should find the Integrated Computor in the User Tray.
- Plug your Monitor, Keyboard and Mouse to the Integrated Computor.

### 2.2 Setup a WiFi hotspot
There are two options here. You can either use a router or [use your PC's wireless adapter to setup a WiFi hotspot](https://www.linuxuprising.com/2018/09/how-to-create-wi-fi-hotspot-in-ubuntu.html). Once your PC is connected to the WiFi network or started the WiFi hotspot, you need to find out your IP address on your remote PC.
```
ip a
```
Note down your wireless IP address as it will be needed later.

### 2.3 Connect Jackal to WiFi
#### 2.3.1 Start the pre-installed network manager (WICD)
*Fun fact: WICD is a curses-based **w**ireless **i**nterface **c**onfiguration **d**aemon*
```
wicd-curses
```
Find the name of the WiFi your remote PC is connected to and move the pointer to highlight it.
#### 2.3.2 Configure the WiFi
- Press the *right arrow* key on your keyboard.
- Highlight `Use Static IPs` and press *space* to put a cross in the box (meaning selected).
- Move the pointer to `IP:` and enter an IP address within the same subnet of your remote PC's wireless network. For example, if your remote PC's wireless IP address is `10.25.50.1` then you can enter `10.25.50.2`.
- Highlight ` Automatically connect to this network` and press *space* to select this option.
- Enter your WiFi Password in `Preshared Key`.
- Press *F10* to save your settings.
#### 2.3.3 Connect
- Make sure the correct WiFi network is highlighted.
- Connect to the WiFi by pressing *C*.
- If all successful, you should see a status text at the bottom of the console: Connected to *your WiFi hotspot name* at *signal strength %* (IP: *your static IP address*)

### 2.4 Connect to Jackal ROS on remote PC
#### 2.4.1 Configure host file
```
echo '10.25.50.2 cpr-jackal' | sudo tee -a /etc/hosts
```
#### 2.4.2 Configure ROS
```
export ROS_MASTER_URI=http://cpr-jackal:11311 # Jackal hostname
export ROS_IP=10.25.50.1 # Your laptop’s wireless IP address
```

If you want to avoid repeating the above command for every new terminal instance, you can create a script, e.g. remote-jackal.sh, with the above commands, then source it.
```
source remote-jackal.sh
```

#### 2.4.3 Test
You can now test if you have connected to Jackal.
```
rostopic list
```
This should print the avaiable `rostopics` in Jackal ROS.
