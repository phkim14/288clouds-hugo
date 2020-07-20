# vRA 8 + NSX-T Blog Series Part 3: vRA 8 Blueprint with Existing Security Group (network profile)



You can create a vRA 8 blueprint to deploy machines and place them in existing NSX-T security group(s) using a network profile. 

Note that with this method, however, any and all security groups that you select in the network profile will be applied to the machines. If you have multiple machines in a blueprint and want to specify which security group each machine should use then use vRA tag or segment port tag (refer to series part 4 & 5 for those methods).

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
* security group(s) configured


## Process Overview
1. Create or edit a network profile.
2. Select which existing security groups you'd like to use.
3. Create a blueprint with Cloud Agnostic Machine and NSX Network objects.

optional steps:
* Create inputs in the blueprint to customize the machine name.


## Demo / Example

### Configure Network Profile
1. Go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE". (or you can choose to edit an existing network profile).
2. Choose an account/region and give the profile a name.
3. Configure existing networks or on-demand networks. 
4. Go to "Security Groups" tab and select "+ ADD SECURITY GROUP". 
{{<image src="step4.png" linked="true">}}
5. Select security group(s) that you'd like to apply. 
{{<image src="step5.png" linked="true">}}
6. Save the network profile. 

### Create and Configure Blueprint
7. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
8. Give a name to the blueprint and choose a project.
9. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. <b>Note that you do not need to add the Security Group object to the blueprint.</b>
10. Connect the Cloud Agnostic Machine to the NSX Network on the canvas. 
11. On the right side in the YAML file, choose an image and size for the machine. 
12. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
13. For the NSX network, change the `networkType` under `properties` accordingly depending on whether you have configured existing or on-demand networks in the network profile. In this demo, I'll be using an on-demand network. 
{{<image src="step13.png" linked="true">}}
14. Click "TEST".
15. Click "DEPLOY" to create a new deployment.
16. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

### Verify Deployment
17. Once deployed, go to "Deployments" tab in vRA and note the IP address of the deployment.
{{<image src="step17.png" linked="true">}}
18. Now log into NSX-T UI and go to "Inventory" > "Groups".
{{<image src="step18.png" linked="true">}}
19. Click "View Members" of the security group you have selected in the network profile.
20. Click "IP Addresses" and you'll see the IP address of the deployment. 
{{<image src="step20.png" linked="true">}}
