# Setting up EnergyPlus on Legion

How to run stuff on the Legion server at UCL
--------------

####1. First you need to make sure the correct compilers are loaded. For EnergyPlus you will need:

	module load cmake/2.8.10.2
	module load compilers/gnu/4.9.2

####2. To get the EnergyPlus source code off GitHub you will need to:

	export GIT_SSL_NO_VERIFY=true
	git clone https://github.com/NREL/EnergyPlus
	cd EnergyPlus
	
####3. Checkout the version of EnergyPlus you need e.g.:


	git checkout tags/v8.2.6-3
	
####4. You will need to create the directory where the code is built:

	mkdir ../EnergyPlus<Version>Build      
  
  - where *<Version>* put in the version number

####5. Confure the cmake compiler:

	ccmake ../EnergyPlus<Version>Build
 
  - Press *c* to configure
  - Change Build Type -> *Release*
  - Change path -> *build path* created in step 4
  - Press *c* to re-configure
  - Press *g* to generate the makefile

####6. Compile the code:

	make -j N   

  - where *N* is the number of cores to use e.g. 8
  - Wait ~20 mins

####7. You will now need some of the add on files (auxilary EnergyPlus stuff)
	
	cd ..
	mkdir EnergyPlus<Version>Build/bin
	cp EnergyPlus<Version>Build/Products/* EnergyPlus<Version>Build/bin
	cp EnergyPlus<Version>Build/Energy+.idd EnergyPlus<Version>Build/bin
	git clone https://github.com/phy6phs/Legion
	cp Legion/EnergyPlus_Installation_Files/EnergyPlus<Version>Build/bin
	
	#and add some weather files:
	
	mkdir EnergyPlus<Version>Build/EnergyPlus-8-2-3 
	mkdir EnergyPlus<Version>Build/EnergyPlus-8-2-3/WeatherData
	cp -r Legion/EnergyPlus_Installation_Files/EnergyPlus-8-2-3/WeatherData/* EnergyPlus<Version>Build/EnergyPlus-8-2-3/WeatherData
	
	
####8. In order to run the *runenergyplus* command you will need to add the bin folder to the $PATH variable:
	
	export PATH 
	PATH=$PATH:<path_to_bin_directory>

  - the above two lines should also be added to your .bash_profile file so that you don't have to do this each time you log in

####9. You can then try some test runs using .idf file from EnergyPlus/test

	runenergyplus <example>.idf <weather_file>
	
####10. Or, if you have lots of idfs that need running you can put them all in the same directory and run one of the scripts in the scripts directory (currently only series script):
  - series_script.sh : for running energyplus on all the files in a folder in series
  - to run you will have to edit it so that it points to the right directory working directory (WORK_DIR):
  
  	qsub series_script.sh  	
 
