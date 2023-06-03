# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 7 (Objectives 7.8-7.14)


This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 7 - Administrative and Operational Tasks

### Objective 7.8 - Manage Storage Profiles
Storage profiles are organized by cloud accounts.
{{<image src="storage-profiles.png" linked="true">}}

You can click "BROWSE" to view all the storage policies related to the cloud account. 
You can click "DELETE" to delete all the storage profiles related to the cloud account. This does not delete the cloud account itself. 
{{<image src="storage-profiles-3.png" linked="true">}}

If you click into a specific cloud account, you can view the storage profiles related to that cloud account. You can edit or delete a specific storage profile from here. You can choose to create a new storage profile for that cloud account here as well. 
{{<image src="storage-profiles-2.png" linked="true">}}


### Objective 7.9 - Manage Network Profiles
You can view all the network profiles by going to "Infrastructure" then "Network Profiles". For each network profile, you can see some basic information about the network profile such as:
* Account/region
* Capability tags
* Network policies
* Number of existing networks
* Number of public networks
* Number of load balancers
* Number of security groups
{{<image src="network-profiles.png" linked="true">}}

You can click "OPEN" to view and edit the network profile or click "DELETE" to delete the network profile.
{{<image src="network-profiles-2.png" linked="true">}}

If you delete a network profile, it may break blueprints that are relying on that network profile. 
For example, I deleted a network profile that one of my blueprints use so when I tried deploying that blueprint, the deployment failed with an error saying that the network profile to match the blueprint constraints could not be found.
{{<image src="network-profile-fail.png" linked="true">}}


### Objective 7.10 - Create and Manage Blueprints
To create a new blueprint (now called template), you go to "Design" and "Cloud Templates" then select "NEW FROM Blank canvas". 
{{<image src="create-blueprint.png" linked="true">}}

You then give the blueprint a name and a description and also select the project for this blueprint. You can choose to share the blueprint only with this project in Service Broker or allow an admin to share the blueprint with any project in the organization.
{{<image src="create-blueprint-2.png" linked="true">}}

The components pane is on the left, and it lists all the different components that you can drag and drop onto the design canvas. When you add an element to the design canvas, YAML code corresponding to that element will appear in the the YAML pane on the right. If you choose to write the YAML code first, the corresponding element will appear on the design canvas. 
{{<image src="create-blueprint-3.png" linked="true">}}

The YAML pane editor will help you with syntax and options. A red exclamation mark shows that you have a syntax error, and a yellow light bulb shows that you have some more options available to customize that element. 
{{<image src="create-blueprint-4.png" linked="true">}}

If you don't want to customize the properties of an element using the YAML code, you can also select the element and edit its properties from the "Properties" tab. 
{{<image src="create-blueprint-7.png" linked="true">}}

You can also create inputs by going to the "Input" tab instead of creating them in YAML code. 
{{<image src="blueprint-inputs.png" linked="true">}}

When you're done with creating the blueprint, you can click "TEST". This functionality tests the syntax, placement, and basic validity of the blueprint. 
{{<image src="create-blueprint-5.png" linked="true">}}

After a successful test result, you can deploy the blueprint by clicking "DEPLOY". You would provide the deployment name if this is a new deployment. 
{{<image src="create-blueprint-6.png" linked="true">}}

After a blueprint is created, you can clone it to make another blueprint or download the YAML code of the blueprint. You can also deploy the blueprint from this view, not just from the blueprint canvas view.
{{<image src="manage-blueprints.png" linked="true">}}


### Objective 7.11 - Create and Manage Blueprint Versions
You can create and manage different versions of the same blueprint.

To create a new version of a blueprint, click "VERSION" in the blueprint. 
{{<image src="blueprint-version.png" linked="true">}}

You should enter a description of the major objectives of the changes for this version in the "Description" box and also provide a detailed list of changes done in the version in the "Change Log" box. If you want to make this version of the blueprint available to the Service Catalog, you can select the checkbox next to "Release this version to the catalog". The best practice would be to only release a version of a blueprint to a catalog after you test the version with actual deployments.
{{<image src="blueprint-version-2.png" linked="true">}}

If you have more than one versions of the blueprint, you can view the versions by clicking on "VERSION HISTORY". 
{{<image src="blueprint-version-history.png" linked="true">}}

From the version history view, you can see all the versions of the blueprint. 
{{<image src="blueprint-version-history-view.png" linked="true">}}

You can view the differences in YAML code between versions.
{{<image src="blueprint-version-history-diff-code.png" linked="true">}}

You can view the differences between versions visually as well.
{{<image src="blueprint-version-history-diff-visual.png" linked="true">}}


### Objective 7.12 - Manage Extensibility/Subscription
Subscriptions enable the use of ABX actions or vRO workflows through the selected event topic. 

To create a new subscription, you go to "Extensibility" then "Subscriptions" and click "+ NEW SUBSCRIPTION". 
{{<image src="new-subscription.png" linked="true">}}

A new subscription being created is enabled by default, but you can change that by toggling the "Status". You can only choose one event topic per subscription. 
{{<image src="create-subscription.png" linked="true">}}

You can create custom conditions with JavaScript syntax boolean expressions using the "event" object and its properties in order to filter events in an event topic. 
{{<image src="create-subscription-2.png" linked="true">}}

You select an ABX action or a vRO workflow to run for this subscription. You can only choose one action or workflow to run per subscription. If you want multiple actions or workflows to run, you need to create multiple subscriptions and assign them to the same blueprint and event.
{{<image src="create-subscription-3.png" linked="true">}}

If you have multiple subscriptions for the same event, you can use the "Blocking" flag to prioritize which items should run first. If you mark a subscription as "Blocking", it will block any other subscriptions of this event topic and condition until this action/workflow has completed. You give the blocking subscription a priority value to define the order of execution of blocking subscriptions on an event topic. Lower number means higher priority. You can choose from a priority value of 0-10. 10 is the lowest and the default value. You can set a timeout for the blocking subscription to define how long to wait until the action/workflow fails. You can also set a recover action/workflow, which would run if the primary action/workflow fails for a blocking subscription.
{{<image src="create-subscription-4.png" linked="true">}}

You can set the "Subscription scope" to any projects or specific projects. 
{{<image src="create-subscription-5.png" linked="true">}}


### Objective 7.13 - Deploy Catalog Items
Your cloud administrator determines your access to different catalog items and where you can deploy these items. 

Catalog items can be vRA Cloud Assembly blueprints, AWS CloudFormation templates, vRO workflows, extensibility actions, VMware marketplace templates, vRA Code Stream pipelines, and more.

To deploy a catalog item, you go to vRA Service Broker and under "Catalog", you can view all the catalog items that are availalbe for you. Once you find the catalog item you want to deploy, click "REQUEST".
{{<image src="deploy-catalog-item.png" linked="true">}} 

Then you enter the request details and click "SUBMIT".
{{<image src="deploy-catalog-item-2.png" linked="true">}} 


### Objective 7.14 - Manage Deployments
You can update existing deployments by updating/editing existing blueprints and applying them to the existing deployments. 

After you update a blueprint, you can click "DEPLOY" then choose "Update an existing deployment". Then from the availalbe deployments, you choose which deployment you want to update. Only one deployment can be updated at a time. 
{{<image src="update-existing-deployment.png" linked="true">}}

You can also perform day 2 actions on existing deployments depending on the day 2 actions available to you. 
{{<image src="deployment-day2-actions.png" linked="true">}}
