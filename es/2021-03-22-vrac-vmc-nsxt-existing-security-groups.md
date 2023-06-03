# Using Existing NSX-T Security Groups in VMC on AWS with vRA Cloud

# ~ Este post será traducido al español próximamente ~

There are different ways you can consume existing NSX-T security groups in a VMC on AWS environment with vRA Cloud templates: through a vRA template or through a network profile. 


## Demo Product Versions  
* vRA Cloud
* VMC on AWS (SDDC version 1.13)


## Prerequisites
VMC on AWS:
* Active SDDC

vRA Cloud:
* Basic infrastructure configured for the VMC on AWS environment (Cloud Proxy, Cloud Account, Project, Cloud Zone, Flavor Mapping, Image Mapping)


## Methods
1. Using vRA template
2. Using vRA network profile

## Demos / Examples

### Method 1: Using vRA Template
1. Under "Compute Groups" in the VMC on AWS UI, create a security group. 
{{<image src="method1-step1.png" linked="true">}}
2. In vRA Cloud Assembly, go to "Resources" > "Security" and add a tag for the security group you just created in VMC on AWS.
{{<image src="method1-step2.png" linked="true">}}
3. In a vRA Cloud template in vRA Cloud Assembly, drag a Cloud Agnostic Security Group resource on the canvas and connect it to the machine you'd like to place in the security group.
4. Under `constraints:`, add the line `- tag: <insert tag name>`.
5. Make sure the value for `securityGroupType` is `existing`. 
{{<image src="method1-step5.png" linked="true">}}
6. After you deploy the template and the deployment is finished, you can go to the VMC on AWS UI, click "View Members" for the security group and verify that the newly created machine is placed in the existing security group. 
{{<image src="method1-step6.png" linked="true">}}

After the deployment is completed, you can reconfigure the security group in an existing deployment by changing it from a vRA cloud template and choosing "Update an existing deployment".

#### Method 1 YAML
```
formatVersion: 1
inputs: {}
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      constraints:
        - tag: pk-testGroup
      securityGroupType: existing
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: PKMP-ubuntu
      flavor: PKMP-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
          securityGroups:
            - '${resource.Cloud_SecurityGroup_1.id}'
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: pk-10.15.150.0
```


### Method 2: Using vRA Network Profile
1. In vRA Cloud Assembly, go to "Infrastructure" then "Network Profiles". Create a network profile or edit an existing one.
2. Under "Security Groups", add existing security group(s) that you want to use. In this example, I added two existing security groups.
{{<image src="method2-step2.png" linked="true">}}
3. In a vRA Cloud template in vRA Cloud Assembly, make sure that the machine(s) being deployed will use the network profile you've created. In this example, I have created a capability tag on the network profile and the machine is using that capability tag under `constraints`. <b>Note that you do not need a security group resource in the template. </b>
{{<image src="method2-step3.png" linked="true">}}
4. After you deploy the template and the deployment is finished, you can go to the VMC on AWS UI, click "View Members" for the security group and verify that the newly created machine is placed in the existing security group. I added two security groups to the network profile so the machine deployed has been placed in both security groups. <b>Note that with this method, all the machines deployed with the template will be placed in all the security groups in the network profile.</b>
{{<image src="method2-step4-1.png" linked="true">}}
{{<image src="method2-step4-2.png" linked="true">}}

#### Method 2 YAML 
```
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: PKMP-ubuntu
      flavor: PKMP-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: PKMP-existingSG-NP
```
