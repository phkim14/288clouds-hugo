# vRA 8 + NSX-T Blog Series Part 4: vRA 8 Blueprint with Existing Security Group (vRA tag)



You can create a vRA 8 blueprint to deploy machines and place them in existing NSX-T security group(s) using vRA tags. 

## Prerequisites
vRA 8:
* NSX-T account connected
* Basic infrastructure configured (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* security group(s) configured


## Process Overview
1. Create a tag on the existing security group(s).
2. Create a blueprint with Cloud Agnostic Machine, NSX Network, and Security Group objects.
3. Specify which security group will be used by adding a constraint on the Security Group object.

optional steps:
* Create inputs in the blueprint to customize the machine name.


## Demo / Example

### Configure Security Group Tag
1. Go to "Infrastructure" > "Security" (under Resources).
2. Select the security group you want to add a tag to.
3. Click "TAGS".
![Step3](step3.png)
4. Enter name of a new tag in "Add tags" section then press "Enter" and save.
![Step4](step4.png)
5. Check that the tag has been created properly.
![Step5](step5.png)

### Create and Configure Blueprint
6. Go to "Blueprints" and Click "+ NEW" to create a new blueprint. (or you can choose to use an existing blueprint and skip this section).
7. Give a name to the blueprint and choose a project.
8. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. 
9. Connect the Cloud Agnostic Machine to the NSX Network on the canvas.
10. On the right side in the YAML file, choose an image and size for the machine. 
11. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
12. For the NSX network, change the `networkType` under `properties` accordingly depending on whether you are using existing or on-demand network. In this demo, I'll be using an existing network.

### Configure Blueprint Security Group
13. Drag a Security Group onto the canvas. <br>
![Step13](step13.png)
14. For the security group, below `securityGroupType`, add the line `constraints:` then another line `- tag:` and type the tag of the existing security group you want to use.
![Step14](step14.png)
15. For the machine, under `networks`, add a line under `assignment` called `securityGroups:`.
16. Add the line `- '${resource.<insert security group object name>.id}'` below to connect the security group to the machine. 
![Step16](step16.png)
17. Click "TEST".
18. Click "DEPLOY" to create a new deployment.
19. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

### Verify Deployment
20. Once deployed, go to "Deployments" tab in vRA and note the IP address of the deployment.
21. Now log into NSX-T UI and go to "Inventory" > "Groups".
22. Click "View Members" of the security group you have selected in the network profile.
23. Click "IP Addresses" and you'll see the IP address of the deployment.

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
    default: existing-sg-vm
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: existing
      constraints:
        - tag: vra-test-sg
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
          securityGroups:
            - '${resource.Cloud_SecurityGroup_1.id}'
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.35.0/24'
```
