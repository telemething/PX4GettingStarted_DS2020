# PX4GettingStarted_DS2020
Getting Started With PX4
"This is a high level overview of how to get started with PX4, hoping to accommodate several different levels of engagement".

What is PX4 (very brief)
"You probably already know this …"
	Hardware
	Software

What is the PX4 community? (very brief)
	Dronecode
	Websites
	Forum
	DevCall
	Etc

What do you want to do with PX4?
"knowledge domains build in layers"
	L1: Operate a PX4 vehicle
	L2: Build a PX4 vehicle
	L3: Build the Source
	L4: Modify the source
	L5: Contribute

L1: Operate a PX4 Vehicle (brief)
"Scenario: You want to operate a PX4 vehicle. It is already built and can maneuver at some level"
Tasks: Pair Radio, Tune (maybe).

L2: Build a PX4 vehicle  (brief)
"Scenario: You want to build a PX4 vehicle. You will acquire physical components and assemble them. You might want to update the PX4 image on the hardware"

L3: Build the source (more depth)
"Scenario:  You want to build PX4 from source"
"Big step up in complexity"
"choose toolchain or container"

	- Toolchain
	"A toolchain is all the stuff youll need to build for a particular target"
	"Win, Mac, Linux. We'll use Ubuntu to build for simulation on Ubuntu"
	"Several convenience scripts, use is recommemded. Manual documented quite well at ref:'Ubuntu Toolchain', so we'll not spend time on that here." 
	"Sometimes the scripts don't meet at requirements, for example : I work with ROS using some openCV features which must be built (not in a distrib), 
	so I need to build ROS up from source too, therefore can't use the ROS convenience script. The same goes for Gazebo. I've run into so many 
	problems with conflicting OpneCV that I always just install the whole chain manually (unless containers!)."
	"For guidance on both convenience scripts and manual installation see ref:'Ubuntu Toolchain'"
	 
	- Container
	"Docker Containers"
	"Docker is a platform for the management of containers. There are others but Docker is the default for non enterprise applications"
	"A container behaves much like a VM (isolation), but is usually smaller and more efficient. They do this with namespaces and cgroups"
	"Containers are in very heavy use in the big clouds and are a popular way of distributing dev environments. They allow you as a consumer
	to plug and play very sophisticated environments which will not conflict with each other or the host."
	"You can elect to use PX4 containers instead of installing and maintaining a PX4 toolchain (recommended)"
	"Containers build upon other containers, each layer contains all aspects of parent layers. An important part of working with containers
	if figuring out which one you want to use. For example: the px4-dev-ros-melodic containers adds ROS to px4-dev-simulation-bionic, which
	itself adds simulation to px4-dev-base-bionic."
	"For more information on Docker see ref: Docker."
	
	- Demo: Build PX4 with a container
	1) Git a copy of PX4 source
		Create a directory for the PX4 source, then Git
		mkdir src_d
		cd src_d
		git clone https://github.com/PX4/Firmware.git
		cd Firmware
	2) Install Docker
		See ref: Docker
	3) Add current user to Docker group
		sudo groupadd docker
		sudo usermod -aG docker $USER
		Log out/in
	4) Select which container you wish to use
		Go to ref:'PX4 Containers", select your container
		We'll use 'px4-dev-simulation-bionic', click on that link
		Notice the 'Docker Pull Command', you can run this command on your command line to download and install the container, but this 
		isn't required. Just note the name of the container: 'px4io/px4-dev-simulation-bionic'.
	5) Run the container
		sudo docker run -it --privileged \
			--env=LOCAL_USER_ID="$(id -u)" \
			-v ~/src_d/Firmware:/src/firmware/:rw \
			-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
			-e DISPLAY=:0 \
			-p 14556:14556/udp \
			--name=px4sim px4io/px4io/px4-dev-simulation-bionic bash
		"Notice PX4 source dir: '~/src_d', container name: 'px4sim', and image name: 'px4io/px4io/px4-dev-simulation-bionic'. 
		"At this point the container images will download, install, and run. Your prompt will change, which indiates that you are now 
		running in the container'"
	6) Navigate to the share on the container, build PX4
		cd ~/src/Firmware
		make px4_sitl_default
		"The conventional build process commences. When complete, the build directory will be present on the share on the host!. You 
		can exit the container or leave it running."
		"On the host (not in the container) navigae to 'src_d', you will see the build directory and output have been created."

L4: Modify the Source (most depth)
	- IDE
	"Visual Studio Code"
	- Debugging 
	- Logs

L5: Contribute (brief)
	- Dev
		Github
		Self-Start or Find Work?
	- Flight Test

Appendix:

Links: Lots of links to resources
<see Links.md>

Ubuntu Toolchain : https://dev.px4.io/v1.9.0/en/setup/dev_env_linux_ubuntu.html

Docker: https://www.docker.com/

PX4 Containers: https://github.com/PX4/containers/


