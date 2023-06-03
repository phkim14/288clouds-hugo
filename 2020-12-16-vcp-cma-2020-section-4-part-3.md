# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 4 (Objectives 4.15-4.16)


This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

Note that the screenshots in this study guide are from a vRA cloud environment, not an on-prem vRA 8.1 environment. There should not be a significant difference between the two at the time of taking these screenshots.

## Section 4 - Installing, Configuring, and Setup

### Objective 4.15 - Describe the Different Out of the Box Integrations Available with vRA
There are various out-of-the-box (OOTB) integrations available with vRA, such as vRealize Orchestrator, configuration management systems, IPAM providers, and more.
{{<image src="integration-types.png" linked="true">}}

For more details on vRA integrations with external systems, please refer to the official documentation [here][vRA8-integrations-official-doc-link]. 

#### Git Integration
vRA supports integration with GitLab, GitHub, and BitBucket repositories for you to manage blueprints and action scripts under source control. Each repository is a separate integration. 

In order to create an integration with GitLab or GitHub, go to "Infrastructure" then "Integrations" and click "+ ADD INTEGRATION". You will need the server URL and token for the target repository.
{{<image src="github-integration.png" linked="true">}}

Once the connection is established, you configure the connection to accept blueprints in an appropriate repository by adding projects. You go to "Infrastructure" then "Integrations" then find the Git connection you've created. Then under the "Projects" tab, click "+ ADD PROJECT". Once you select a project, you then configure a repository to be used for the project.
{{<image src="github-integration-add-project-repository.png" linked="true">}}

You can save actions and version action scripts directly to a Git repository from vRA Cloud Assembly. With blueprints, you cannot push new or updated blueprints to a Git repository from vRA Cloud Assembly. To update a blueprint or add new blueprints to a repository, you must do this from the Git interface then import/sync the blueprints. 

#### External IPAM Integration
You can create a provider-specific external IPAM integration point to use a third-party IPAM solution to manage the IP addresses used in vRA deployments. 

To create an IPAM integration, you go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "IPAM". Then you select the IPAM provider you want to use. If the IPAM provider you want to use is not available, you can click "MANAGE IPAM PROVIDERS" and import a provider-specific integration package (.zip file) from the IPAM provider's website, VMware solutions exchange marketplace, or from vRA Cloud Assembly "Marketplace" tab if available. 
{{<image src="ipam-integration-providers.png" linked="true">}}

After selecting the IPAM provider, you need to provide more details as shown in the screenshot below. Running environment supports communication between vRA and the IPAM provider. You can use AWS or Azure cloud account or an actions-based extensibility (ABX) On-Prem Embedded integration point as a running environment. Note that if you use an AWS or Azure cloud account as a running environment, the IPAM provider appliance must be accessible from the Internet and have a publicly resolvable DNS name. 
{{<image src="ipam-integration-details.png" linked="true">}}

#### MyVMware Integration
You can integrate with MyVMware to support VMware related actions and capabilities with blueprints, such as accessing the VMware Marketplace. 

To add a MyVMware integration, you go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "My VMware". All you have to provide are your MyVMware credentials (username and password).

#### vRealize Orchestrator Integration
vRealize Automation has a preconfigured vRealize Orchestrator (vRO) instance that can be used for extensibility subscriptions. But with this vRO integration, you can add one or more external vRO instances and use the included workflow library in extensibility subscriptions. 

To add a vRO integration, go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "vRealize Orchestrator". You need to provide the vRO URL, a cloud proxy, as well as vRO credentials.
{{<image src="vro-integration.png" linked="true">}}

Once the vRO integration is configured, the workflows should be added. You can see them by going to "Extensibility" then "Workflows". 
{{<image src="vro-integration-workflows.png" linked="true">}}

#### Kubernetes Integration
There are various methods to manage and deploy Kubernetes resources with vRA Cloud Assembly:
1. Pivotal Container Service (PKS) integration
2. Red Hat OpenShift integration
3. vSphere Project Pacific capabilities
4. Extensibility (Event Topics) for Kubernetes cluster deployment

Sections below provide a high-level overview of each method, but for more details, please refer to the official documentation [here][vRA8-kubernetes-integration-official-doc-link].

<b><u>PKS Integration</b></u><br>
PKS integration allows you to manage PKS instances and Kubernetes clusters provisioned by PKS both on-prem and in the cloud. Before adding the integration, you need to have a configured PKS server setup with UAA authentication. 

After the PKS integration is created, you can create Kubernetes zones or discover external Kubernetes clusters and assign them to a project under the "Kubernetes Provisioning" tab when creating or editing a project. You can also add or create Kubernetes namespaces to manage clusters. 
{{<image src="kubernetes-project.png" linked="true">}}

<b><u>OpenShift Integration</b></u><br>
You can configure a Red Hat OpenShift integration with vRA Cloud Assembly to support Kubernetes, similar to a PKS integration. vRA Cloud Assembly supports OpenShift versions 3.x (at the time of writing this blogpost). 

Before you add an OpenShift integration, you should already have a configured Red Hat OpenShift implementation. To add an OpenShift integration, you can go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "Red Hat OpenShift". Here, you need to provide the URL of the OpenShift endpoint and appropriate credentials.
{{<image src="openshift-integration.png" linked="true">}}

Once an OpenShift integration is created, you can create Kubernetes zones and assign them to a project like you would after a PKS integration. 

<b><u>vSphere Project Pacific</b></u><br>
With vSphere Project Pacific, you can utilize supervisor clusters and create or add namespaces to provide selective access to Kubernetes resources within existing vRA projects. 

Supervisor clusters are customized Kubernetes clusters associated with existing vSphere environment. To add a supervisor cluster, you go to "Infrastructure" then "Kubernetes" and under "Supervisor Clusters", you can select "ADD SUPERVISOR CLUSTER". 
{{<image src="project-pacific-supervisor-cluster.png" linked="true">}}

Supervisor namespaces are used to provide access control to Kubernetes resources, and you can create multiple namespaces for each supervisor cluster. You can create or add supervisor namespaces under the tab "Supervisor Namespaces". Once you select a project while creating a supervisor namespace, you can view users and groups that have access to the selected project under the "Users" tab. 
{{<image src="project-pacific-supervisor-namespace.png" linked="true">}}

<b><u>Extensibility for Kubernetes</b></u><br>
vRA Cloud Assembly provides OOTB set of event topics that provide actions related to Kubernetes cluster deployment. To view these event topics, you go to "Extensibility" then "Event Topics" and you can filter the list for "Kubernetes". 
{{<image src="kubernetes-event-topics.png" linked="true">}}

You can view details on what the event topic does by clicking on the event topic. 
{{<image src="kubernetes-event-topic-sample.png" linked="true">}}

#### Puppet Enterprise Integration
To use Puppet for configuration management with vRA Cloud Assembly, you need an instance of Puppet Enterprise installed and a connection between this Puppet instance and vRA Cloud Assembly instance.

With the Puppet integration, the Puppet provider installs, configures, and runs the Puppet agent on a deployed compute resource. The Puppet provider supports SSH and WinRM connections. The deployed virtual machines are assigned to the Puppet Master and when the VMs are decommissioned, appropriate users can de-registered those VMs from the Puppet Master. 

To create a Puppet integration, you go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "Puppet". You need to provide credentials to the Puppet Enterprise instance you would like to connect to vRA Cloud Assembly. 
{{<image src="puppet-integration.png" linked="true">}}

By default, when you add a Puppet integration, it is available to all the projects, but you can restrict it to specific projects. You can add a Puppet component into a blueprint by just dragging and dropping the Puppet component then configuring the Puppet Properties on the right pane. 
{{<image src="puppet-integration-blueprint.png" linked="true">}}

#### Ansible Integration
Besides Puppet, you can also use Ansible Open Source or Ansible Tower integrations with vRA Cloud Assembly for configuration management.

<b><u>Ansible OpenSource Integration</b></u><br>
To create an integration with Ansible Open Source, you go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "Ansible". 

You need to provide the hostname of the control machine on which your Ansible Open Source instance resides as well as the absolute file path for the Ansible INI inventory file for use with this integration. If the inventory file does not exist at the specified path, a new inventory will be created. vRA Cloud Assembly manages the Ansible inventory file so make sure that vRA Cloud Assembly user has rwx access on the inventory file. You also need to provide appropriate Ansible credentials. 
{{<image src="ansible-integration.png" linked="true">}}

When you integrate Ansible Open Source with vRA Cloud Assembly, you can run one or more Ansible playbooks in a specified order when new machines are provsioned to automate configuration management. You specify the playbooks in the blueprint. You can add an Ansible component into a blueprint by dragging and dropping the Ansible component on the blueprint canvas and configuring the properties.
{{<image src="ansible-integration-blueprint.png" linked="true">}}

For machines provisioned on public clouds (i.e. AWS, Azure, GCP), the machine's public IP address is used as the value for the address property if the machine is connected to a public network. If the machine is not on a public network, then the Ansible OpenSource integration looks for the IP address from the network attached to the machine. If there are multiple networks attached to the machine, Ansible OpenSource integration uses the network with the least index of the NIC attached to the machine. If the index is not provided, then Ansible OpenSource integration uses the first network attached to the machine.

For more details on the Ansible OpenSource integration pre-requisites and procedure, please refer to the official documentation [here][vRA8-ansible-integration-official-doc-link].

<b><u>Ansible Tower Integration</b></u><br>
vRA Cloud Assembly supports integration with Ansible Tower versions 3.5 and 3.6 (at the time of writing this blogpost). 

With the Ansible Tower integration, templates define the inventory and playbook for use with a deployment, and there is one-to-one mapping between a job template and a playbook. You can add job templates through the Ansible Tower UI. You can also view the execution of Job templates invoked by vRA Cloud Assembly from the Ansible Tower UI. 

To create an integration with Ansible Tower, you go through the same process as creating an integration with Ansible but make sure to select "Ansible Tower" instead of "Ansible".

Similar to creating an Ansible integration, you need to provide the Ansible Tower hostname and credentials.
{{<image src="ansible-tower-integration.png" linked="true">}}

You can also add an Ansible Tower component into a blueprint, same as you've done for an Ansible component. 

For more details on the Ansible Tower integration pre-requisites and procedure, please refer to the official documentation [here][vRA8-ansible-tower-integration-official-doc-link].

#### Active Directory Integration
vRA Cloud Assembly supports OOTB integration with Active Directory servers. This integration allows you to provide creation of computer accounts in a specified Organizational Unit (OU) within an AD server before machine povisioning. The OU must be created before associating the AD integration with a vRA Cloud Assembly project. AD integration requires an LDAP connection to the AD server. 

To create an AD integration,  you go to "Infrastructure" then "Integrations", click "+ ADD INTEGRATION" and select "Active Directory". You need to provide the name of the Active Directory server, select a running environment, and enter appropriate credentials as well as base DN (a comma separated list of values that define a path in the AD). 

For the running environment, if you want to configure an AD integration with an on-prem vCenter, you need an ABX integration with an extensibility cloud proxy. If you want to configure an AD integration in the cloud, you need an Azure or AWS account. 
{{<image src="ad-integration.png" linked="true">}}

For more details on the Active Directory integration, please refer to the official documentation [here][vRA8-ad-integration-official-doc-link].

### Objective 4.16 - Integrate vRA with vRealize Operations
You can integrate vRealize Automation with vRealize Operations (vROPs) Manager for advanced workload placement, deployment monitoring, and pricing cards.

This integration must be between either both on-prem or both cloud instances of vRealize Automation and vRealize Operations. For example, you cannot integrate an on-prem vRA instance with a cloud vROPs instance. 

For more details on integrating vRA with vRealize Operations, please refer to the official documentation [here][vRA8-vrops-integration-official-doc-link].

<b><u>Advanced Workload Placement</b></u><br>
This only works on Distributed Resource Scheduler (DRS) enabled clusters of a vSphere cloud zone. 

When a machine needs to be provisioned, vRA first follows its application intent model and considers related constraints (i.e. tag-based, project membership, associated cloud zones, affinity filters, etc.) to find target resources for placement. Then out of the target resources eligible for placement given by vRA, vROPs Manager considers past and future workloads as well as what-if predictions to select which target resource is the most optimal for provisioning. 

vROPs Manager does not support workload placement on resource pools in a vSphere environment. 

For more details on advanced workload placement by the vROPs integration, please refer to the official documentation [here][vRA8-vrops-integration-adv-workload-placement-official-doc-link].

<b><u>Deployment Monitoring</b></u><br>
vROPs Manager can monitor vRA deployments with its metrics after the vROPs integration is created.

When monitoring is enabled, vRA retrieves vROPs metrics about the deployments and you can view these under "Monitor" tab for each deployment. 

Only vSphere virtual machine metrics (CPU, memory, storage IOPS, network MBPS) appear for the deployments. At this time, metrics from other components (i.e. vCloud Director, XaaS) or public cloud providers (i.e. AWS, Azure) are not supported. 

For more details on deployment monitoring by the vROPs integration, please refer to the official documentation [here][vRA8-vrops-integration-deployment-monitoring-official-doc-link].

<b><u>Pricing Cards</b></u><br>
vRA Cloud Assembly pricing cards are used to analyze financial impact of each vRA deployment. Pricing cards define the rates for a pricing policy then the pricing policy can be assigned to specific projects to define a total price.

A vROPs integration is required before you are able to create pricing cards in vRA Cloud Assembly. When a vROPs integration is created, a default rate card is available to use, and all pricing cards that are created are applied to projects by default. You can choose to create pricing cards that apply to projects only or cloud zones. 

To create pricing cards, you go to "Infrastructure" then "Pricing Cards" and click "+ NEW PRICING CARD". 
{{<image src="vrops-integration-pricing-cards.png" linked="true">}}

Unfortunately, I did not have access to a vROPs cloud integration at the time of writing this post so please refer to the official documentation [here][vRA8-vrops-integration-pricing-cards-official-doc-link] for more details on pricing cards.


[vRA8-integrations-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-8D943F7A-61BE-4094-84B6-4F3807B01C21.html
[vRA8-ansible-integration-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-9244FFDE-2039-48F6-9CB1-93508FCAFA75.html
[vRA8-ansible-tower-integration-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-2C88F1B8-EBF5-463F-9C21-D164681459F0.html
[vRA8-ad-integration-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-011A34E3-9548-4180-8171-16AEDA27089A.html
[vRA8-kubernetes-integration-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-081EA313-129F-4098-B4CC-587A42E7BFFF.html
[vRA8-vrops-integration-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-0C20F25F-EB98-4335-9D8C-4C16BB847C37.html
[vRA8-vrops-integration-adv-workload-placement-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-1F900EE2-B93B-4C78-B02B-2E0D94FFA232.html
[vRA8-vrops-integration-deployment-monitoring-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-3E1FFCE4-E00A-4A51-83EF-CC61209DDDAF.html
[vRA8-vrops-integration-pricing-cards-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-Cloud-Assembly/GUID-376DBE2D-CDC1-4D90-8A6E-C0CCC61621B7.html#GUID-376DBE2D-CDC1-4D90-8A6E-C0CCC61621B7
