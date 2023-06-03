# Creating On-demand Distributed Firewall Rules in VMC on AWS with vRA Cloud

# ~ Este post será traducido al español próximamente ~

Let's explore how you can create on-demand distributed firewall rules in a VMC on AWS environment with a vRA Cloud template.


## Demo Product Versions  
* vRA Cloud
* VMC on AWS (SDDC version 1.13)


## Prerequisites
VMC on AWS:
* Active SDDC

vRA Cloud:
* Basic infrastructure configured for the VMC on AWS environment (Cloud Proxy, Cloud Account, Project, Cloud Zone, Flavor Mapping, Image Mapping)

## Process Overview
1. Create a template that adds a new security group to the deployment.
2. Add firewall rules to the on-demand security group resource.

## Demo / Example

### Create a Template with New Security Group
1. In a vRA Cloud template in vRA Cloud Assembly, drag a Cloud Agnostic Security Group resource on the canvas and connect it to the machine you'd like to place in the security group.
2. Make sure the value for `securityGroupType` is `new` and give a name for the on-demand security group by giving a value for the `name` field.
{{<image src="step2.png" linked="true">}}
Note that you cannot add on-demand distributed firewall rules to an existing security group. If you try, you'll get an error like below.
{{<image src="step2-error.png" linked="true">}}

### Add Firewall Rules to the Security Group
3. Add rules to the on-demand security group resource by adding a `rules` section. You can add multiple rules even though the example only shows one rule added.
{{<image src="step3.png" linked="true">}}

### Verify Deployment
4. After you deploy the template, click on the deployment under "Deployments" in vRA Cloud Assembly. You can then click on the security group resource to view the resource name as well as the firewall rules you've deployed with that security group.
{{<image src="step4.png" linked="true">}}
5. From the VMC on AWS UI, you can go to "Compute Groups" and verify that the security group has been created.
{{<image src="step5-1.png" linked="true">}}
You can also verify that the machine has been placed in this new security group by clicking "View Members". 
{{<image src="step5-2.png" linked="true">}}
6. From the VMC on AWS UI, go to "Distributed Firewall" and in the "Application" section, you'll see that a new policy with the firewall rule as written in the vRA template has been created. Note that the policy is created at the top of the list. 
{{<image src="step6.png" linked="true">}}

### Demo / Example Template YAML File
```
formatVersion: 1
inputs: {}
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: new
      name: pk-ondemand-SG
      rules:
        - ports: 443
          protocol: TCP
          source: Any
          access: Allow
          direction: inbound
          name: ondemand-FW-allow443
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
