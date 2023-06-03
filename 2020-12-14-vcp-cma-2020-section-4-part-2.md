# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 4 (Objectives 4.8-4.14)


This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 4 - Installing, Configuring, and Setup

### Objective 4.8 - Set up Cloud Accounts
Cloud accounts are used to connect vRA to multiple types of resource providers, such as AWS, GCP, Azure, NSX-T, NSX-V, vCenter, and VMC on AWS. You can have multiple cloud accounts for each type of system; however, only one cloud account can connect to each system. 

You can add a cloud account in Cloud Assembly by going to "Infrastructure" then "Cloud Accounts" and clicking "+ ADD CLOUD ACCOUNT". 
{{<image src="add-cloud-account.png" linked="true">}}

Select which type of cloud account you want to add then provide required details. The screenshot below is an example of details needed to add a vCenter cloud account.
{{<image src="add-cloud-account-2.png" linked="true">}}


### Objective 4.9 - Add Cloud Zones
A cloud zone identifies subsystems for provisioning within a cloud account. For example, if you want to provision deployments to a vSphere environment with the vCenter cloud account, data centers or clusters would be the provisioning systems. 

You can create a cloud zone automatically when you create a cloud account or you can create one manually afterwards. 

To create a cloud zone manually, you can go to "Infrastructure" then "Cloud Zones" and click "+ NEW CLOUD ZONE". Select the cloud account you want to use to create a cloud zone and provide the name. After selecting the cloud account, you can navigate to the "Compute" tab and select specific clusters or hosts to use as resources. 
{{<image src="add-cloud-zone.png" linked="true">}}


### Objective 4.10 - Add Projects
Projects organize resources and users; it is similar to the concept of "tenants" in vRealize Automation 7. 

To create a project, go to "Infrastructure" then "Projects" and click "+ NEW PROJECT". From there, under the "Users" tab, you can select groups and users by email address from vIDM to add to the project. 
{{<image src="add-project-users.png" linked="true">}}

From the "Provisioning" tab, you can add cloud zones to the project. The cloud zones can be of different types, such as vSphere, AWS, Google, etc. Each project must have at least one cloud zone. 

You can also set priority of each cloud zone. If there are two cloud zones that match all the criteria needed for provisioning, then the placement logic selects the cloud zone with a higher priority. Lower number means higher priority. 

In addition to provisioning priority, you can set limits on the number of instances that can be deployed within the project, maximum amount of memory that can be used by the cloud zone in MB and maximum number of virtual CPUs that can be used by the cloud zone. 
{{<image src="add-project-provisioning.png" linked="true">}}

From the "Provisioning" tab, you can optionally define the following details:
* Resource Tags - tags to be created for cloud resources during provisioning
* Constraints - network, storage, and extensibility constraints that are matched with capability tags on specific network profiles, storage profiles, and vRealize Orchestrator integrations respectively
* Custom Properties - used for provisioning or to capture additional metadata
* Custom Naming - naming template to be used for machines, networks, security groups, and disks provisioned in this project
* Request Timeout (not shown in the screenshot) - amount of time before the deployment fails
{{<image src="add-project-provisioning-2.png" linked="true">}}


### Objective 4.11 - Add Image Mappings
Image mappings are virtual machine templates. To create an image mapping, go to "Infrastructure" then "Image Mappings" and click "+ NEW IMAGE MAPPING". You can map images to any kind of template. You can also add CloudConfig configuration scripts to each image.

CloudConfig is the VMware YAML blueprint code that sends instructions to cloud-init, and cloud-init is a set of Python scripts that initialize cloud instances of linux machines. 
{{<image src="image-mapping.png" linked="true">}}


### Objective 4.12 - Add Flavor Mappings
Flavor mappings are virtual machine sizes. To create a flavor mapping, go to "Infrastructure" then "Flavor Mappings" and click "+ NEW FLAVOR MAPPING". 

For vSphere and VMC on AWS environments, you define the CPU and memory. For public cloud environments (i.e. AWS, Azure, GCP), you choose which flavor to map to in that specific environment.
{{<image src="flavor-mapping.png" linked="true">}}


### Objective 4.13 - Add Network Profiles 
A network profile defines a group of networks and network settings that are available for a specific cloud account in a particular region or data center. You use network profiles to control placement decisions to choose a network during virtual machine provisioning.

By default, network profiles use the IP Address Management (IPAM) in vRA, but third-party IPAM providers, such as Infoblox, are supported. The configured IPAM solution provides IP address data and related information (i.e. DNS, gateway address) to provisioned machines. 

The IPAM in vRA can be managed and configured by going to "Infrastructure" then "Networks". You can define available IP ranges under the "IP Ranges" tab and view all the IP addresses that are being tracked by the vRA IPAM under the "IP Addresses" tab. 
{{<image src="vra-ipam.png" linked="true">}}

To create a network profile, go to "Infrastructure" then "Network Profiles" and click "+ NEW NETWORK PROFILE". Under the "Summary" tab, you can choose the cloud account and give the network profile a name.

Under the "Networks" tab, you can add networks where machines can be provisioned. Machines can be provisioned to existing, on-demand, or public networks.
* Existing network - network that already exists in the selected cloud account region
* Public network - network that has access to/from the Internet
* On-demand <b>private</b> network - network without outbound nor inbound traffic created by the blueprint during provisioning; network traffic is limited to occur only between the resources on this network
* On-demand <b>outbound</b> network - network that limits traffic to occur between resources deployed on this network but also allows one-way outbound network traffic
* On-demand <b>routed</b> network - network that contains a routable IP space divided across available subnets that are linked together; this specific option is only available for NSX-V and NSX-T networks
{{<image src="network-profile-networks.png" linked="true">}}

You can add a common security group for all the machines deployed using this network profile. If you want a more granular security group to be applied to specific machines, you can do that from the blueprint. You can view available security groups by going to "Infrastructure" then "Security".
{{<image src="network-profile-security-groups.png" linked="true">}}

You can add existing load balancers to a network profile as well. Load balancers you add to a network profile are available when machines are provisioned using this network profile. 
{{<image src="network-profile-load-balancers.png" linked="true">}}

To learn more about how to use vRA 8.x with NSX-T using network profiles and tags, check out my vRA 8 + NSX-T blog series [here][vRA8-NSX-T-blog-series-link].


### Objective 4.14 - Add Storage Profiles 
A storage profile is used to describe the kind of storage to be deployed according to characteristics, such as service level, cost, performance, or purpose. 

You define storage for a cloud account region so each cloud account region contains its own storage profiles. Depending on which cloud account region you choose, available characteristics you can define for the storage can be different. 

For example, you can see the difference in available configurable options when you choose a vSphere account versus an Azure account.
{{<image src="storage-profile-vsphere.png" linked="true">}}
{{<image src="storage-profile-azure.png" linked="true">}}

For a vSphere cloud account storage profile, you can use a vSphere VM storage policy defined in that vSphere environment. 
{{<image src="storage-profile-storage-policy.png" linked="true">}}

You can choose one storage profile in a cloud account region to be the preferred storage for that region during creation. You can mark this after creating the storage profile as well. 
{{<image src="preferred-storage.png" linked="true">}}



[vRA8-NSX-T-blog-series-link]: https://288clouds.com/2020-03-12-vra8-nsxt-blog-series-overview.html
