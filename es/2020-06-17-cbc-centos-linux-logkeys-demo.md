# CBC Endpoint Detection Demo with Logkeys

# ~ Este post será traducido al español próximamente ~

Carbon Black Cloud (CBC) can detect if an application on the company blacklist is running then send an alert and deny/terminate the process. Let's setup a demo using Logkeys, a linux application that stores keystrokes with or without the knowledge of user, to see how CBC reacts. 

## Prerequisites
* Carbon Black Cloud access (Note: the environment I'm using is Carbon Black Cloud Enterprise)
* A linux machine (Note: I'm using CentOS 7) - should have administrator/root access
* CBC sensor installed and active on the linux machine

## Process Overview
1. Install logkeys on the linux machine. 
2. Investigate logkeys and add it to the company blacklist.
3. Verify that CBC denies logkeys process from running. 

## Demo / Example

### Install Logkeys
1. Log into the linux machine where you want to install logkeys.
2. Install logkeys using terminal / command line.
```
yum install gcc make gcc-c++
yum install git
git clone https://github.com/kernc/logkeys.git
yum install automake

cd logkeys
./autogen.sh
cd build
../configure
make
sudo make install
```
3. (Optional) Setup keymap: `locale-gen` for Ubuntu or `localedef` for CentOS. To check system locale and keymap, use `localectl status`.
4. (Optional) View logkeys options: `logkeys --help`.

### Use Logkeys
5. As a root user, start logkeys: `sudo logkeys -s -u`.
6. Type a bunch of random commands to test if logkeys is working (i.e. `pwd`, `ls`, `ss`, `ifconfig`).
{{<image src="step6.png" linked="true">}}
7. View logkeys output file to see if the commands were recorded: `nano /var/log/logkeys.log`. Note that logkeys basically recorded gibberish from my typing because I did not configure keymap on the linux machine. 
{{<image src="step7.png" linked="true">}}
8. To terminate logkeys process: `logkeys -k`.

### Investigate Logkeys in CBC
10. Interestingly, Carbon Black (out of the box) detects logkeys as a potential malware when you run it.
{{<image src="step10.png" linked="true">}}
11. You can also search for "logkeys" in "Investigate" and see the alert. 
{{<image src="step11.png" linked="true">}}
12. You can view more details of the alert by viewing the alert triage.
{{<image src="step12.png" linked="true">}}
13. From this view, you can zoom in and click on a process to see more details. You can also take actions like adding the process to whitelist or blacklist. 
{{<image src="step13.png" linked="true">}}

### Configure CBC to Terminate Logkeys
14. For this demo, I add logkeys to the company blacklist. You can view the company blacklist by going to Enforce > Reputation.
{{<image src="step14.png" linked="true">}}
15. I also verify that the policy the linux machine is using terminates applications on the company blacklist when it tries to run.
{{<image src="step15.png" linked="true">}}

### Verify that CBC Blocks Logkeys
16. Now let's test the policy and make sure that CBC blocks logkeys. Go back to the linux machine and try running logkeys. You'll see now that logkeys will not run and see that the operation is not permitted.
{{<image src="step16.png" linked="true">}}
17. If you go to the CBC UI, you will also see some new alerts that show that logkeys has been blocked by the policy and the process has been terminated.
{{<image src="step17.png" linked="true">}}
18. You can also see that the policy deny action has been taken and an alert has been creatd when you look at recent events.
{{<image src="step18.png" linked="true">}}

<br>
<i>Credit: Thanks to my colleague, <a href="https://www.linkedin.com/in/nikolay-nikolov-06617218/" target="_blank" rel="noopener noreferrer">Nikolay Nikolov</a>, for working with me on this demo.</i> 
