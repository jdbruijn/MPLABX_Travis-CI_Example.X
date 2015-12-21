MPLABX Travis CI Example
========================
[![License MIT][badge-license]][license]
[![Travis build status][badge-travis]][travis-build]

Example of a MPLABX project with automatic build on [Travis CI][travis-ci].

Documentation
=============
This MPLAB X project is automatically build on Travis CI.
To accomplish this it needs the MPLAB X IDE and the MPLAB XC16 compiler.  
In this documentation I will explain how to set up a repository for Travis CI and how it builds this project.

### Set up repository for Travis CI
In order to set up a repository of a MPLAB X project for automatic builds on Travis CI the `.travis.yml` and `travis-install-dependencies.sh` scripts need to be present in the repository. Examples of those scripts can be found in this repository. Once those files are present in the repository, Travis CI needs to know that it has to build a project. More information on how to setup a repository with Travis CI can be found in their [Getting Started Docs][travis-getstarted].

### Install dependencies script
This script is called by the `.travis.yml` script.  
It is a Linux shell script that installs the software needed for building the MPLAB X project. Thus the MPLAB X IDE and the MPLAB XC16 Compiler. If a project is using a different compiler the download link and install commands need to be adjusted for that specific compiler. Currently the script installs the MPLAB X IDE v3.15 and the MPLAB XC16 Compiler v1.25.  
The software packages are downloaded to a new directory one level below the current working directory, which is in my example `/home/travis/build/vidavidorra/MPLABX_Travis-CI_Example.X`, to keep the project directory clean. So in my example the location where the software packages are downloaded to would be `/home/travis/build/vidavidorra/install-dependencies/`.  
To install these software packages automatically the command line argument `--mode unattended` is used, so no user input is needed in the installation.
For the installation of the MPLAB XC16 Compiler a argument called `netservername` is required, but it does not matter what the netservername is. I use the netservername `dontknow` in my scripts.

### Generate makefiles script
This script is called by the `.travis.yml` script.  
It is a Linux shell script that comes with the Microchip's MPLAB X IDE and it generates makefiles without the need of starting the IDE. So it's way less processor expensive than starting the IDE, and so reducing the build time.
The help of this script is shown below. Currently only the `project_path` argument is given to the script so it generates makefiles for all the build configurations.  

```
$ /opt/microchip/mplabx/v3.15/mplab_ide/bin/prjMakefilesGenerator.sh -help
No project has been specified. There should be at least one. Nothing to do.


Invalid arguments. Usage is:
prjMakefilesGenerator [-help] [-v] [-mplabx-userdir=<path>] [project_path[@config_name]*]+ 
	<project_path>         - path to the MPLAB-X project in the file system
	[config_name]          - (optional) name of a build configuration to generate the makefile for.
	                         If missing, the makefiles are generated for all the build configurations.

	-help                  - displays this help screen.
	-v                     - verbose processing.
	-mplabx-userdir=<path> - the MPLAB-X user directory (useful if you run MPLAB-X with --userdir).

	Example: prjMakefilesGenerator /home/usr/prj1@default@custom /home/usr/prj2 /home/usr/prj3@xc16
	The tool will generate the makefiles for:
	  1.	The configurations named 'default' and 'custom' of the project named 'prj1'
	  2.	All the configurations of the project named 'prj2' (all because none in particular is specified)
	  3.	The configuration named 'xc16' of the project named 'prj3'
```

### Travis script
This script is called by the Travis CI build server.  
Information about how this script works can be found in the [Travis CI Documentation][travis-docs].
The additional packages that are installed in this example are shown below. These are all packages needed by either the MPLAB X IDE or the MPLAB XC16 Compiler.  
Before the installation of the software packages starts the `travis-install-dependencies.sh` script is made executable. After this a file named `prjMakefilesGenerator.sh` is executed. This is a file, provided by Microchip, that generates the makefiles without the need of starting the IDE, which would consume a lot more time. That script is part of the MPLAB X IDE, so basically a IDE of over 500 MB is downloaded just for a small script. I need to do a bit of testing to see if it would be possible to download (and install) just the needed files for that script, which would probably reduce the time needed for the build dramatically.

### Build
The build lasts for about two minutes, depending on the workload and the download speed of the servers.
It needs to download over 600 MB and install several software packages.
See the table below for the detailled sizes and download speed in one of my builds. The sizes of the software are that of the Linux installers.

| Software                  | Size [MB] | Download speed [MB/s] | Download time [s] |
| ------------------------- | --------- | --------------------- | ----------------- |
| MPLAB X IDE v3.15         | 548       | 58.1                  | 9.3               |
| MPLAB XC16 Compiler v1.25 | 59        | 63.3                  | 0.9               |

An example log of one of my builds can be found [here][travis-log], of which the durations can be found in the table below.
As seen the most time is taken by downloading and installing the needed software, like MPLAB X and the MPLAB XC16 Compiler.

| Description                       | Time [s] |
| --------------------------------- | -------- |
| Clone the Git repository          | 0.47     |
| Setting up the server             | 19.43    |
| Download and install dependencies | 101.48   |
| Generate makefiles                | 6.11     |
| Build the source                  | 0.96     |
| **Total duration**                | **131**  |

Licensing
=========
MPLABX Travis CI Example is free software, released under the [MIT license](http://opensource.org/licenses/MIT), as published by the Open Source Initiative.
Modification and redistribution are permitted according to the terms of the MIT license.
The license can be found in the `LICENSE` file.

[Pull requests](https://github.com/vidavidorra/MPLABX_Travis-CI_Example.X/pulls) and [bug reports](https://github.com/vidavidorra/MPLABX_Travis-CI_Example.X/issues) are welcome!


[badge-license]:            https://img.shields.io/badge/license-MIT-blue.svg
[license]:                  https://github.com/vidavidorra/MPLABX_Travis-CI_example.X/blob/master/LICENSE
[badge-travis]:             https://travis-ci.org/vidavidorra/MPLABX_Travis-CI_Example.X.svg?branch=master
[travis-build]:             https://travis-ci.org/vidavidorra/MPLABX_Travis-CI_Example.X
[travis-ci]:                https://travis-ci.org
[travis-getstarted]:        https://docs.travis-ci.com/user/getting-started
[travis-log]:               https://travis-ci.org/vidavidorra/MPLABX_Travis-CI_Example.X/builds/92781353
[travis-docs]:              https://docs.travis-ci.com
