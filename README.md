
![# ROSMASTER SAM'S CHEAT SHEET V2](https://i.imgur.com/VfweMIg.png)
***This time it's even ROSsier***

This document should serve as a reference for using ROS in !basic! robot development. Not compatible with ROS 2. Tested on melodic & noetic.


**- The basics -**

<!-- This document is written as a .md file. You should be able to see the first line as a bold heading if you opened it in visual studio code/pycharm or any good code editor. 

but if you can see this paragraph, that means you're reading it as text. If you want to see it rendered nicely, open it in one of these programs, 
or paste the contents of this file into an online  markdown editor, 
like https://dillinger.io/ -->

Inside of this file whenever I write a command you should input, I will write it with the following format:
> commandname --mod1 -mod2 argument_1 argument_2 

Pay close attention to the spaces between arguments, they are important!

Replace any argument with one relevant to what you are trying to do. For example I might 
write an argument as "name", and you should write in your own terminal a useful and 
descriptive name you want, not "name"

Finally, note that this document is only intented as an introduction to get you coding nodes with python as fast as possible. For a more in-depth tutorial, or to learn to use c++ please read and follow along with **all** of the "basic" tutorials listed at http://wiki.ros.org/ROS/Tutorials

You won't regret following them, it only takes a couple minutes and its going to help a ton when you're doing your project. I also recommend taking a look at http://wiki.ros.org/rosserial_python, which is a ros package for communticating over a serial bus with ros (to an arduino, teensy etc)

## The Terminal
The first thing we are going to need to do is open a terminal/shell/command prompt. You can do this in a number of ways, 
but I like to press ctrl + alt + t. This will open your linux default terminal. I recommend using Terminator instead 
of the out-of-the box terminal, as being able to split the view cleanly is very useful when running many nodes 
simultaneously.

The terminal is your window to ROS - it is very important to learn linux terminal commands, as understanding them well
will come in very handy over time as you work on projects in ROS. With this in mind, lets create a new workspace!

## Creating a Catkin Workspace
Creating a new catkin workspace is easy! Just create a new folder (I recommend 
putting it inside your "home" folder for easy access). You can do this with the 
graphical file view, but I like to do:
> mkdir catkin_ws_name

You can make all of your packages in the same workspace, but I like to make a new one for each semester project I work on. This makes running the build command a lot faster.

With the new folder created, **cd** into it, and create another folder with the same mkdir command. This folder has to be named "src". This is the **source** folder, where you will write the code for your packages.

After you have created the src folder, now run 
> catkin_make

This will create several new folders, but don't worry about them too much yet. The catkin workspace is now ready for you to create packages!

One more thing though, and this is so important it needs it own subheader:

## Sourcing the workspace

Whenever you want to run a node from a catkin workspace in a terminl window, you 
need to re-source the workspace. This needs to be done in **each and every new 
terminal window**. You will forget to do this a lot, I do too.

To source the workspace, navigate the terminal to the base catkin folder, then 
run the following commnd:

> source ./devel/setup.bash

## Running a Node

After you have sourced the workspace, you may want to run a node from a package. The way to do this is to run:

> rosrun package_name node_in_package_name

## Creating a new Package with a Python Node

To create a new package with a python node, navigate to the src folder, then run

> catkin_create_pkg your_package_name rospy std_msgs ...

Here I have added a ... to show that you can specify more dependencies for your package, 
but rospy and std_msgs should pretty much always be there, because you are going to use python!

Next, go into the folder for your package that catkin created inside src. There are two key files inside each package, and the first we will look at is "*package.xml*"

### Package.xml
Package.xml is a file that tells catkin (and ROS) what the package is called, what it is for, who wrote it, and more informtion about the package. A lot of it might seem 
unimportant, but taking the time to fill in some of this information and remove the comments catkin generated shows a bit of professionalism and pride in your work (and looks good when it comes to exam time!) 

It is an XML file, which is a common file type to store data. Explaining what that is is a bit outside the 
scope of this document, but look inside the file and notice how there are a bunch of lines surrounded with 

&lt;!-- This type of stuff? --&gt;

Those are comments, and will be ignored by ROS and catkin. A lot of them explain helpful things you can add 
to this file and have some sample tags you can add by uncommenting them. 

There will also be some **build_depend, build_export_depend,** and **exec_depend** tags. These are the dependancies of your package. If you want to add a new dependancy after you have created the package, add an entry for the new dependancy for **each** of these tags. 

Next we will look into the belly of the beast, the maw of madness, the:

### CMakeLists.txt

Okay, its not actually *that* bad. But it is a common place to get stuck for a few days wondering why your project won't build. Fortunately with python it's not nearly as bad as with c++, but try not to change too much in this file without understanding **why** you're changing it first, and always save backups if you're trying something new.

Note that we are talking about the CMakeLists.txt *inside your package folder*. There is another file with the same name in the src directory of the workspace, leave that one alone.

This file has comments as well, just like packge.xml. The comments in cmake files are done with the "#" character, and boy are there a lot of them! For python, scroll down to line 160, and uncomment the following part:
>catkin_install_python(PROGRAMS  
&emsp;scripts/my_python_script  
&emsp;DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}  
)

change the part that says "scripts/my_python_script" to the path of the python file (including .py extention) that contains the entry point for your node (if your node only needs one python file, that file is the entry point). You may not have created it yet, so if you didn't and you want a command to easily do that, run the following commands from the base directory of your package

> touch ./src/main_node.py

then 

> chmod +x ./src/main_node.py

Make sure that the path (src/main_node.py) is in place of "scripts/my_python_script" in the package CMakeLists.txt

This will create a python file at that directory, and set it to be executable. Setting it as executable is important for cmake to be able to build it in the way we told it to.

I won't go over how to code nodes themselves, I recommend using http://wiki.ros.org/ROS/Tutorials/WritingPublisherSubscriber%28python%29 as a starter reference for that, and http://wiki.ros.org/rospy/Overview as more complete documentation.

## Building a Package

Now we've done all the work of creating a package with a node in it, we can finally build and then run it!

Go aaaaaall the way down to the catkin workspace base directory, and then run

> catkin_make

This will build all packages in the catkin workspace. Packages really should be rebuilt each and every time you make a change to python or c++ code, but *sometimes* you can get away without re-building with python nodes. If it's behaving strangely, re-build first! 

Finally, remember to [Source the workspace](#sourcing-the-workspace), and then [Run your node!](#running-a-node)

Todo: add a section for messages, services, c++ nodes. Add a section for how to download ros packages like rosserial.

