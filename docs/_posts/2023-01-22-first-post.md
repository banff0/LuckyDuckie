--- 
layout: "post"
title: "Lab 1 Write Up"
img_url: "/docs/lab1/Dashboard.png"
txt: "kedm"
---
<p>
        In this lab we implemented the tools and setup to allow us to access the duckiebots using our computer, this included setting up the correct environments on our laptops (docker, github, ROS connection, duckitown terminal, ect.). We also learnt some of the key dts commands that allow us to interface with the duckiebots, drive command, camera command, ect. Another key step in this lab was to set up the duckiebots, though they came pre assembled we still needed to make sure to calibrate the bots, this included making sure the camera inputs were calibrated, the
        wheels were calibrated and the line recognition was set up correctly. We also set up some preferences on the duckiebot to make sure the gain and trim were correct for our purposes. Finally we learnt how to run code on the duckiebot that we had written on our laptops.
</p>
<p>
        Some of the challenges faced in this lab were after I had done all the set up and began the lane follow command the duckiebot worked fine the first time, but following that the next few subsequent tests resulted in the bot not following the lines at all. I resolved this issue
        by relcallibrating the wheel trim and this seemed to resolve the issue leading me to believe this was not an issue with the line recognition.
        The biggest challenge by far though was trying to get the dockerhub image working. My first issue was that I had not read that I needed to change the version we were using to arm64v8 which resulted in the download for opencv taking a very long time. Once I had made the appropriate adjustments I was able to install opencv with no issues. The next issue I encountered was trying to get the boilerplate code for accessing the duckiebot camera with opencv to work. This proved difficult even after consenting the github repo where they set up the gst pipeline() which seemed like it should have worked but did not. 


</p>

<p>
    In this lab we learnt how to access the duckiebots from our laptops and get them to run code we had written on our laptops, meaning that in the future we should be able to make them do rather cool things. We also learnt the basics of docker and how it allows us to work in a separate environment that is easily shareable to a separate device(such as a duckiebot).
</p>

<h2> Dashboard image </h2>
<p> Sorry! I could'nt get the image embeded here, I hope to be more profficient with jekyll by next lab so my site should look a little nicer here is a link to the <a href="https://drive.google.com/file/d/1B91bwVNb5j3sig1g9VRmeKbYeoHKD7Jr/view?usp=share_link" target="_blank">image</a>: 
</p>



<h2> Drive for 2 meters </h2>
<iframe width="420" height="315" src="https://youtu.be/BzEZb2VpFs4" frameborder="0"></iframe>
<p> <a href="https://youtu.be/BzEZb2VpFs4" target="_blank">Link</a> in case the above video does not work </p>

<h2> Lane follow demo </h2>
<iframe width="420" height="315" src="https://youtu.be/5u_FgfXBX1M" frameborder="0" allowfullscreen></iframe>
<p> <a href="https://youtu.be/5u_FgfXBX1M" target="_blank">Link</a> in case the above video does not work </p>
