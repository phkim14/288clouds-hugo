# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 7 (Objectives 7.17-7.21)


This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 7 - Administrative and Operational Tasks

### Objective 7.17 - Create Service Broker Content Sources
A content source defines the origin of importing blueprints (now called templates) into the service broker catalog. 

Imported blueprints are refreshed every six hours; changes to blueprints in external sources will be reflected after a refresh. 

To add a content source, you go to "Content & Policies" then "Content Sources" and click "+ NEW". 
{{<image src="add-content-source.png" linked="true">}} 

You select the type of the content source you are trying to add. It can be an extensibility action, vRO workflow, AWS CloudFormation template, Marketplace VM template, Code Stream pipeline, or VMware Cloud template. 
{{<image src="add-content-source-2.png" linked="true">}} 

You need to provide details to finish adding the content source. You would provide different details depending on the type of content source you are trying to add. 

Below is an example of required details if you choose VMware Cloud templates as a content source. 
{{<image src="add-content-source-vmware-cloud-templates.png" linked="true">}} 

Below is an example of required details if you choose AWS CloudFormation templates as a content source. 
{{<image src="add-content-source-aws-cloudformation.png" linked="true">}} 

If you delete a content source, the imported items are deleted from the catalog, except for Cloud Assembly blueprints. 


### Objective 7.18 - Configure Content Sharing
You use content sharing to make the imported blueprints available to project members. 

To configure content sharing, you go to "Content & Policies" then "Content Sharing". Then you select the project that you want to share items with. 
{{<image src="content-sharing.png" linked="true">}} 

After selecting the project, click "+ ADD ITEMS" to select the content you want to share with the project members. You can choose to share content sources or specific content from a list of all content. 
* If you share a content source and new blueprints are added to that content source, the new blueprints will be available in the catalog for the project members automatically.
* If you share specific content from a list of all content instead of a content source, new blueprints will not be available in the catalog. You would have to individually add new blueprints to be shared if you want them to be available for the project members.
{{<image src="content-sharing-2.png" linked="true">}} 


### Objective 7.19 - Create and Manage Custom Forms
Sevice Broker administrators can create custom forms to customize the request forms for imported blueprints (now called templates).

To create a custom form for an imported template, you go to "Content & Policies" then "Content" and select "Customize form" for a specific template. 
{{<image src="create-custom-form.png" linked="true">}} 

Similar to the blueprint canvas in vRA Cloud Assembly, you drag and drop elements to the design canvas then use the properties pane on the right side to configure each element. 
{{<image src="create-custom-form-2.png" linked="true">}} 

You can import and export a custom form in JSON or YAML format. 
{{<image src="create-custom-form-3.png" linked="true">}} 

Since there are so many elements and properties per element that you can explore, I'm not going to cover all the details. You should explore this and experiment with making custom forms in a lab environment. 

You can find more information on using custom forms in the official documentation [here][vRA8-custom-form-official-doc-link].


### Objective 7.20 - Manage Policies
You use policies to apply rules or parameters to new or current deployments to enable governance and process your deployments.

You can audit when policies were applied from the policy enforcement list. The events remain in this list for 100 days. 
{{<image src="enforcement.png" linked="true">}} 

You can create a policy by going to "Content & Policies" then "Definitions" and click "+ NEW POLICY". 
{{<image src="add-policy.png" linked="true">}} 

You select a policy type that you want to create:
* Approval Policy
* Day 2 Actions Policy 
* Lease Policy

#### Approval Policy
An approval policy defines when approvals are required and who must approve the request before the request is provisioned.

<b><u>Scope</u></b><br>
The scope determines whether the policy will be applied to all deployments in the organization or to deployments in a selected project. 

<b><u>Deployment Criteria</u></b><br>
You can provide deployment criteria if you want to refine the deployments to which the policy will be applied. 

Deployment criteria format is as follows: clause + operator + value
* Clause options: cloudTemplate, catalogItem, deployment, ownedBy, requestedBy, resources
* Operator options (depends on selected clause): equals, not equal to, has any

Below is an example where a specific project has been selected for scope and deployment criteria has been added where this approval policy will only be applied to deployments requested by a user, phoebek (me). 
{{<image src="approval-policy.png" linked="true">}} 

<b><u>Approver Mode</u></b><br>
You can choose "Any" or "All" for the Approver mode.
* Any - only one approver needs to approve for the request to be processed
* All - all the listed approvers must respond with the same response for the request to be processed; if at least one approver rejects the request, the request will be denied 

<b><u>Approvers</u></b><br>
You can add one or more users as approvers. All approvers must have at least organization members and the Service Broker user roles. 

The approval request will be sent to all the approvers simultaneously. 

<b><u>Auto Expiry Decision & Trigger</u></b><br>
You can choose to automatically approve or reject a request after a specific number of days has passed. 

You set the decision to approve or reject by configuring the Auto expiry decision field. You set the number of days in the Auto expiry trigger field. The max number of days you can set is 7 days. 

<b><u>Actions</u></b><br>
You select one or more actions that this policy applies to. You can find actions by entering the deployment or resource type in the search box. You can use an asterisk as a wildcard after a string as well. 
{{<image src="approval-policy-2.png" linked="true">}} 

#### Day 2 Actions Policy
A day 2 actions policy controls what group of users can run a selected list of actions on deployed resources. 

<b><u>Enforcement Type</u></b><br>
You can choose this day 2 actions policy enforcement type to be either hard or soft. 

Hard policies will be ranked higher than soft policies so soft policies can be overridden by hard policies. 

<b><u>Role</u></b><br>
Role entitles the members of the selected role, within the selected scope and deployment criteria, to be able to run the selected actions. 

The role can be a project administrator, a project member, or a named custom role. 
{{<image src="day2-action-policy.png" linked="true">}} 

#### Lease Policy
A lease policy controls how long the provisioned workloads are active before they are destroyed and resources can be reclaimed. 

<b><u>Maximum Lease (days)</u></b><br>
You can set the maximum number of days a deployment can exist without being renewed. The lease begins when the deployment is created. 

<b><u>Maximum Total Lease (days)</u></b><br>
You can set the maximum number of days a deployment can exist, including lease renewals. Users can renew leases until the deployment reaches the maximum total lease day, then the deployment is destroyed and resources are reclaimed. 

<b><u>Grace Period (days)</u></b><br>
You can set the number of days after a lease expires before it is destroyed. It gives an opportunity for users to extend the lease for another block of time, as long as it does not exceed the maximum Total Lease value. 
{{<image src="lease-policy.png" linked="true">}} 


### Objective 7.21 - Manage Notifications
A cloud administrator can configure an email server to send email notifications to users about events in vRA Service Broker and vRA Cloud Assembly. The email server is only for outbound messages.

If you want to set up an email server for notifications, you go to "Content & Policies" then "Email Servers" under "Notifications". 

Unfortunately, I did not have access to this in the lab environment to take screenshots, but for more details, please refer to the official documentation [here][vRA8-service-broker-notification-official-doc-link].


[kubernetes-architecture-official-doc-link]: https://kubernetes.io/docs/concepts/overview/components/
[vRA8-custom-form-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Service-Broker/GUID-A152CC03-D0DC-41C1-9ADA-A386983CA548.html
[vRA8-service-broker-notification-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Service-Broker/GUID-9973D5B2-9C42-448D-B2DA-E2F781292A9D.html
