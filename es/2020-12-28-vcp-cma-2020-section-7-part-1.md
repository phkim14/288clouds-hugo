# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 7 (Objectives 7.1-7.7)

# ~ Este post será traducido al español próximamente ~

This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 7 - Administrative and Operational Tasks

### Objective 7.1 - Manage the Identity and Access Management Tab
I think I basically covered this when I was reviewing objective 4.7 so please refer to the exam study guide section 4 [here][VCP-CMA-exam-study-guide-section-4-part-1].


### Objective 7.2 - Manage Cloud Accounts
You can edit configuration details of cloud accounts after they are created.

Depending on the type of cloud account that you create, you may be able to associate the accounts. For example, after you create a vCenter cloud account and a NSX-T cloud account, you can associate those accounts by editing them. 
{{<image src="vcenter-nsx-cloud-account-association.png" linked="true">}}

You can associate multiple vCenters to one NSX-T cloud account by editing the NSX-T cloud account details. This feature is not available in vRA 8.1. It is available starting with vRA 8.2. 
{{<image src="vcenter-nsx-cloud-account-association-2.png" linked="true">}}

If you remove a cloud account used in a deployment, resources that are part of that deployment will become unmanaged. 


### Objective 7.3 - Manage Cloud Zones
You can edit placement policy, manage capability tags, and manually add or remove compute resources associated with the cloud zone.  

There are three different placement strategies you can choose from:
* DEFAULT - place resources on random hosts
* BINPACK - place computes on the most loaded host that still has enough resources to run the given compute
* SPREAD - place computes to the cluster or host with the least number of VMs
{{<image src="placement-policy.png" linked="true">}}


### Objective 7.4 - Manage Projects
You can view all the projects that you have access to under "Infrastructure" and "Projects". From here, you can see some basic information about each project such as:
* Number of administrators
* Number of associated cloud zones
* Number of associated cloud templates
* Number of associated deployments
* Price
{{<image src="projects.png" linked="true">}}

You can validate your configurations by simulating a provisioning request by clicking on "TEST CONFIGURATION".
{{<image src="projects-2.png" linked="true">}}

You select a project that you want to test and provide desired deployment properties. Then this process will go through the projects, cloud zones, and profiles configurations to validate the request. This does not actually provision deployments. 
{{<image src="test-project-configuration.png" linked="true">}}

To edit a project, click "OPEN" and edit details as needed. 


### Objective 7.5 - Manage Image Mappings
You need to have templates available to assign to image mappings. If the templates are not showing up as available options when creating or editing an image mapping, you can run image synchronization for a cloud account to make sure that the images are current. 

You go to "Infrastructure" then "Cloud Accounts" and open a cloud account where you want to run image synchronization. Image synchronization runs automatically every 24 hours but you can manually trigger it by clicking "SYNC IMAGES". This might take some minutes to complete. 
{{<image src="image-sync.png" linked="true">}}

You should organize image mappings for a project by similar OS, tagging strategy, and functional deployment intent.

If you delete an image mapping, it can break blueprints that utilize that image. 


### Objective 7.6 - Manage Flavor Mappings
You use flavor mapping to create similar flavor sizings across your cloud accounts and regions. You should organize flavor mappings for your project by deployment intent. 

You can view all the flavor mappings by going to "Infrastructure" and "Flavor Mappings". You can see how many cloud accounts are configured for each flavor mapping.
{{<image src="flavor-mapping.png" linked="true">}}

If you delete a flavor mapping, it can break blueprints that utilize that flavor. 


### Objective 7.7 - Manage Capability and Constraint Tags
Tags are very important because they control how and where vRA Cloud Assembly uses resources and infrastructure to provision deployments. During provisioning, vRA Cloud Assembly matches all the capability tags with constraint tags in the blueprint to define the deployment configuration. 

Below is a high-level overview of how vRA Cloud Assembly uses tags to define a deployment:
1. Filter cloud zones by criteria (i.e. availability, profiles) then by zone and compute capability tags with hard constraints
2. From this filtered list of cloud zones, use priority to select a cloud zone; if all the cloud zones have the same priority, they are sorted by matching soft constraints (cloud zone and compute capabilities)
3. Select a host in the selected cloud zone by matching hard and soft constraints expressed in the blueprint 

It is crucial to develop a tag strategy and tagging conventions to minimize confusion among users who create and use tags in vRA Cloud Assembly. Here are some best practices to create an effective tag strategy:
* Design a tag strategy that aligns with the structure of your business
* Use simple and meaningful names and values for tags so that it is intuitive for the users to understand
* Do not create duplicate or extraneous/unused tags

To create a tag strategy that aligns with the structure of your business, you should consider your specific use cases and think about some of the following questions:
* How many different types of environments (i.e. production, test, dev, etc.) will you deploy to?
* How many different cloud zones will you deploy to? 
* How are the compute resources structured for deployments?
* Are there different operating systems for deployments? 
* How many storage options are available for deployments?
* Are there storage tiers for different types of deployments? 
* How many networking options are available for deployments? 

<b><u>Capability Tags</u></b><br>
You create capability tags on compute resources, cloud zones, images, networks, network profiles, etc.
* If you add capability tags to cloud zones and network profiles, the tags affect all resources within those zones and profiles.
* If you add capability tags to storage or network components, the tags affect only the components where the tags have been applied.

When you create capability tags, you should already know how you are going to create appropriate blueprint constraints since vRA Cloud Assembly matches capability tags with constraints on blueprints. 

<b><u>Constraint Tags</u></b><br>
Tags added to projects and blueprints are constraint tags. They are matched to capability tags on resources, profiles, and cloud zones to define deployment configurations. 

Cloud administrators can use constraint tags on projects to apply convernance at the project level. If there is a conflict between a project constraint tag and a blueprint constraint tag, the <b>project</b> constraint tag takes precedence. 



[VCP-CMA-exam-study-guide-section-4-part-1]: https://288clouds.com/2020-12-14-vcp-cma-2020-section-4-part-1.html

