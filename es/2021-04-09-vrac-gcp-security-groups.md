# vRA Cloud + GCP: Using Security Groups

# ~ Este post será traducido al español próximamente ~

Let's explore how you can apply existing Google Cloud Platform (GCP) network tags when deploying machines to a GCP environment with a vRA cloud template. 


## Demo Product Versions  
* vRA Cloud
* GCP Environment


## Prerequisites
GCP:
* Active GCP project
* Service account with appropriate roles for connectivity with vRA cloud

vRA Cloud:
* Basic infrastructure configured for the GCP environment (Cloud Account, Project, Cloud Zone, Flavor Mapping, Image Mapping)

Note: If you need help setting up a GCP cloud account, you can use [Gary Flynn's blog][garyFlynn-GCPcloudaccount-setup-link] as a resource. 


## Methods
1. Using vRA template
2. Using vRA network profile

## Demos / Examples

### Method 1: Using vRA Template
1. Ensure that you have a network tag in the GCP environment that you'd like to use in a vRA template. The network tag must be in use by at least one firewall rule for it to be discovered by vRA during data collection.
Below is an example of a network tag applied to an existing instance in GCP.  
{{<image src="method1-step1.png" linked="true">}}
Note that a GCP network tag is not a separate resource by itself. You can only create a GCP network tag when you're creating an instance or if you're adding a new network tag to an existing instance. 
For example, when you're creating an instance, find the "Networking" section and you can create tags under "Network tags".
{{<image src="method1-step1-1.png" linked="true">}}
2. In vRA Cloud Assembly, go to "Resources" > "Security" and add a vRA tag to the GCP network tag you'd like to use in a vRA template. 
{{<image src="method1-step2.png" linked="true">}}
If you've just made a new network tag in GCP, you might have to wait until another data collection has been done for that GCP environment. Data collection occurs every 10 minutes automatically. You can check when the last data collection has been done by viewing the cloud account details from "Infrastructure" > "Cloud Accounts" in vRA Cloud Assembly.
3. In a vRA Cloud template in vRA Cloud Assembly, drag a Cloud Agnostic Security Group resource on the canvas and connect it to the machine you'd like to apply the GCP network tag. I only use one security group in this example but you can have multiple security groups attached to one machine.
4. Under `constraints:`, add the line `- tag: <insert tag name>`.
5. Make sure the value for `securityGroupType` is `existing`. 
{{<image src="method1-step5.png" linked="true">}}
Creating on-demand security groups (or GCP network tags) is not supported with GCP. If you try to set the value for `securityGroupType` as `new` and deploy the template, it will fail and give the error like below.
{{<image src="method1-step5-error.png" linked="true">}}

#### Verify Deployment
6. After you deploy the template and the deployment is finished, go to the GCP UI > "Compute Engine" > "VM instances" and click instance that has been created by the vRA deployment.  
{{<image src="method1-step6.png" linked="true">}}
7. Scroll down to "Network interfaces" section and click "View details".
{{<image src="method1-step7.png" linked="true">}}
8. Under "VM instance details" > "Network tags", you should see that the network tag has been applied by the vRA template on this instance. 
{{<image src="method1-step8.png" linked="true">}}

#### Method 1 YAML
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
          assignment: static
          securityGroups:
            - '${resource.Cloud_SecurityGroup_1.id}'
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: pk-test-subnet-10-15-152-0
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: existing
      constraints:
        - tag: instance1-test-network-tag
```


### Method 2: Using vRA Network Profile
1. In vRA Cloud Assembly, go to "Infrastructure" then "Network Profiles". Create a network profile or edit an existing one.
2. Under "Security Groups", add existing security group(s) that you want to use.
{{<image src="method2-step2.png" linked="true">}}
3. In a vRA Cloud template in vRA Cloud Assembly, make sure that the machine(s) being deployed will use the network profile you've created. In this example, I have created a capability tag on the network profile and the machine is using that capability tag under `constraints`. <b>Note that you do not need a security group resource in the template. </b>
{{<image src="method2-step3.png" linked="true">}}

#### Verify Deployment
4. After you deploy the template, go to "Deployments"in vRA Cloud Assembly and click on the deployment that you've created. From there, under "Network" for the machine that has been deployed, you will see that the network tag has been applied.
{{<image src="method2-step4.png" linked="true">}}
5. You can also verify that the network tag has been added to the instance deployed by the vRA template in the GCP UI. 
{{<image src="method2-step5.png" linked="true">}}

At the time of this blogpost, vRA does not support reconfiguring or updating security group(s) of an existing deployment for public clouds. For example, if you change the security group in a vRA cloud template and try to update an existing deployment, it will fail with the error below.
{{<image src="update-sg-error.png" linked="true">}}

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
          assignment: static
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: pk-test-subnet-10-15-152-0
```


[garyFlynn-GCPcloudaccount-setup-link]: https://garyflynn.com/technology/vmware/vrealize-automation-8x-setup-gcp-cloud-account/
