# Perception-Challenge-WA

Process:
I first interpreted the instructions and realized that the depth map data has the same orientation and resolution as the stereo camera where the traffic light position is found. After learning about how npz files are organized, I realized that the pixel location from the stereo camera corresponds to the array index of the world point (relative to the car).

For each frame, after finding the center point within the provided traffic light bounds, I retrieved the x and y distances from that point to the car. The tricky part is then using that to find the world position of the car.

After doing some research, I decided to create a simple rotation pipeline (with the help of GPT 5) to convert relative coordinates to world coordinates. I used GPT to learn about the process and provide the math, then I programmed it in.


Method:

Initial values:

I first took the arctangent of x and y to get the angle between the line that connects them and the y-axis (the angle between the car's direction and the light).

I then took pi/2 - that angle and put it into a 2x2 rotation matrix to use for rotatiing 2-dimensional points. The dot product of a vector in R2 with a rotation matrix will rotate that point, so I applied the rotation vector with the relative position of the point from the npz.


Rest of the values:

I found the change in angles given by the arctan on x and y and did basically as described above but with the change in angle from the previous frame, rather than the initial angle (along with some stabilizing formula provided by GPT), created its rotation matrix, and applied it to the car's current position to get the displacement (s).

This calculates the car's rotation-corrected change in position (I hope)

I then updated the car's global position by moving forward s units in the direction of the current rotation (R_acc), which is just each previous frame's change in rotation added together.

