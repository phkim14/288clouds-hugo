# Using On-demand NSX-T Security Groups in VMC on AWS with vRA Cloud

# ~ Este post será traducido al español próximamente ~

There are different ways you can create on-demand NSX-T security groups in a VMC on AWS environment with vRA Cloud templates: through a vRA template or through a network profile. 


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
1. In a vRA Cloud template in vRA Cloud Assembly, drag a Cloud Agnostic Security Group resource on the canvas and connect it to the machine you'd like to place in the security group.
2. Make sure the value for `securityGroupType` is `new` and give a name for the on-demand security group by giving a value for the `name` field.
{{<image src="method1-step2.png" linked="true">}}
3. After you deploy the template and the deployment is finished, you can go to the VMC on AWS UI and verify that a new security group has been created under "Compute Groups". 
{{<image src="method1-step3.png" linked="true">}}
4. You can verify that the machine has been placed in this new security group by clicking "View Members". 
{{<image src="method1-step4.png" linked="true">}}

#### Method 1 YAML
```
formatVersion: 1
inputs: {}
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: new
      name: pk-ondemand-SG-01
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
2. Under "Network Policies", select "On-demand security group" for the "Isolation Policy". 
{{<image src="method2-step2.png" linked="true">}}
Note that if you select "On-demand security group" AND you have existing group(s) added under "Security Groups" tab within the network profile, vRA will place the machine(s) in the on-demand security group it creates AND the existing group(s) that are in the network profile. vRA will only create distributed firewall rules according to the `networkType` you select for the on-demand security group. It will not create any distributed firewall rules using the existing security group(s). 
{{<image src="method2-step2-note-1.png" linked="true">}}
{{<image src="method2-step2-note-2.png" linked="true">}}
{{<image src="method2-step2-note-3.png" linked="true">}}

3. In a vRA Cloud template in vRA Cloud Assembly, make sure that the machine(s) being deployed will use the network profile you've created. In this example, I have created a capability tag on the network profile and the machine is using that capability tag under `constraints`. <b>Note that you do not need a security group resource in the template. </b>
4. You <b>must</b> set `networkType` property for the cloud network as `private` or `outbound` in order for the isolation policy to take effect. For this example, I set the `networkType` as `private`. If you do not use `private` or `outbound` and use another `networkType` like `existing`, vRA will not create an on-demand security group for the deployment. 
{{<image src="method2-step4.png" linked="true">}}
5. After you deploy the template and the deployment is finished, go to "Deployments" in vRA Cloud Assembly and click on the deployment. On the right panel, expand "Network" and you'll see the name of the security group that has been created by vRA. 
{{<image src="method2-step5.png" linked="true">}}
6. You can view the security group in the VMC on AWS UI under "Compute Groups".
{{<image src="method2-step6.png" linked="true">}}
7. If you click on "View Members" for the security group, you can verify that the deployed machine has been placed in this security group. 
{{<image src="method2-step7.png" linked="true">}}
8. In VMC on AWS UI, go to "Distributed Firewall" then "Category Specific Rules". You will see that a new section has been created with rules that deny all inbound and outbound traffic to the newly created security group so that the machines in this security group are isolated. 
{{<image src="method2-step8-1.png" linked="true">}}
Note that if you set the `networkType` as `outbound`, the outbound rule created in this section will be "Allow" instead of "Reject". 
{{<image src="method2-step8-2.png" linked="true">}}

#### Method 2 YAML 
```
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      customizeGuestOs: false
      image: PKMP-ubuntu
      flavor: PKMP-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
          assignment: static
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: private
      constraints:
        - tag: PKMP-isolateSG-NP
```
