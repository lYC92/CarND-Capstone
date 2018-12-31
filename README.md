# Self-Driving Car Capstone (individual submission)

This is the project repo for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For more information about the project, see the project introduction [here](https://classroom.udacity.com/nanodegrees/nd013/parts/6047fe34-d93c-4f50-8336-b70ef10cb4b2/modules/e1a23b06-329a-4684-a717-ad476f0d8dff/lessons/462c933d-9f24-42d3-8bdc-a08a5fc866e4/concepts/5ab4b122-83e6-436d-850f-9f4d26627fd9).

|Name | email |
|:-----------------|:-------|
| Yangchuan Li | liyangchuan654@gmail.com |

## introduction
In this project, the software that driving Carla is developed. The main function
includes: 1) waypoint updater, 2) DBW, 3) Traffic light detector.

__Note:__ The implementation of this code refer to the tutorial provided in the project guideline video.  

### waypoint updater
This function (node) publish way point ahead of car. When the car face a red light (stop index) it will generate decelerate waypoints to make sure the car will stop.

### Drive-by-wire
This node control steering, brake and throttle of the car.

The throttle is controlled by PID controller. The input is the different of real velocity and desired velocity.

The brake is calculated by ```vel_error * vehicle_mass * wheel_radius```.

The steering is calculated in yaw_controller which compute needed angle to keep needed angular velocity. Also, steering command is filtered with a low pass filter.

### Traffic light detector
The traffic light detector recognize the picture from the ```/image_color``` topic and push the traffic light result to ```/traffic_waypoint``` topic. The classifier model derives from object detection model provided in this [tensor flow repo](https://github.com/tensorflow/models). And also thanks to this helpful [tutorial](https://github.com/alex-lechner/Traffic-Light-Classification) from Alexander Lechner, a classifier model can be derived by using transfer learning technique.

The pre-trained model I used is SSD Inception V2 Coco (17/11/2017). And the labeled data is using the data provided in the [tutorial](https://github.com/alex-lechner/Traffic-Light-Classification). After running the transfer learning program, we got the frozen graph used in classifier.


## Trouble I found
It seems there is bandwith issue on ROS topic information transfer between simulator and ROS controller. Right now I have the trained classifier model and a controller that works. But when I want to combine those, i.e. open the camera mode and using classifier to recognize traffic light. The latency is so significant that doesn't make things work.

To illustrate what I finish, I made the following test in the workspace.

1. In ```tl_detector.py``` I publish the traffic light result from ```/vehicle/traffic_lights``` topic. The purpose is to demo that if the classifier could give me the correct traffic light result, the car will just move as expected. And the result is showed as this, so basically the car drive as expected. [VIDEO](https://youtu.be/YM-ivWJ_Jx4)

2. Open classifier and see what's going on. And the result is not as expected. But as you can see when facing the first traffic light. The car did stop, which shows that classifier is actually working. But later the way point shows bigger and bigger update latency to make the car controller doesn't work. And in both cases we have only 20 waypoints output to save bandwith for image.  [VIDEO](https://youtu.be/MpXzocVA8b0)

So since I have the model trained with good result, I really think the second case could work if there is better computing resource available. In my case, I only have laptop locally, and the workspace seems not work very good as well.

## Installation
In my case, two environment are used.

The first one is in local: the ROS program is running in virtual machine with port forwarding described below. The host OS is MacOS and the simulator runs in it.

The second one is using workspace provided by Udacity which has GPU equipped.

### Native Installation
* Download the Udacity virtual machine and setup [port forwarding](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/16cf4a78-4fc7-49e1-8621-3450ca938b77)
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Usage

In virtual machine

1. Unzip the project zip package

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
roslaunch launch/styx.launch
```

In host OS

4. Run the simulator
