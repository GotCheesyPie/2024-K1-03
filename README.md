Install Ubuntu 20.04
Install ROS Noetic
Install Anaconda

Install Crazyswarm following https://crazyswarm.readthedocs.io/en/latest/installation.html using the step-by-step in "Physical Robots and Simulation"
--> note 1: dont forget to disable the anaconda first, before installing Crazyswarm using conmmand: "conda deactivate" so that the installation will not crash with the crazyswarm
--> note 2: check and install the required modules when testing the installation with "$CSW_PYTHON -m pytest"
--> note 3: if the test_videoOutput.py failed, do not worry, you can test individual programs by going to this directory: crazyswarm/ros_ws/src/crazyswarm/scripts, see all the files in it and check the codes by using this command (for example): python3 hello_world.py --sim

Install crazyflie client following https://www.bitcraze.io/documentation/repository/crazyflie-clients-python/master/installation/install/
--> note 1: dont forget to set the udev permissions
--> note 2: after the installation is complete, use this command to start the client "python3 -m cfclient.gui"

Before setting up, set the USB permission:
https://www.bitcraze.io/documentation/repository/crazyflie-lib-python/master/installation/usb_permissions/

Now, use the crazyflie client to assign addresses to each crazyflie. One crazyradio can control up to 10 crazyflies. To change the address using USB (it can be done with the radio as well but using USB avoids some pitfalls)

    1. Unplug your Crazyradio
    2. Start the Crazyflie
    3. Connect the Crazyflie with USB cable
    4. Start the client
    5. You should now have something like "usb://0" in the URI selector in the top left corner
    6. Click connect
    7. Open the Configure 2.X dialog under the "Connect" menu
    8. Change the address (cf1 will use address 0xE7E7E7E701 and cf10 uses address 0xE7E7E7E70A)
    9. Click write
    10. Close the configure dialog
    11. Restart the Crazyflie (turn it off and then turn it on again)
    12. Reconnect by clicking Connect
    13. Reopen the Config dialog --> you should get new address at this stage

If you have the new address, the procedure to connect using the radio should be:

    1. Close the client
    2. Disconnect the USB cable of the crazyflie
    3. Plug in the crazyradio again
    4. Start the client
    5. Enter your new address in the Address field
    6. Click Scan

Your crazyflie should now appear in the URI selector if all is good and clicking connect should connect to it.

Upgrade the crazyflie firmware by following this steps:
1. Go to the menu Connect->Bootloader
2. Connect to the crazyflie in this dialog, if it is not already connected in the flighttab.
3. Select the latest release from the drop down menu or select it if you have downloaded it from the Github release page.
4. Click "Program" and wait for both the STM and NRF to be flashed.
5. If your crazyflie has a lighthouse deck, the lighthouse deck fpga will be updated too (>= release 2021.03)
6. Your crazyflie will automatically restart a couple of times.
7. Do not touch or restart your crazyflie untill all flashing is done and you see "status: idle" at the bottom.
8. To check the firmware version, under the View menu, open up Tabs->Console tab and look at the output when connecting to the Crazyflie 2.X.

Upgrade the crazyradio firmware by following this steps:
1. Download the crazyradio firmware from this address: https://github.com/bitcraze/crazyradio-firmware
2. Put it under your crazyswarm folder and execute: 
2.a. python crazyradio-firmware/usbtools/launchBootloader.py
2.b. sudo python3 crazyradio-firmware/usbtools/nrfbootload.py flash prebuilt/cradio.bin
--> note: if it says that there is no usb module, install it using this command: 
sudo apt-get install python3-usb
then run again the command:
sudo python3 crazyradio-firmware/usbtools/nrfbootload.py flash prebuilt/cradio.bin
you will see "verification secceded!" if it can run perfectly

Control the crazyflie via crazyflie client
1. connect the crazyradio to the PC
2. Open the crazyflie client using the following command on Linux terminal: cfclient
3. put the crazyflie UAV and turn it on
4. scan the device / crazyflie UAV (crazyflie can only control 1 device at once)
5. click connect --> if connected successfully, you can go to the loco positioning (dont forget to add the flowdeck and loco deck on the crazyflie UAV)
6. click configure position --> it should configure the current position of the crazyflie --> change the position of the crazyflie to test if the positioning works
7. Go to flight control and you can try to control your crazyflie to fly to the desired position, or hovering, or etc



TO CONTROL MORE THAN ONE CRAZYFLIE UAVs

CRAZYSWARM CONFIGURATION

Configure external tracking system by editing the file on crazyswarm/ros_ws/src/crazyswarm/launch/hover_swarm.launch
To use LPS/LightHouse/FlowDeck, change the following:
- motion_capture_type: "none"
- enable_logging: True
- enable_logging_pose: True

follow instructions from https://crazyswarm.readthedocs.io/en/latest/configuration.html#configuration for complete configurations

enumerate crazyflies (how many crazyflies are you controlling) by editing the command on crazyswarm/ros_ws/src/crazyswarm/launch/crazyflies.yaml
remember to check the address and channel of each crazyflies and radios using cfclient and configure each of these correctly so that all can be controlled



FIRST TEST (https://crazyswarm.readthedocs.io/en/latest/tutorials/tutorials.html#tutorial-hover)

Go to the following folder: ~/crazyswarm/ros_ws/src/crazyswarm/scripts and run this command:
python3 hello_world.py --sim

In the 3D visualization, you should see a Crazyflie take off, hover for a few seconds, and then land.

If the script runs in simulation, you can move on to real hardware. 
First, go to /crazyswarm folder and start the crazyswarm_server by typing the following commands:
source ros_ws/devel/setup.bash
roslaunch crazyswarm hover_swarm.launch
You should see the positions of crazyflies on RViz with respect to the world coordinates if it has been configured correctly

You can always check/correct your crazyflies' positions by doing this:
Open a new terminal
Check the configuration on crazyflies.yaml on this path: crazyswarm/ros_ws/src/crazyswarm/launch/crazyflies.yaml
Make sure it is correct already by checking the channel and address using cfclient

To test the flight:
On the new terminal (different with the one to open crazyswarm), do:
source ros_ws/devel/setup.bash

Then go to ~/crazyswarm/ros_ws/src/crazyswarm/scripts
deactivate anaconda by using "conda deactivate" command and execute: python3 hello_world.py


////////////////////////////////
To check of crazyflie can connect to the crazyswarm client / PC:
rosrun crazyflie_tools scan --address E7E7E7E701
It should find the Crazyflie as well. Then try:
rosrun crazflie_tools reboot --uri radio://0/100/2M/E7E7E7E701
check if the crazyflie can reboot --> it worked
/////////////////////////////




