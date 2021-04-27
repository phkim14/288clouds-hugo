# vRA 8 + NSX-T Blog Series Part 2: vRA 8 Blueprint with On-demand Routed NSX-T Networks


You can create a vRA 8 blueprint to create an on-demand routed NSX-T network and deploy machines connected to these newly created network. When you tell the blueprint to create an on-demand routed NSX-T network, vRA 8 actually creates a tier-1 gateway that connects to the pre-existing tier-0 gateway. It then creates an on-demand network and attaches it to the tier-1 gateway that it has created.


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
* tier-0 logical router configured


## Process Overview
1. Create a network profile that creates on-demand networks. 
2. Select a tier-0 gateway and edge node in the network profile.
3. Specify the on-demand network details (i.e., CIDR, subnet size, etc.)
4. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.
5. Set the network type as `routed`. 

optional step:
* Create inputs in the blueprint to customize the machine name and the network name.


## Demo / Example

### Configure Network Profile
1. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE".
2. Choose an account/region and give the profile a name.
3. Go to "Network Policies" tab and select "Create an on-demand network" radio button. 
4. Select a transport zone. 
5. Enter CIDR.
6. Select a subnet size.
7. Select the IP range assignment type. This demo uses static assignment as there is no DHCP in the environment. 
8. Under "Network Resources" section, select a tier-0 logical router.
9. Under "Network Resources" section, select an edge cluster. 
10. Click "CREATE" to save the profile. 
{{<image src="step10.png" linked="true">}}

### Create and Configure Blueprint
11. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
12. Give a name to the blueprint and choose a project.
13. Drag a Cloud Agnostic Machine and a NSX Network onto the canvas. 
14. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
15. On the right side in the YAML file, choose an image and size for the machine. 
16. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine.
17. For the NSX network, change the `networkType` under `properties` to `routed`.
{{<image src="step17.png" linked="true">}}
18. Click "TEST".
19. Click "DEPLOY" to create a new deployment.
20. Give it a deployment name, choose "Current Draft", the click "DEPLOY".

### Verify Deployment
21. Log into NSX-T UI and go to the "Advanced Networking & Security" tab (Note that you cannot see this in the NSX-T Simplified UI).
22. Under "Networking" > "Switching", you can see the on-demand segment that has been created from the blueprint.
{{<image src="step22.png" linked="true">}}
23. If you go to "Networking" > "Routers", you will see the tier-1 router that has been created and connected to the tier-0 router that you selected in the network profile.
{{<image src="step23.png" linked="true">}}

You should also be able to see the deployment under "Deployments" in vRA UI as well as the new machine and the new NSX-T segment created in the vSphere UI.

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
    default: on-demand-net-vm
  network-name:
    type: string
    title: network-name
    default: on-demand-ov
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: centos-temp
      flavor: small
      customizationSpec: linux
      name: '${input.vm-name}'
      networks:
        - network: '${resource.Cloud_NSX_Network_1.id}'
          assignment: static
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: routed
      name: '${input.network-name}'
```
<br>
<i>Credit: Huge thanks to my colleague, <a href="https://www.linkedin.com/in/pattonmichael/" target="_blank" rel="noopener noreferrer">Michael Patton</a>, for working with me on this demo.</i> 
