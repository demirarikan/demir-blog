---
layout: post
title:  "Would you let a robot get this close to your neck?"
date:   2022-07-15 18:02:28 -0400
categories: 

---
<figure>
<iframe width="560" height="315" src="https://www.youtube.com/embed/S8de9J3oR5Q?si=ZRlMYy7b4MminG6x" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen style="display: block; margin: auto;"></iframe>
    <figcaption style="text-align: center;">
        Automated ultrasound probe placement using RGB-D camera to estimate the position of the thyroid. The camera view can be seen on the bottom right.
    </figcaption>
</figure>

In ultrasonic thyroid scans the ultrasound (US) probe is placed on the patients neck. In this project me and my friend Sinan tackled the first step of this procedure, placing the probe on the neck automatically. For this project we used the KUKA iiwa 6 DoF robotic arm in combination with the Intel realsense RGB-D camera. 

This was also my first experience with 3D printing, designing and printing a camera holder for the other 3D printed US probe end effector of the robotic arm. 

<figure>
    <img src="{{site.baseurl}}/assets/kap/3d-camera-holder.png" style="width: 100%; max-width: 400px; height: auto; display: block; margin: auto;">
    <figcaption style="text-align: center;">
    My first 3D printed object is the white camera holder attached to the ultrasound probe end effector.
    </figcaption>
</figure>

First the robot would be placed above the bed with the end effector pointing to the ground so that the camera could see the patient. We used OpenPose to get the nose, neck and shoulder keypoint coordinates and calculated the position based on the patients anatomical features. The Z position of the thyroid could be easily read from the RGB-D cameras depth values at the thyroid XY position.

```
thyroid_x = neck_keypoint.x - distance(shoulder_keypoint.x, neck_keypoint.x) * 0.1
thyroid_y = neck_keypoint.y - distance(nose_keypoint.y, neck_keypoint.y) * 0.3
```
<figure>
    <img src="{{site.baseurl}}/assets/kap/openpose-view.png" style="width: 100%; max-width: 300px; height: auto; display: block; margin: auto;">
    <figcaption style="text-align: center;">
    Initial camera view and OpenPose keypoints.
    </figcaption>
</figure>


But this formula gives us only 1 point, hence 1 DoF, which gives us infinite options to select the angle to put the probe. Since we wanted the probe to be perpendicular to the patients neck, so we needed to know what the approximate curvature of their neck at that point is. In order to calculate this we selected 2 additional points, one below and one to the side of thyroid, and created a plane based that spans these 3 points. This allowed us to calculate the normal vector of this plane which was consequently the approach angle of the robot. 

For the robot control, we initially performed an eye in hand calibration using charuco boards to calculate the transformation matrix from the camera space to robot space. Thanks to the [iiwa_stack](https://github.com/IFL-CAMP/iiwa_stack) repo, written by the people at TUM IFL, we were easily able to control the robot easily by sending it cartesian pose messages over ROS. With the thyroid position and the approach angle known, we put everything into a ROS message and published it to move the robot. 
