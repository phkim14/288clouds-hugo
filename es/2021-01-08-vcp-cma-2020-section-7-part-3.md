# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 7 (Objectives 7.15-7.16)

# ~ Este post será traducido al español próximamente ~

This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 7 - Administrative and Operational Tasks

### Objective 7.15 - Describe Kubernetes Cluster
Kubernetes is an open-source container management system that creates virtual containers to separate the kernel from the virtual machine. This separation provides flexible and rapid deployments. 

Kubernetes offers some of the following benefits:
* Autoscaling deployments with an automatic configuration of load balancers
* Enabling simple tasks and systems to run as isolated functions, allowing for dynamic and agile deployments
* Built-in health checks with automatic actions and rebuilding of unresponsive machines

Basic knowledge of Kubernetes is important for vRealize Automation because vRealize Automation 8.x is established in a Kubernetes cluster. 

Here are some of the basic concepts of Kubernetes architecture: 
* Kubernetes cluster is the main level with at least oen master API server and at least one worker node. 
* The worker node(s) host pods, which are virtual container sets. 
* A pod is usually a Docker engine that runs one or more containers. A pod offers the capability to isolate the application layer from the machine. Communication between pods is handled through a proxy server for rapid communication with little overhead. 
* A container is the smallest form of Kubernetes; it is similar to a virtual machine in vSphere vCenter. 

For more details on Kubernetes, please refer to Kubernetes official documentation [here][kubernetes-architecture-official-doc-link]. 


### Objective 7.16 - Customize a Deployment using CloudConfig and Cloud-init
You can use cloudConfig and cloud-init to customize a single blueprint and have it be deployed and run on any cloud. 

#### CloudConfig
CloudConfig is a section in the VMware YAML blueprint code where you can add machine initialize commands that run at deployment time. 

You use initialization commands to customize users, permissions, installations, and more (i.e. set a hostname, install packages, etc.).
* For linux, cloud-init standard is used
* For Windows, Cloudbase-init commands are used

Couple things to note when using cloudConfig: 
* Linux cloud-init and Windows Cloudbase-init do not share the same syntax. Therefore, a cloudConfig section for a linux OS would not work in a machine image of another OS. 
* If you are deploying a vSphere machine with cloudConfig, it must have a DHCP IP address. Static IP addresses are not allowed because the VM template must have a live connection to the network before the cloudConfig scripts can run. Therefore, DHCP must be used. 
* Do not use a vSphere customization specification with a blueprint that uses cloudConfig. cloudConfig is not compatible with vSphere customization specification. 

<b><u>cloudConfig in a Blueprint</b></u><br>
The `cloudConfig:` section should line up with other parts of the machine properties (i.e. `image`, `flavor`, `networks`, etc.). You need a pipe character after the `cloudConfig` directive. All the commands after this pipe character will be sent to the virtual machine cloud-init software after the image is deployed. These commands run only on the first boot. 
{{<image src="cloud-config-sample.png" linked="true">}}

<b><u>cloudConfig in an Image Mapping</b></u><br>
You can also add cloudConfig scripts to an image mapping, but these commands cannot be interactive since inputs are not allowed. 

If you want to add cloudConfig scripts to an image mapping, go to "Infrastructure" and "Image Mappings" then click "+ ADD" under "Cloud Configuration". 
{{<image src="cloud-config-image-mapping.png" linked="true">}}

You then write the cloud configuration script for image mapping. 
{{<image src="cloud-config-image-mapping-2.png" linked="true">}}

Note that if you have cloudConfig commands in both an image mapping and a blueprint, the commands are combined into a single list of commands. If there is a conflict between those two sets of cloudConfig commands, the directives in the image mapping take precedence. 

If you want to learn more about cloud-init and see some cloud config examples, please refer to the cloud-init official documentation [here][cloud-init-cloudconfig-example-link].

#### Cloud-init 
Cloud-init is a set of Python scripts that initialize cloud instances of linux machines; it configures SSH keys and runs commands to customize the machine without any user interaction. 

By default, cloud-init is available in Ubuntu 18 and Photon 3. For CentOS or other versions of Ubuntu and Photon, you need to install cloud-init. Note that not all operating systems support all cloud-init commands. 

Output from cloud-init is found in in the `/var/log/cloud-init-output.log` file after the deployment is completed. The main log file is at `/var/log/cloud-init.log`. If you're having trouble with a cloud-init script, use these logs for troubleshooting. 

<b><u>Create a vSphere Template that Supports Cloud-init (Example)</b></u><br>
This is an example procedure to create a CentOS cloud-init template in vSphere. 
1. Create/deploy a CentOS VM in a vSphere environment
2. Open the CentOS console and login with root account
3. Install the cloud-init software: `yum install -y cloud-init`
4. Install Perl: `yum install -y perl`
* Note: cloud-init and Perl are already installed on Ubuntu 18 but they must be installed manually in CentOS or other versions of Ubuntu. To install cloud-init in Ubuntu, use `apt-get install cloud-init`.
5. Create a file using Nano or vi: `/etc/cloud/cloud.cfg.d/99-custom-networking.cfg`
6. Add a line to the `99-custom-networking.cfg` file: `network: {config: disabled}`
7. Save the `99-custom-networking.cfg` file
* This file takes network control away from cloud-init. This file is not needed if you're making an Ubuntu OS template.
8. Clean the virtual machine: `cloud-init clean`
* Do not do anything else on the virtual machine after running this command.
9. Close the console and shutdown the guest OS from the vSphere client.
10. Edit settings of the VM: select "Passthrough CD-ROM" from the "Device Mode" drop-down menu under "CD/DVD drive"
* <b>Do not forget this step.</b> If CD-ROM is not set to Passthrough mode, the template wil not work with cloud-init because CloudConfig sends instructions to cloud-init through the CD-ROM drive on the virtual machine template.
11. Convert the virtual machine to a template.

For information on how to set up a Windows template with cloudbase-init in vRA, please refer to the official documentation [here][vRA8-windows-cloudinit-template-official-doc-link].


[kubernetes-architecture-official-doc-link]: https://kubernetes.io/docs/concepts/overview/components/
[vRA8-windows-cloudinit-template-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-C995FFE9-CE02-49DC-900B-66473D8A86FB.html
[cloud-init-cloudconfig-example-link]: https://cloudinit.readthedocs.io/en/latest/topics/examples.html
