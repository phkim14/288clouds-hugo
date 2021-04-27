# vRA 8 + NSX-T Blog Series Part 1: vRA 8 Blueprint with Existing NSX-T Networks


# ~ Este post será traducido al español próximamente ~


You can create a vRA 8 blueprint to deploy machines on existing NSX-T networks. 

## Demo Product Versions  
* vSphere 6.5 U3
* vRA 8.0.1 (including vRSLCM and vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## Prerequisites
vRA 8:
* NSX-T account connected
* Basic infrastructure configured (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* logical router (tier-0 or tier-1) configured
* logical segment(s) attached to a logical router


## Process Overview
1. Set up the existing networks with IP ranges you want to use for the machines.
2. Create a network profile.
3. Add existing networks you want to use in the network profile.
4. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.  
5. Specify which network you want to use by adding a constraint tag to the network object. 

optional steps:
* Create tags on the existing networks with names of your choice.
* Create an input in the blueprint to customize the machine name.


## Demo / Example

### Configure Existing Network IP Range
1. Log into vRA Cloud Assembly.
2. Go to "Infrastructure" > "Networks" (under Resources).
3. Click on the existing network you want to use and make sure the information is filled out. 
{{<image src="step3.png" linked="true">}}
4. Click the checkbox next to the existing network you want to use and click "MANAGE IP RANGES".
{{<image src="step4.png" linked="true">}}
5. Click "+ NEW IP RANGE".
6. Create an IP range that you'd like to use to give IP addresses to the machines deployed from this blueprint.
{{<image src="step6.png" linked="true">}}
7. Double-check the start and end IP addresses on the range created to make sure it's correct.
{{<image src="step7.png" linked="true">}}

### Configure Network Profile
8. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE".
{{<image src="step8.png" linked="true">}}
9. Choose an account/region and give the profile a name.
{{<image src="step9.png" linked="true">}}
10. Go to "Networks" tab and click "+ ADD NETWORK".
{{<image src="step10.png" linked="true">}}
11. Choose the existing network(s) you would like to use in the blueprint then save.

### Create Blueprint
12. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
13. Give a name to the blueprint and choose a project.
{{<image src="step13.png" linked="true">}}
14. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. 
{{<image src="step14.png" linked="true">}}
15. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
{{<image src="step15.png" linked="true">}}

### Configure Blueprint
16. On the right side in the YAML file, choose an image and size for the machine. When you click between the quotes, a dropdown menu of available options should show up.
{{<image src="step16.png" linked="true">}}
17. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
18. For the NSX network, make sure it says `networkType: existing` under `properties`.
19. Below `networkType` add the line `constraints:` then another line `- tag:` to choose the existing network you want to use.
{{<image src="step19.png" linked="true">}}
20. Click "TEST".
{{<image src="step20.png" linked="true">}}
21. Click "DEPLOY" to create a new deployment.
22. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".
{{<image src="step22.png" linked="true">}}

### Verify Deployment
23. Monitor the progress. Once completed, you should be able to see the deployment under "Deployments" in vRA UI as well as the new machine created in the vSphere UI.
{{<image src="step23.png" linked="true">}}
{{<image src="step23-1.png" linked="true">}}
{{<image src="step23-2.png" linked="true">}}

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: 'centos-temp'
      flavor: 'small'
      networks:
        - network: '${resource.Cloud_NSX_Network_1.id}'
          assignment: static
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints: 
        - tag: subnet-cidr: 192.168.34.0/24
```
