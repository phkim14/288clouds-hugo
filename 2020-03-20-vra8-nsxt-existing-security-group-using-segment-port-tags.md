# vRA 8 + NSX-T Blog Series Part 5: vRA 8 Blueprint with Existing Security Group (segment port tag)


You can create a vRA 8 blueprint to deploy machines and place them in existing NSX-T security group(s) by putting tags on the machine segment port(s).

## Update: July 26, 2021
Starting with vRA 8.2, using segment port tags to place machines in NSX-T security groups longer works. 
Thanks to my colleague, [Mukesh Idnani][mukesh-twitter-link], for this finding!

[mukesh-twitter-link]: https://twitter.com/mukeshIdnani 


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
* Security group(s) configured


## Process Overview
1. Configure Membership Criteria of the existing security group(s).
2. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.
3. Configure the tag on the machine network in the blueprint to place a tag on the machine segment port.

optional step:
* Create inputs in the blueprint to customize the machine name.


## Demo / Example

### Configure Security Group Membership Criteria
1. Log into NSX-T and go to "Inventory" > "Groups".
2. Edit the security group you want to configure. 
{{<image src="step2.png" linked="true">}}
3. Click "Set Members".
{{<image src="step3.png" linked="true">}}
4. Click "+ ADD CRITERIA".
5. Select "Segment Port" in the first column under "Criteria".
6. Enter the name of the tag and the scope. You need both for vRA blueprint.
{{<image src="step6.png" linked="true">}}

### Create and Configure Blueprint
7. Go to "Blueprints" and Click "+ NEW" to create a new blueprint (or you can choose to use an existing blueprint and skip this section).
8. Give a name to the blueprint and choose a project.
9. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. <b>Note that you do not need to add the Security Group object to the blueprint.</b>
10. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
11. On the right side in the YAML file, choose an image and size for the machine. 
12. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
13. For the NSX network, change the `networkType` under `properties` accordingly depending on whether you have configured existing or on-demand networks in the network profile. In this demo, I'll be using an existing network. 

### Add Segment Port Tag in Blueprint
14. For the machine, under `networks`, add a line under `assignment` called `tags:`.
15. Add the line `- key: <insert scope name>`.
16. Add the line `value: <insert tag name>` below. Make sure they are aligned.
Note that anything following a hashtag is a comment in YAML.
{{<image src="step16.png" linked="true">}}
17. Click "TEST".
18. Click "DEPLOY" to create a new deployment.
19. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

### Verify Deployment
20. Once deployed, go to "Deployments" tab in vRA and note the IP address of the deployment.
{{<image src="step20.png" linked="true">}}
21. Now log into NSX-T UI and go to "Inventory" > "Groups".
22. Click "View Members" of the security group you have used.
23. Click "IP Addresses" and you'll see the IP address of the deployment. 
{{<image src="step23.png" linked="true">}}
24. If you want to see the segment port tag that has been applied, log into NSX-T and go to "Advanced Networking & Security" > "Switching".
25. Select the overlay network that the machine has been placed on.
26. Find the logical port that belongs to the machine. You can see the VM name if you look at the "Attachment" column. If you don't know the VM name that has been created, go to vRA 8 UI > "Deployments" and check the deployment.
{{<image src="step26.png" linked="true">}}
27. Select the logical port and click "Actions" > "Manage Tags".
{{<image src="step27.png" linked="true">}}
28. You should see the tag that you've specified in the blueprint.
{{<image src="step28.png" linked="true">}}

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
    default: existing-sg-vm-tag-vm
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
          # this is a tag on the segment port, NOT the VM
          tags:
            - key: Three-Tier-App # Scope (defined in NSX-T)
              value: web # Tag (defined in NSX-T)
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.35.0/24'
```

