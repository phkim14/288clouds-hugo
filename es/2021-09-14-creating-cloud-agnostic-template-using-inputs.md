# Creating a Cloud Agnostic vRA Template with Inputs

# ~ Este post será traducido al español próximamente ~

vRealize Automation 8.x / Cloud allows you to create templates with cloud agnostic resources so that you can create a template that can be used across on-premises data centers and multiple clouds. 

So let's explore how you can create a vRA template that meets the following requirements:
* The user should be able to select the deployment target environment (where the deployment should be created)
* The template should be able to deploy to multiple environments; for this specific demo, the template can deploy to the following environments: on-premises vSphere environment, VMware Cloud on AWS, AWS, and Google Cloud Platform (GCP)

Note that Azure, which is available for use with vRA, is not included in my demo, but the concept remains the same if you want to add Azure to the mix. 

## Demo Product Versions  
* vRA Cloud
* VMC on AWS (SDDC version 1.14v5)
* on-premises data center - vSphere version 7.0.2

## Prerequisites
Working Environments and Access:
* VMware Cloud on AWS SDDC
* AWS
* GCP
* On-premises data center / SDDC with vSphere

vRA Cloud:
* Basic infrastructure configured for all the environments as necessary (i.e., Cloud Proxy, Cloud Account, Project, Cloud Zone, Network Profile, Storage Profile)

## Process Overview
1. Configure image and flavor mappings
2. Apply tags to necessary resources (i.e., network profile, storage profile)
3. Create a cloud template input

## Demo / Example

### Configure Image and Flavor Mappings
You need to configure at least one image mapping and one flavor mapping to use in the cloud agnostic template. The mappings should include all the environments where you want the cloud agnostic template to be able to deploy. 

Below is a flavor mapping I've created for this demo. As you can see, it includes AWS, GCP, VMC on AWS, and on-premises environment. 
{{<image src="flavor-mapping.png" linked="true">}}

Below is an image mapping I've created for this demo. Same as the flavor mapping, this image mapping of an Ubuntu machine includes images from all the environments (AWS, GCP, VMC on AWS, on-prem).
{{<image src="image-mapping.png" linked="true">}}

### Apply Tags
Each target environment should have a tag that you apply to its resources.
{{<image src="cloud-zones.png" linked="true">}}
As you can see in the screen capture above, I have various cloud zones already configured with a specific tag per environment. 
* For GCP, gcp-us-west1
* For AWS, aws-us-west-2
* For on-premises vSphere, sjc-vcsa-01
* For VMC on AWS, vmconaws-sddc-02

I have network profiles created for each environment as well. Each network profile has an appropriate tag that corresponds with its environment. Note that these tags are exactly the same ones as the ones I've used for cloud zones. 
{{<image src="network-profiles.png" linked="true">}}

If you're using storage profiles, you need to apply the same tags there as well. Below is an example of a storage profile I've created to use for VMware Cloud on AWS. It has the same tag, vmconaws-sddc-02, applied to the storage profile.
{{<image src="storage-profile.png" linked="true">}}

In this demo, I'm going to place the deployed machine in an existing security group. I have created an existing security group in each environment. As you can see, each security group has a tag applied, depending on the environment. Note that these tags are the same ones as the ones I've used for cloud zones, network profiles, and other resources. 
{{<image src="security-groups.png" linked="true">}}

### Create Cloud Template Input
Now, this is the fun part. I want the user to choose where they want to deploy the template. This is done by creating a cloud template input. 

To create a cloud template input, go to the "Inputs" tab and click "+ NEW CLOUD TEMPLATLE INPUT". 
{{<image src="inputs-tab.png" linked="true">}}

Let's review the input I've created for the demo. 

{{<image src="targetEnv-input.png" linked="true">}}
* The name of this input is the variable name. I named it "targetEnv".
* The display name of the input is what the user will see in the input form. So I wrote out: "Target Environment".
* You can choose the type of this variable value. In this case, the input value will be a string. 

If you scroll down and expand the "More Options" section, you can see that I've selected "Key Values" to create a dictionary for this input. Each key-value pair represents an environment where the user can choose to deploy. 
{{<image src="targetEnv-input-more-options.png" linked="true">}}
* The key is the name of the environment that the user can choose
* The value is the name of the corresponding tag for that environment

These key-value pairs can be more easily seen in the YAML file. 
{{<image src="targetEnv-YAML.png" linked="true">}}

Basically, the user will be able to pick from one of the available keys (or `title` as seen in the YAML code). The value of the input variable will equate to the corresponding `const` value.

You then use `'${input.targetEnv}'` as a value wherever you need a constraint tag for a cloud agnostic resource. 

For example, I want the cloud agnostic template to use the appropriate existing security group per environment that I've created. So for the cloud security group resource, I use `'${input.targetEnv}'` as the constraint tag. 
{{<image src="sg-constraint-tag.png" linked="true">}}

Same idea applies to the network. I want the cloud agnostic template to use an appropriate network profile that matches the environment that the user has chosen. So for the cloud network resource, I use `'${input.targetEnv}'` as the constraint tag.
{{<image src="network-constraint-tag.png" linked="true">}}

To see the input form that a user might see, you can click "DEPLOY". After you give the deployment a name, you can see the input form.
{{<image src="input-form.png" linked="true">}}


### Demo / Example YAML File

```
formatVersion: 1
inputs:
  targetEnv:
    type: string
    title: Target Environment
    oneOf:
      - title: VMC on AWS
        const: vmconaws-sddc-02
      - title: AWS
        const: aws-us-west-1
      - title: GCP
        const: gcp-us-west1
      - title: Azure
        const: azure-west-us
      - title: On-Premises
        const: sjc-vcsa-01
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: existing
      constraints:
        - tag: '${input.targetEnv}'
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: Ubuntu-20.04
      flavor: size-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
      securityGroups:
        - '${resource.Cloud_SecurityGroup_1.id}'
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: private
      constraints:
        - tag: '${input.targetEnv}'
```
