--- 
layout: "post"
title: "Lab 2 Write Up"
img_url: "/docs/lab1/Dashboard.png"
refrences: [https://github.com/duckietown/dt-core,
            https://github.com/duckietown/dt-core/blob/daffy/packages/led_emitter/src/led_emitter_node.py,
            https://github.com/duckietown/template-ros,
            http://wiki.ros.org/ROS/Concepts,
            https://docs.duckietown.org/daffy/duckietown-robotics-development/out/dt_infrastructure.html,
            https://docs.duckietown.org/daffy/duckietown-robotics-development/out/odometry_modeling.html,
            http://wiki.ros.org/rospy_tutorials/Tutorials/WritingImagePublisherSubscriber]
---

<!-- <h1> Lab 2 Write Up </h1> -->

<p>
        In this lab we implemented the odometry of the duckiebot, as well as use the kinematics of the bot to get it to move in a specified pattern. The first step in doing this was to track how far we had gone in meters. To do this we got the number of ticks recorded by the wheels by subscribe to the topics /left_wheel_encoder_node/tick and /right_wheel_encoder_node/tick then using this data we were able to calculate the distance traveled by out bot by using the formula (2*pi*wheel_raduis*ticks)/135 for each of the wheels . Using those distances we were also able to track the angle of our robot by using the formula ((distance_right - distance_left) / (2 * axel_radius)). Putting these two things together we ended up with the vector for the robot frame (where for the duckiebot yr is always 0) setting the xr to be the distance traveled. In doing this an issue arose where the distance continually increased since the ticks had no way of distinguishing between forward and backward motion, to remedy this we tracked the direction of the wheel based on the velocities we set, and used this to compute the direction of movement of each wheel. In order to get the wheel to rotate at a set velocity we then needed to publish a “WheelsCmdStamped” message to the topic wheels_driver_node/wheels_cmd which has a subscriber that will set the wheel velocities on the physical robot. At this point I should note that Jonathan and I became partners towards the end of the lab, so we both completed the above functionality in slightly different manners, but ultimately decided to complete the lab using his codebase. The last thing that needed to be figured out was how to change the colour of the LED on the bot, so that we could indicate which state it was in. To do this Jonathan created a service proxy that was able to communicate with the led_emitter_node/set_pattern topic and give us control of the bot’s lights. 

</p>
<p>
        Once we knew where the bot was and how to move it we could use this to execute commands such as move forward 1 meter or rotate 180 degrees. To do this I implemented a function that would check if the robot had met the parameters required for the previous command, and if so we would then set the wheel velocity to the necessary speeds for the next command, mark the current distance and angle of the bot -- so that we could track how far the bot had to go until it reached the distance or angle required, and update the onboard LED to indicate the new command being executed. I then placed all the commands needed to complete the predetermined pattern in a list, with each command consisting of a dictionary that held all the information needed (when to finish the previous command and begin executing, the wheel speed required to complete the task, and the LED colour assigned to the command. Then the bot just traversed this list and executed the commands until the list was empty. 
 


</p>

<p>
    At this point a number of issues were encountered, firstly the bot was not moving in a straight line when the wheel speeds were set to the same value. To rectify this issue we set one wheel to have less power than the other, this straightened out the bot after some trial and error. The second issue was the bot kept on overshooting the goal destinations we set in the commands, either going past the expected distance, or over rotating. To rectify this we tried increasing the threshold on when we could say the bot was sufficiently close to an angle or position and begin executing the next command. This worked to some extent, but often resulted in an over correction where the bot would under rotate, or not move far enough. The best solution we found was to set the wheel velocities to be lower, then the slower moving bot was less likely to move past a goal before the bot could get the ticks, calculate the distance, and then publish the next velocity to the wheels. There was still an issue of the movement being rather unpredictable, and we suspected this might be because some momentum from the previous command was being carried over, but unaccounted for, resulting in the bot moving in an unpredicted manner. To test this we put a pause between each of the bots commands, and this resulted in much more predictable movement from the bot confirming our initial suspicions Since this yielded better results this pausing was left in the final codebase. Lastly we then took the robot frame that we had been working in and converted that to the world frame, using the inverse kinematics provided in class. 
</p>

<p>
    We tried to get the rosbag working but Jonathan and I ran out of time, and Huayie was unable to figure out how to do it as well. 

</p>


<h2> Answers to Questions: </h2>

<ul>
    <li>
        To translate between robot frame and initial frame you set 
        world_x = robot_x * cos(robot theta)
        world_y = robot_y * cos(robot theta)
        Wold_theta = robot_theta
        Then for this example, since we started at x = 0.32, y = 0.32 we need to add 0.32 to both world_x and world_y
    </li>
    <li>
        To convert the location and theta of the initial position to the world frame you take robot_x which == 0, thus world_x and world_y are zero plus the 0.32 we add to account for the starting position, and then set the world theta to be the robot theta.
    </li>
    <li>
        There is a difference between the actual location of out bot, and the desired location since the robot's movement is not precise, so the wheels might slip, or there may be a loss in traction, this leads to the robot thinking it has moved when in reality it has not causing this difference. 
    </li>
    <li>
        The topic used to make the motor move is vehical_name/wheels_driver_node/wheels_cmd and the way we figured this out was by looking at the different topics, and then checking the information on ones with names dealing with wheels, and if the type had a structure that seemed to be in the correct format to make it move and there was a subscriber, we tried sending it a message to move the wheels.
    </li>
    <li>
        We used a speed of 0.5 for both wheels, an increase in speed led to a decrease in accuracy, and too low a speed led to the bot not being able to move under its own weight. 
    </li>
    <li>
        To track the angle rotated we used the formula: 
        ((distance_right - distance_left) / (2 * axel_radius))
        Where ditance_left/right was calculated as:
            (2 * pi * wheel_radius * ticks) / 135
    </li>
    <li>
        To make the robot rotate we set the speed of the two wheels in opposite directions, resulting in neutral steering, thus to do this we used the same topic used to make the bot move forward (vehical_name/wheels_driver_node/wheels_cmd)
    </li>
    <li>
        To track the angle rotated we set a variable to 0 initially and then updated it to the rotation angle calculated above every time a new message was published to the wheel tick topic.
    </li>
    <li>
        To drive the robot in a circle we just need to set the velocity of the right, and left wheel to be different speeds in the same direction, then the ratio of these two speeds will lead to a different radius of the circle. 
    </li>
    <li>
        The final position of the robot was:
        World_x = 7.414957177079178
        World_y = 5.215506373748548
        World_theta = 2.5376110196153103
        This was not that close to the actual position of the robot.
    </li>
</ul>

<h2> Images/Videos: </h2>

<h1> Screenshot of camera image view in my own topic </h1>
<ul>
    <p>  This is an <a href="https://drive.google.com/file/d/1XDtMp3fwPTiP-rHIKWO8881Wl9WSTcOd/view?usp=share_link" target="_blank">image</a> of the image that I published to my own topic seen through rqt_image, its exactly the same as the image from the compressed image topic, since that is what I subscribed to in order to get my image.
    </p>
</ul>

<h1> Screenshot of code that shows how to acquire and send images using ros topics </h1>
<ul>
    <p> This <a href="https://drive.google.com/file/d/1yg21LQmskpOFAGkV4Rl7wxPDX1oDg6Y6/view?usp=share_link" target="_blank">image</a> shows the code that allows us to communicate with a ros topic by subscribing to it, in this case an image topic, and then it publishes that image to a differnet topic so that it can be accessed by a different subscriber.
    </p>
</ul>

<h1> Videos of my Duckiebot performing the task defined in part two </h1>
<ul>
    <!-- <iframe width="420" height="315" src="https://youtu.be/BzEZb2VpFs4" frameborder="0"></iframe> -->
    <p> This <a href="https://drive.google.com/file/d/1wiUuJ0EwKTymeTDd-oVdGuFo8J_a2tH7/view?usp=share_link" target="_blank">video</a> shows how we got the duckiebot to follow the set path given in the assignment, there is a lot of deviation from the expected path due to the imprefections of the duckiebot (loss of traction, gear slippage, ect) but for the most part it does what we want and is able to traverse a space given commends. Also here is another video where it performs some parts better than it did in the first video, with a little help from Jonathan <a href="https://drive.google.com/file/d/1wrnlKZkeCrh3XfT4JkjrmiMjoSXX707V/view?usp=share_link" target="_blank">video 2</a>, and one more where there is an example of the bot not being able to rotate under its own weight <a href="https://drive.google.com/file/d/1whm4RT-zLn-frwvhL6uOuf0UWlyXqN7P/view?usp=share_link" target="_blank">video 3</a> </p>
</ul>

<h1> Videos of some of the smaller exercises in part one </h1>
<ul style="list-style-type: none">
    <li>
        <p> This is the <a href="https://drive.google.com/file/d/1wueelVZc_WT6KOLfMjTpzYkspeM-Zty1/view?usp=share_link" target="_blank">move one meter then back video</a> where the duckiebot moved one meter forward, then one back, following pre defined commands, and no human intervention. </p>
    </li>
    <li>
        <p> This is the <a href="https://drive.google.com/file/d/1x2SlyQsovt5KSnkOuLvoV_w1oDzd071M/view?usp=share_link" target="_blank">rotate 90 degrees video</a> where the duckiebot rotated 90 degrees following pre defined commands, and no human intervention. </p>
    </li>
</ul>


<h1> References: </h1>
<p>
<ul >
    {% for refrence in page.refrences %}
        <li>
            <a href="{{refrence}}" target="_blank">{{refrence}}</a> 
        </li>
    {% endfor %}
</ul>
</p>

