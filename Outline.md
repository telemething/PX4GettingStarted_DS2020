# PX4GettingStarted_DS2020
Getting Started With PX4
	"This is a high level overview of how to get started with PX4, hoping to accommodate several different levels of engagement".
	"For the sake of brevity we won't cover 'what is PX4' or 'what is the PX4 community', but those subjects are covered
	in the appendix. 

What do you want to do with PX4?
	"knowledge domains build in layers"
	L1: Operate a PX4 vehicle
	L2: Build a PX4 vehicle
	L3: Build the Source
	L4: Modify the source
	L5: Contribute

L1: Operate a PX4 Vehicle (brief)(probably move to appendix)

	"Scenario: You want to operate a PX4 vehicle. It is already built and can (probably) maneuver at some level"
	Tasks: Pair Radio, Configure, Tune (maybe).
	
	- Radio Controller
	"A radio controller is the handheld unit which you use to communicate with the mobile frame during field operations."
	"While it is possible to do without an RC (by use of control from an onboard companion computer), you will
	probably need or want to use a radio controller."
	"A radio controller communicates with the flight controller via a tranceiver which is physically connected with the 
	flight controller. The controller and on board tranciever communicate two way, exchanging commands and telemetry."
	"Prior to flight the controller and tranceiver must be paired to one another."  
	See ref:Radio Controller
	
	- QGroundControl
	"QGroundControl is a ground station, which runs on a computer separate from the PX4 mobile platform. You can use it 
	to peform pretty much any task you need to configure the flight controller and most other hardware mounted on 
	the mobile platform. In addition to configuration the FC can also be used for monitoring and mission tasks."
	"There are several operations which need to be performed prior to flight. The standard procedure is to connect
	the flight controller (on the mobile frame) to a computer via USB and then use QGroundControl to perform those
	operations. Some typical things to perform: airframe selection, sensor orientation calibration, radio channel 
	mapping." 
	See ref:QGroundcontrol
	
	- Steps:

L2: Build a PX4 vehicle  (brief)(probably move to appendix)

	"Scenario: You want to build a PX4 vehicle. You will acquire physical components and assemble them. You might want to update 
	the PX4 image on the hardware"
	"A PX4 flight controller pretty much always comes delivered with PX4 installed."
	"If you want to change that version then follow the instructions at ref:'Firmware Update'"
		

L3: Build the source (more depth)
	"Scenario:  You want to build PX4 from source"
	"Big step up in complexity"
	"choose toolchain or container"
	"although using a container 

	- Toolchain
	"A toolchain is all the stuff youll need to build for a particular target"
	"Win, Mac, Linux. We'll use Ubuntu to build for simulation on Ubuntu"
	"Several convenience scripts, use is recommemded. Manual documented quite well at ref:'Ubuntu Toolchain', so we'll not spend time on that here." 
	"Sometimes the scripts don't meet at requirements, for example : I work with ROS using some openCV features which must be built (not in a distrib), 
	so I need to build ROS up from source too, therefore can't use the ROS convenience script. The same goes for Gazebo. I've run into so many 
	problems with conflicting OpneCV that I always just install the whole chain manually (unless containers!)."
	"For guidance on both convenience scripts and manual installation see ref:'Ubuntu Toolchain'"
	
	- Demo: Build PX4
		"Here we demonstrate building an image for SITL simulation"
		1) Install the toolchain
			<see appendix>
		2) Git a copy of PX4 source
			Create a directory for the PX4 source, then Git
			mkdir src
			cd src
			git clone https://github.com/PX4/Firmware.git --recursive
		3) Navigate to the Firware dir, build PX4
			cd Firmware
			make px4_sitl jmavsim
		4) If all goes well a JMavSim will appear and the console will open the PX4 shell. Give the shell a moment to initialize, then takeoff.
			commander takeoff
		"The build process commences. When complete, the build output will be present under ~/src/Firmware/build/"		
	 
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
	"All interaction of the container with the host is prohibited, except as explicity enabled by command line paramaters. For example: it
	is very common for a directory in the user domain on the host be mapped to a directory on the container through the '-v' paramater, and 
	it is very common to open a network port via the '-p' parameter."
	"For more information on Docker see ref: Docker."
	
	- Demo: Build PX4 with a container
	1) Git a copy of PX4 source
		Create a directory for the PX4 source, then Git
		mkdir src_d
		cd src_d
		git clone https://github.com/PX4/Firmware.git --recursive
		cd Firmware
	2) Install Docker
		See ref: Docker
	3) Add current user to Docker group
		sudo groupadd docker
		sudo usermod -aG docker $USER
		Log out/in
	4) Select which container you wish to usepx4 
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
			--name=px4sim px4io/px4-dev-simulation-bionic bash
		"Notice PX4 source dir: '~/src_d', container name: 'px4sim', and image name: 'px4io/px4io/px4-dev-simulation-bionic'. 
		"At this point the container images will download, install, and run. Your prompt will change, which indiates that you are now 
		running in the container'"
	6) Navigate to the share on the container, build PX4
		cd src/firmware
		make px4_sitl_default
		"The conventional build process commences. When complete, the build directory will be present on the share on the host!. You 
		can exit the container or leave it running."
		"On the host (not in the container) navigate to 'src_d', you will see the build directory and output have been created."

L4: Modify the Source (most depth)

	- IDE
	"Why an IDE?"
	" 1) Intellisense, 2) click build + error list, 3) click debug + single step, 4) Git integration, 5) lots of tools, 6) same IDE for everything, for example ROS.
	"A few options available on Winows, Linux, and Mac. My preferred IDE is Visual Studio Code on Ubuntu so well demonstrate that here."
	
	- Demo: Build PX4 for Visual Studio Code
		1) Git a copy of PX4 source
		Create a directory for the PX4 source, then Git
		mkdir src_vsc
		cd src_d
		git clone https://github.com/PX4/Firmware.git --recursive
		2) Install VSCode. See rfe:'Install Visual Studio Code'
		3) Open VSC, open 'folder' '~/src_vsc/Firmware'. 
		"A lot of config happens, wait, don't break out. Breaking out early will require VSC restart"
		"If 'This workspace has extension recommendations' prompt appears in lower right, click 'Show Recommendations', select all except 'CMake'.
		"More config happens, wait, don't break out."
		4) If [PX4 detect] is not active kit, change to [PX4 detect]. Wait for config to finish.
		5) Select build variant, we'll use '[px4_sitl]' here. Wait for config to finish.
		6) Click 'Build'. A standard build ensues. If problems happen then messages will be seen. Sometimes the 'problems' tab is the best view, 
		sometimes the 'output' tab is the best view. You can double click in either, you may be taken to the problem code.'
	- Debugging 
		1) Set a breakpoint at '~/src-vsc/Firmware/platforms/posix/src/p44/common/main.cpp' in main() on an executable line.
		2) Click the Debug icom in the left horizontal toolbar. Click on the '		Start Debugging' green triangle in the upper left dropdown."
		*  if VSCode brings up dialog with "Errors exist after running preLaunchTask 'gazebo.iris', 
		and 'terminal' tab shows 'Could not open file[/home/<you>/src_vsc/Firmware/Tools/sitl_gazebo/worlds/iris.world]' 
		grab a copy from /home/<you>/src/Firmware/Tools/sitl_gazebo/worlds/iris.world. Then restart VSC.
		3) If all is well you should see a) a build, b) a startup sequence, c) code halting on the breakpoint. At thispoint you can singlestep, 
		add and remove breakpoints, inspect variables, etc.
	-Debugging hardware
		"won't go into detail, but Ill mention some tooling. See ref:'JTAG Debugging'.
		Segger (my choice)
		Olimex
		Dronecode JTAG (easy because of header)
		Babel UAVCAN
	- Logs
		ref: 'Logging'
		*** needs a lot of expansion, this is an area which I feel needs consolidation, docs are all over the place.
	- Console
		ref:'Debug Console'

L5: Contribute (brief)
	- Dev
		Github
		Self-Start or Find Work?
	- Flight Test

------ Appendix: ------

What is PX4 (very brief)
	
	- Software
		"PX4 is flight control software which supports a number of platforms: Fixed wing, rotor, rover, and submarine."
		See ref:'PX4 Airframes'
		"BSD open source, created by Lorenz Meier and team in 2008 while at ETH. Hosted on Github."
		See ref:'PX4 History'
		"Anyone is free to obtain, modify, and deploy PX4."
	- Hardware
		"Pixhawk standard: community support, full compatability.
		Manufacturer supported: shared support, variations in compatibility.
		Unsupported: manufacturer only support, variation in compatibility."
		See ref:'PX4 Hardware'
		

What is the PX4 community? (very brief)

	Dronecode
		"PX4 is part of Dronecode, and Dronecode is part of the Linux Foundation. Dronecode's mission is to support 
		open source drone software"
		See ref:Dronecode
	Website
		"PX4 main site"
		see ref:'PX4 Main site'
	Slack
		"70 channels, something for everyone. Good, but messages time out and disappear quickly on free license."
		See ref:Slack	Forum
		"Traditional message board. Messages don't expire."
		see ref:Forum
	DevCall
		"Standard Scrum, moves quickly, not for chit-chat. A good place for newcomers to observe and get a feel 
		for what's what and look for opportunities to contribute. Hosted on Zoom".
		See ref:Devcall
	Etc

Links: Lots of links to resources

<see Links.md>

- PX4 Airframes: https://docs.px4.io/master/en/airframes/airframe_reference.html

- PX4 History: https://auterion.com/the-history-of-pixhawk/

- PX4 Hardware: https://px4.io/ecosystem/compatible-hardware/

- Dronecode: https://www.dronecode.org/

- Slack: https://slack.px4.io/

- Forum: https://discuss.px4.io/

- Devcall: https://dev.px4.io/master/en/contribute/dev_call.html

- PX4 Main site: https://px4.io/

Ubuntu Toolchain : https://dev.px4.io/v1.9.0/en/setup/dev_env_linux_ubuntu.html

QGroundcontrol http://qgroundcontrol.com/

Firmware Update: https://docs.qgroundcontrol.com/en/SetupView/Firmware.html

Radio Controller: https://docs.px4.io/master/en/getting_started/rc_transmitter_receiver.html

Docker: https://www.docker.com/

PX4 Containers: https://github.com/PX4/containers/

J-Link Visual Studio Code: https://wiki.segger.com/J-Link_Visual_Studio_Code

Install Visual Studio Code: https://code.visualstudio.com/Download

JTAG Debugging: https://dev.px4.io/master/en/debug/swd_debug.html

Debug Console: https://dev.px4.io/master/en/debug/consoles.html

Logging: https://dev.px4.io/master/en/log/logging.html


