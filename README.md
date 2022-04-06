# point_on_circle
Use tracing or rotation to keep track of a point on a moving circle

<p align="center"><img src="https://github.com/SceneDuGreene/point_on_circle/blob/main/tracing_chemtrails.gif" title="circle_plot"> </p>

# Example Code
Running this Example code will save this GIF animation to your Desktop. Make sure to update the **[INSERT USER]** file location at the very bottom of the code.
```Python
import numpy as np
import matplotlib.pyplot as plt 
from matplotlib import animation

# First set up the figure, the axis, and the plot element we want to animate
fig = plt.figure()
ax = plt.axes(xlim=(-1, 3), ylim=(-2, 1)) #set axes limits
ax.set_aspect('equal') #this will ensure that circle looks circular (as opposed to elliptical)

object1, = ax.plot([], [], lw=2)
object2, = ax.plot([], [], lw=2)
centroid,  = ax.plot([], [],'ko', lw=100) #centroid2 tracking
centroid_path,  = ax.plot([], [],'k', lw=2) #centroid2 path linear vel
time_tracker, = ax.plot([], [],'r', lw=2) #track time on t axis
point1,  = ax.plot([], [],'bo', lw=100) #point1 -> point on circle1
point1_path, = ax.plot([], [],'b', lw=2) #particle1 path
point2,  = ax.plot([], [],'mo', lw=100) #point2 -> point on circle2
point2_path, = ax.plot([], [],'m', lw=2) #particle2 path


centroid_path_x = []; centroid_path_y = [] #initialize centroid_path storage
point1_path_x = [] ; point1_path_y = [] #initialize track point path storage
point2_path_x = [] ; point2_path_y = [] #initialize track point path storage
frames = 30; interval = 15 #Constants for animate function

# initialization function: plot the background of each frame
def init():
    object1.set_data([], []) #object1 -> points on circle
    object2.set_data([], []) #object2 -> follow singular point
    centroid.set_data([], []) #centroid2 point
    centroid_path.set_data([], []) 
    time_tracker.set_data([], []) 
    point1.set_data([], []) 
    point1_path.set_data([], []) 
    point2.set_data([], []) 
    point2_path.set_data([], []) 

    return object1, object2, centroid, centroid_path, time_tracker, point1, point1_path,  point2, point2_path

# animation function.  This is called sequentially
def animate(i):
# Draw circles at each frame i over entire animation
    res = 100 # Resolution of shape. keep between 50->200
    x_0 = -0.0 ; y_0 = -0.5 # initial x,y position
    v_x = 1/20 ; v_y= 1/20  #initial velocity that cause motion
    x1, y1, x1_0, y1_0, d_x1, d_y1 = circle_anim(1,i,x_0,y_0,v_x,v_y,res)  #r between 0->1
    x2, y2, x2_0, y2_0, d_x2, d_y2 = circle_anim(0.5,i,x_0,y_0,v_x,v_y,res) #r between 0->1
    r1dot = np.array([[x1_0 + d_x1],[y1_0 + d_y1]]) #moving origin1
    r2dot = np.array([[x2_0 + d_x2],[y2_0 + d_y2]]) #moving origin2
    centroid_x = r2dot[0] ; centroid_y = r2dot[1] #centroid point
    centroid_path_x.append(r2dot[0]);centroid_path_y.append(r2dot[1]) #path 
    ax.set_title("Kinematic Tracing vs. Rotation w/ Chemtrails")
    
# Tracing - follow moving point on circle path
    theta_1 = 0 # Starting orientation
    trace_point = i*int((np.floor(res/frames))) # cycle through various points on the circle (0->100)
    # Will error if above number of frames (theta, linspace<100)
    mat1 = np.array([[np.cos(theta_1),-np.sin(theta_1)],[np.sin(theta_1),np.cos(theta_1)]])
    p1 = r1dot + np.dot(mat1,np.array([[x1[trace_point]-(r1dot[0].item())],[y1[trace_point]-(r1dot[1].item())]])) #constant velocity
    px1= p1[0] ; py1=p1[1]
    point1_path_x.append(p1[0]); point1_path_y.append(p1[1]) #path 

#Rotation - follow stationary point on circle
    omega_2 = -np.pi/4 # Angular rate [rad/s]?
    theta_2_dot = omega_2*i/3 # Starting anguar vel
    rotating_point = x2.argmax()  # choose a point on circle to follow (0->100)
    mat2 = np.array([[np.cos(theta_2_dot),-np.sin(theta_2_dot)],[np.sin(theta_2_dot),np.cos(theta_2_dot)]])
    p2 = r2dot + np.dot(mat2,np.array([[x2[rotating_point]-(r2dot[0].item())],[y2[rotating_point]-(r2dot[1].item())]])) #constant velocity
    px2= p2[0] ; py2=p2[1]
    point2_path_x.append(p2[0]); point2_path_y.append(p2[1]) #path 

    n = 5 #length of chemtrail
    if i<n:
        object1.set_data(x1, y1) #object1 -> points on circle
        object2.set_data(x2, y2) #object2 -> follow singular point
        centroid.set_data(centroid_x, centroid_y)  #centroid2 point
        centroid_path.set_data(centroid_path_x, centroid_path_y) #chemtrails
        time_tracker.set_data((centroid_x, centroid_x),(-1.9, -2))
        point1.set_data(px1, py1)  #object1 -> points on circle1
        point1_path.set_data(point1_path_x,point1_path_y) #point_path circle1
        point2.set_data(px2, py2)  #object2 -> points on circle2
        point2_path.set_data(point2_path_x,point2_path_y) #point_path circle2

    else:
        object1.set_data(x1, y1) #object1 -> points on circle
        object2.set_data(x2, y2) #object2 -> follow singular point
        centroid.set_data(centroid_x, centroid_y)  #centroid2 point
        centroid_path.set_data(centroid_path_x[-n*2:], centroid_path_y[-n*2:]) #chemtrails
        time_tracker.set_data((centroid_x, centroid_x),(-1.9, -2))
        point1.set_data(px1, py1)
        point1_path.set_data(point1_path_x[-n:],point1_path_y[-n:])
        point2.set_data(px2, py2)  #object2 -> points on circle2
        point2_path.set_data(point2_path_x[-n:],point2_path_y[-n:]) #point_path circle2

    return object1, object2, centroid, centroid_path, time_tracker, point1, point1_path, point2, point2_path
    
def circle_anim(r,i,x_0,y_0,v_x,v_y,res): #r between 0->1
    # theta goes from 0 to 2pi
    theta = np.linspace(0, 2*np.pi,res) 
    # the radius of the circle
    # compute cartesian x1 and x2
    d_x = v_x*i ; d_y = v_y*np.sin(i) #calculate change in displacement
    circlex1 = r*np.cos(theta) + x_0 + d_x 
    circley1 = r*np.sin(theta) + y_0 + d_y
    return circlex1, circley1, x_0, y_0, d_x, d_y

anim = animation.FuncAnimation(fig, animate, init_func=init,
                               frames=frames, interval=interval, blit=True)
plt.xlabel("time (s)"); plt.ylabel("Amplitude (m)")

f = r"c://Users/[INSERT USER]/Desktop/point_on_circle.gif"
writergif = animation.PillowWriter(fps=15)
anim.save(f, writer=writergif)

plt.show()
```
