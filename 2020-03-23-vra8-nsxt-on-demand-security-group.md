# vRA 8 + NSX-T Blog Series Part 6: vRA 8 Blueprint with On-demand Security Group



You can create a vRA 8 blueprint to deploy machines and place them in an on-demand NSX-T security group. 

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
* logical network(s) configured


## Process Overview
1. Create or edit a network profile.
2. Select which existing security groups you'd like to use.
3. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.

optional steps:
* Create inputs in the blueprint to customize the machine name.


## Demo / Example

### Configure Network Profile
1. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE". (or you can choose to edit an existing network profile that has existing networks).
2. Choose an account/region and give the profile a name.
3. Configure existing networks. 
4. Go to "Network Policies" tab and select "Create an on-demand security group" radio button. 
{{<image src="step4.png" linked="true">}}
6. Save the network profile. 

### Create and Configure Blueprint
7. Go to "Blueprints" and Click "+ NEW" to create a new blueprint. (or you can choose to use an existing blueprint that uses existing networks).
8. Give a name to the blueprint and choose a project.
9. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. <b>Note that you do not need to add the Security Group object to the blueprint.</b>
10. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
11. On the right side in the YAML file, choose an image and size for the machine. 
12. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
13. For the network, choose `networkType`.
14. Choose `private` or `outbound` for `networkType`. 
{{<image src="step14.png" linked="true">}}
15. Click "TEST".
16. Click "DEPLOY" to create a new deployment.
17. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

### Verify Deployment
17. Once deployed, log into NSX-T UI and go to "Advanced Networking & Security" > "Inventory" > Groups". You will see that a new security group has been created with the name starting with "isolation-securitygroup".
{{<image src="step17.png" linked="true">}}
18. Go to "Security" > "Distributed Firewall".
19. You will see that a new firewall section has been created for the security group that has just been created. If you set the `networkType` in the blueprint as `private`, you will see that the rules reject all inbound and outbound traffic. If you set the `networkType` as `outbound`, the rules will allow outbound traffic and reject only the inbound traffic.
{{<image src="step19.png" linked="true">}}

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: vm-name
    default: on-demand-sg-vm
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
      # networkType must be private or outbound
      # if it's private, both inbound and outbound are blocked. if it's outbound, only inbound is blocked.
      # networkType existing does not make on-demand sg, it just deploys the machine
      networkType: private
      constraints:
        - tag: 'subnet-cidr:192.168.37.0/24'
```
