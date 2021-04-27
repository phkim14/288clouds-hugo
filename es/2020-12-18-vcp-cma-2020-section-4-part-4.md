# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 4 (Objectives 4.17-4.21)

# ~ Este post será traducido al español próximamente ~


This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 4 - Installing, Configuring, and Setup

### Objective 4.17 - Describe the Onboarding Process
You can use a workload onboarding plan to bring existing, unmanaged machines into vRA Cloud Assembly after adding a cloud account that contains machines that were depoyed outside of vRA Cloud Assembly.

You can create an onboarding plan, select existing machines manually or by using a filtering rule, then run the plan to import the machines. You can onboard up to 3,500 unmanaged machines within a single onboarding plan per hour. You can onboard up to 17,000 unmanaged machines concurrently with multiple onboarding plans per hour. 

The person who runs the workload onboarding plan is automatically assigned as the machine owner. 

To create an onboarding plan, you go to "Infrastructure" then "Onboarding" and click "NEW ONBOARDING PLAN". You then select the cloud account from where you want to onboard machines as well as a project with at least one user to associate the new deployments with. 
{{<image src="onboarding-plan.png" linked="true">}}

Once an onboarding plan is created, you can manually select machines to add to the plan under "Machines" tab. 
{{<image src="onboarding-plan-machines.png" linked="true">}}

If you manually select machines, you can choose how to transform those machines into deployments. You can either have each machine be its own deployment, put all the selected machines into one deployment, add the machines to an existing plan deployment in the current onboarding plan, or add the machines to an existing deployment in vRA Cloud Assembly. 
{{<image src="onboarding-plan-machines-deployment-selection.png" linked="true">}}

You can also create a filtering rule using properties, such as name, address, or tags, to populate machines into the plan under the "Rules" tab. 
{{<image src="onboarding-plan-rules.png" linked="true">}}

After you create a filtering rule, you can go to the "Machines" tab to view the machines that have been added to the plan by the filtering rule. 
{{<image src="onboarding-plan-machines-2.png" linked="true">}}

Once machines have been added to the plan, you can go to the "Deployments" tab and view the deployments that will be created from this plan. Note that if you use a filtering rule to add machines, each machine will be its own separate deployment.
{{<image src="onboarding-plan-deployments.png" linked="true">}}

In the "Deployments" tab, you can also select the deployments and choose to create blueprints (now called cloud templates) in Cloud Assembly format. Each deployment creates its own corresponding blueprint. 

Note that if you are onboarding a vsphere machine, you need to edit the blueprint `imageRef:` field with the correct template name. You can do this while creating the onboarding plan or update the blueprint after the onboarding process is complete. 
{{<image src="onboarding-plan-deployments-cloud-templates.png" linked="true">}}

For more details on onboarding, please refer to the official documentation [here][vRA8-onboarding-official-doc-link].


### Objective 4.18 - Describe Action-Based Extensibility (ABX)
Action-Based Extensibility (ABX) is a serverless function capability integrated into vRA Cloud Assembly that you can use to define extensibility actions and configure them to run after specific events in extensibility subscriptions occur. 

Some terms related to extensibility here (full list [here][vRA8-extensibility-terms-official-doc-link]):
* Event Topic - a set of events that have the same logical intent and structure
* Event - a change in the state in the producer or any of the entites managed by it; an instance of an event topic
* Subscription - users can subscribe to an event topic and define the criteria that triggers notifications; links either extensibility actions or workflows to triggering events
* Workflow Subscription - triggers a vRO workflow
* Action Subscription - triggers an extensibility action (ABX)

To create an extensibility action, you go to "Extensibility" tab and click "+ NEW ACTION". You name the action and choose an associated project or select to share the action with all projects in the organization. 

You can choose from Python, NodeJS, or Powershell as the scripting language for the code. 
{{<image src="abx.png" linked="true">}}

Inputs are created on the side panel to the right. You can add additional inputs if you'd like. You can also select FaaS provider between AWS, Microsoft Azure, or On Prem to change the intended destination. 
{{<image src="abx-2.png" linked="true">}}

You can import and export actions between environments. You go to "Extensibility" tab and click "IMPORT". To export, you click "EXPORT", which is available for each action. 
{{<image src="abx-import-export-actions.png" linked="true">}}

### Objective 4.19 - Describe the Different Types of Tags in vRA

#### Tag Overview
A tag is a text string that is attached to a resource. Multiple tags can be attached to the same resource. The following resources can have tags attached:
* Cloud zones
* Compute resources
* Network profiles
* Storage profiles

You use tags in blueprints to select which resource is used, and therefore, tags are very important in vRA 8.x. 

Tags can be used to organize resources by:
1. Capability (i.e. speed of network, speed or expense of storage, purpose of a cloud zone, etc.)
2. Location (geographical division)
3. Type (i.e. platform type like AWS or GCP, environment purpose like production or dev, etc.)
4. Other criteria (i.e. department, division)

You manage tags from "Infrastructure" then "Tags".
{{<image src="tag-management.png" linked="true">}}

Note that vRA tags are <b>not</b> the same as vSphere tags. They are not directly related to one another. vRA does not collect vSphere tags during data inventory collection. 

#### Capability VS. Constraint Tags
There are 2 different types of tags: capability and constraint. 

<b><u>Capability Tags</b></u><br>
A capability tag is placed on a specific resource to indicate a capability. It can be placed on resources, cloud zones, storage profiles, and network profiles. 

Capability tags are matched to blueprint constraint tags during provisioning. 

Below is an example of a capability tag defined for a cloud zone. The capability tag here would be applied to all compute resources in this cloud zone. 
{{<image src="capability-tag.png" linked="true">}}

Although the capability tag is applied to all compute resources in a cloud zone, the tag does not show as one of the tags when you view the compute resource under the "Compute" resources tab. 
{{<image src="capability-tag-under-resources.png" linked="true">}}

<b><u>Constraint Tags</b></u><br>
A constraint tag is a `key:value` tag used in a blueprint (in YAML) or a project, and it forces a deployment to deploy only on resources where a capability tag matches the constraint. 

Below is an example of a constraint tag used in a blueprint.
{{<image src="blueprint-constraint-tag.png" linked="true">}}

To define constraint tags in a project, you go to "Infrastructure" and "Projects". From there, select a project you want to edit and go to the "Provisioning" tab to add constraint tags. 
{{<image src="project-constraint-tags.png" linked="true">}}

A project constraint tag can be a hard or a soft constraint by specifying `key:value:hard` or `key:value:soft` respectively. By default, project constraint tags are hard constraints. If a hard constraint is not met, the deployment will fail. The deployment will not fail if a soft constraint is not met since a soft constraint is a preference.

You can also avoid deploying to specific resources by creating a project constraint tag in a `!key:value` format. 

Note that if there is a conflict between a project constraint tag and a blueprint constraint tag, the <b>project constraint tag</b> takes precedence. 

### Objective 4.20 - Configure Capability Tags
You can create a capability tag by going to the resource where you want to create a capability tag. 

For example, let's say you want to create a capability tag for a network profile. When you go to create or edit a network profile, you can find the "Capabilities" section under the "Summary" tab. There you just enter your tag in the text box. The capability tag will be applied to all networks in the network profile. 
{{<image src="capability-tag-network-profile.png" linked="true">}}

You can also create a tag  under "Infrastructure" and "Tags" then search and select an existing tag to add as a capability tag. 
{{<image src="existing-tag-as-capability-tag.png" linked="true">}}

If you delete a tag completely from "Infrastructure" and "Tags", you're deleting a tag completely from vRA. Therefore, that tag will be removed from all resources to which it was attached. 

### Objective 4.21 - Configure Multi-Tenancy
With the release of vRA 8.1, support for dedicated infrastructure multi-tenancy became available. 

To enable a multi-organization configuration, you need to configure external Workspace ONE Access for multi-tenancy use. Tenancy can be enabled in vRSLCM after vRA is installed and configured. You then use vRSLCM to create and configure tenants. 

Enabling tenancy creates a new Provider organization by default, and the Provider Admin can create new tenants, add tenant admins, setup directory synchronization, and add users. Tenant admins have control over their tenant; they can control directory synchronization, add users, configure policies, access content and provisioned resources, and more within their tenant. 

By default, multi-tenancy is enabled based on tenant hostname so unlike previous vRA versions (7.x) where users accessed tenants with URLs based on directory path, users now will access tenants based on hostname. 

For a step-by-step process on how to set up multi-organization tenancy for vRA, please refer to the official documentation [here][vRA8-multi-tenancy-setup-official-doc-link].



[vRA8-onboarding-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-B8037EFB-27A9-4351-A528-E3FFFB5DD586.html
[vRA8-extensibility-terms-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-86CF0C87-6462-4E64-8DE6-85ECD1353121.html
[vRA8-multi-tenancy-setup-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Administering/GUID-1D0D01DB-F19C-4328-B24C-9CA4C58CDCE4.html
