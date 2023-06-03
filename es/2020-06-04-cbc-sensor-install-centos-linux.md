# Carbon Black Cloud Sensor Install on a Linux Machine

# ~ Este post será traducido al español próximamente ~

Let's manually install a Carbon Black Cloud (CBC) sensor on a CentOS 7 machine! 

## Prerequisites
* Carbon Black Cloud access (Note: the environment I'm using is Carbon Black Cloud Enterprise)
* CentOS 7 machine (can be Ubuntu as well)


## Process Overview
1. Download the Carbon Black Cloud sensor kit on the linux machine.
2. Install the sensor via terminal.
3. Verify that the endpoint shows up in Carbon Black Cloud.

## Demo / Example

### Download Carbon Black Cloud Sensor Kit
1. Log into Carbon Black Cloud from the linux machine where you want to install the sensor.
2. Go to "Endpoints" > "Sensor Options" and select "Download sensor kits". 
{{<image src="step2.png" linked="true">}}
3. Select "Download Kit" of the appropriate OS (in my case, RHEL/CentOS 6 & 7)
{{<image src="step3.png" linked="true">}}
4. Select "Company codes" under "Sensor Options" and make note of the company code. You'll need it during the install.
{{<image src="step4.png" linked="true">}}

### Install Sensor
5. Move the downloaded .tgz file to the Desktop (or wherever you want). 
6. Create a temporary directory on the Desktop (or wherever you placed the .tgz file): `mkdir cb-psc-install`
7. Open terminal and navigate to the Desktop (or wherever you did the previous 2 steps).
8. Extract the .tgz file into the temporary directory you've created: `tar -C <temporary directory name> -zxf <.tgz file name>`
9. Install and register the sensor, using the company code noted from step 4 in the following command: `sudo cb-psc-install/install.sh <company code>`. Once completed, it should say "Success: agent was successfully installed."
{{<image src="step9.png" linked="true">}}

### Verify Installation
10. Run the command `systemctl status cbagentd`	to check the status of the sensor. It should say "active (running)".
{{<image src="step10.png" linked="true">}}
11. Go to Carbon Black Cloud UI > "Endpoints", and your linux machine should now show up. 
{{<image src="step11.png" linked="true">}}
