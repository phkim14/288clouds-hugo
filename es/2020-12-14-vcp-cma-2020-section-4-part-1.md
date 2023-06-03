# VCP-CMA 2020 (2V0-31.20) Exam Study Guide - Section 4 (Objectives 4.1-4.7)

# ~ Este post será traducido al español próximamente ~

This study guide aligns with the exam blueprint for the Professional VMware vRealize Automation 8.1 exam (2V0-31.20), which leads to the VMware Certified Professional - Cloud Management and Automation 2020 certification. The exam is 135 minutes with 70 questions. 

This study guide has been created to help myself study for the exam, but I hope you find this helpful in your own preparation for the exam. If you want official training to prepare for the exam, please refer to VMware Learning. 

## Section 4 - Installing, Configuring, and Setup

### Objective 4.1 - Describe the Different Types of vRealize Automation deployments 
There are 2 types of deployments: standard and clustered.

<b><u>Standard Deployment Architecture</b></u>
{{<image src="standard-deployment.png" linked="true">}}
Using the vRealize Easy Installer, the vRealize Suite Lifecycle Manager (vRSLCM) is deployed first and then vRSLCM is used to deploy VMware identity manager and vRealize Automation. 

<b><u>Clustered Deployment Architecture</b></u>
{{<image src="clustered-deployment.png" linked="true">}}
Same as the standard deployment, vRSLCM is deployed first then using vRSLCM, multiple VMware identity managers and vRealize Automation appliances can be deployed for a clustered deployment architecture. VMware NSX-T, F5 BIG IP, or Citrix NetScaler can be used as a load balancer for the vRealize Automation appliances. 


### Objective 4.2 - Prepare the Pre-requisites for an Installation (DNS, NTP, Service Accounts, etc.)
Please refer to the official documentation for more details on the pre-requisites [here][vRA8.1-easy-installer-official-doc-link].

<b><u>System Resources Requirements</b></u>
{{<image src="system-resource-requirements.png" linked="true">}}
{{<image src="system-resource-requirements-2.png" linked="true">}}

<b><u>Network Requirements</b></u><br>
vRealize Automation needs the following:
* Single, static IPv4 and network address
* Reachable DNS server
* Valid FQDN (should be set so that it can be resolved both forward and in reverse through the DNS server)
* Reachable NTP server

Note that the IP address and the hostname cannot be changed after installation. 


### Objective 4.3 - Perform a Standard Deployment using vRealize Easy Installer
The vRealize Easy Installer helps you install vRealize Suite Lifecycle Manager (vRSLCM), VMware Identity Manager (vIDM), and vRealize Automation (vRA) from a single console. The Easy Installer also registers vRealize Automation with VMware Identity Manager.  

<b><u>High-Level Deployment Procedure</b></u><br>
1. Download the vRealize Easy Installer ISO file.
2. Run the installer file to start the Installation Wizard. Note that the location of this installer file depends on the OS of the local system (linux, macOS, or Windows) to which the ISO file has been mounted.
3. Click "Install" to start the deployment.
4. Follow instructions and provide necessary details as requested by the Installation Wizard:
* vCenter IP or FQDN with administrator credentials
* Inventory location, compute resource, datastore, and network configuration details for appliances to be deployed
* Common password for all the products (vRSLCM, vIDM, vRA)
* vRSLCM VM IP and hostname details
* vIDM VM IP and hostname details
* vRA VM IP and hostname details
5. Monitor the installation progress from the Installation Wizard while vRSLCM is deployed.
6. Once vRSLCM is deployed, login to the vRSLCM and monitor the installation progress of vIDM and vRA from the vRSLCM UI. 

Please refer to the official documentation for more details on the step-by-step process [here][vRA8.1-easy-installer-official-doc-link].


### Objective 4.4 - Configure vRealize Automation using Quickstart
After vRA is installed, you can use the Quickstart wizard to help you get started using Cloud Assembly and Service Broker right away. 

The vCenter Server Quickstart will walk you through the following tasks in Cloud Assembly and Service Broker:
* Add a vCenter Server cloud account
* Add a NSX (V or T) cloud account and associate it with the vCenter Server account
* Add a cloud account region - select the datacenter you'd like to use
* Create a sample machine blueprint that you can use to deploy 
* Create a project (Quickstart project is created for you)
* Create lease and machine naming policies (these will be associated with the Quickstart project)
* Add the templates to the catalog
* Deploy a machine from the catalog

Note that you can only run the Quickstart once. 

Please refer to the official documentation to see a step-by-step procedure of the vCenter Server Quickstart [here][vRA8.1-quickstart-official-doc-link].


### Objective 4.5 - Perform Manual Installation using Lifecycle Manager
You can install vRealize Automation without using the vRealize Easy Installer. Instead, you can manually deploy vRA through the vRSLCM UI.
The procedure below assumes that you have deployed vRSLCM and vIDM through the vRealize Easy Installer already. 

<b><u>Step-by-Step Procedure</b></u><br>
1. Login to vRSLCM UI and navigate to "Lifecycle Operations".
2. Click on "Create Environment".
3. Provide the necessary details in all the fields. For the password, if you don't have any passwords configured in vRSLCM, you can click the plus icon on the right to configure a password then select it.
{{<image src="create-environment.png" linked="true">}}
4. Select vRealize Automation as the product to install. 
{{<image src="create-environment-2.png" linked="true">}}
5. Read the EULA and agree.
6. Select a license key then click "Validate Association" to make sure the license is valid. Add the license first if needed.
7. Select a certificate. Add the certificate first if needed.
{{<image src="create-environment-3.png" linked="true">}}
8. Provide infrastructure details.
{{<image src="create-environment-4.png" linked="true">}}
9. Provide network details for the vRA appliance. 
{{<image src="create-environment-5.png" linked="true">}}
10. Provide FQDN and IP address for the vRA appliance.
{{<image src="create-environment-6.png" linked="true">}}
11. Run precheck.
12. Review summary and click "Submit" to start the deployment.
{{<image src="create-environment-7.png" linked="true">}}

You can monitor the progress from the "Request Details" page.
{{<image src="request-details.png" linked="true">}}


### Objective 4.6 - Configure Identity Sources
VMware Identity Manager is based on the OAuth 2.0 authorization framework and is used to manage user authentication, access policies, and user entitlements to resources. VMware Identity Manager UI can be accessed at https://<VMware_Identity_Manager_FQDN>/SAAS/admin.

You can sync users and groups from your enterprise directory to the vIDM service. You can choose to sync nested group members when synchronizing Active Directory users to vIDM. You can set up a sync schedule to sync regularly or start a manual sync at any time. 

The following directory types are supported:
* Active Directory over LDAP: use this if you are going to connect to a single Active Directory domain environment
* Active Directory, Integrated Windows Authentication: use this if you are going to connect to a multidomain or multiforest Active Directory environment
* OpenLDAP: use this for OpenLDAP directory 

You can also create multiple local directories. 
{{<image src="directory-integration.png" linked="true">}}

If you want to modify or customize user attributes, you can configure them at the "User Attributes" tab under "Identity & Access Management" before creating a directory. 
{{<image src="user-attributes.png" linked="true">}}


### Objective 4.7 - Configure Identity and Access Management
Roles define a set of privileges that specifies which tasks that users associated with the roles can perform. A user can have multiple roles. In vRA, there are 2 types of roles: 
* Organization roles: These are defined at the topmost vRA level. There are two organization roles - organization owner and organization member. An organization owner has access to Services, Identity & Access Management, and Branding tabs. The main responsibility of an organization owner is to assign roles to the organization members. An organization member only has access to the Services tab.
* Service roles: These roles define user access to different services available with vRA, including Cloud Assembly, Code Stream, Orchestrator, and Service Broker. Each service has multiple roles available. 

Only one organization can exist in a vRealize Automation deployment. Every vRA user given a service role must first be associated with an organization role. 

As an organization owner, you can navigate to the "Identity & Access Management" tab from the vRA Cloud Services Console and manage acive users and enterprise groups.
{{<image src="identity-access-mgmt-vra.png" linked="true">}}

You can assign and edit roles for a specific user from the "Identity & Access Management" tab by selecting a user then clicking "Edit Roles". You can assign multiple service roles to one user. 
{{<image src="vra-roles.png" linked="true">}}

<b><u>Cloud Assembly Service Roles</b></u><br>
There are 3 roles for the Cloud Assembly service:
1. Cloud Assembly Administrator
* Create cloud accounts and cloud zones
* Create and manage projects
* Create and manage flavor and image mappings
* Configure and manage network and storage profiles
* Create and manage tags
2. Cloud Assembly User
* Create blueprints
* Deploy machines and services
* Manage deployments
3. Cloud Assembly Viewer
* Read only

For more details on each Cloud Assembly service role, refer to the official documentation [here][vRA8.1-cloud-assembly-service-broker-roles-official-doc-link]. 

<b><u>Service Broker Service Roles</b></u><br>
There are 3 roles for the Service Broker service:
1. Service Broker Administrator
* Configure content sources
* Configure policy definitions
* Add cloud accounts and cloud zones
2. Service Broker User
* Access self-service catalog
* Deploy machines and services from the catalog
* Manage deployments
3. Service Broker Viewer
* Read only

For more details on each Service Broker service role, refer to the official documentation [here][vRA8.1-cloud-assembly-service-broker-roles-official-doc-link]. 

<b><u>Code Stream Service Roles</b></u><br>
There are 5 roles for the Code Stream service:
1. Code Stream Administrator
* Execute any actions
* Run or resume pipelines that have a step for approval
2. Code Stream Developer
* Create and manage pipelines
* Create and manage endpoints
* Create and manage dashboards
3. Code Stream Executor
* Run pipelines and resume, pause, or cancel pipeline executions
4. Code Stream Viewer
* Read only at Code Stream service level
5. Code Stream User
* Read only at the project level

For more details on each Code Stream service role, refer to the official documentation [here][vRA8.1-code-stream-roles-official-doc-link].

<b><u>Orchestrator Service Roles</b></u><br>
There are 2 roles for the vRealize Orchestrator service:
1. Orchestrator Administrator
* Access all vRO features and content
* Add Git repositories
2. Orchestrator Workflow Designer
* Create, run, edit, and delete their own vRO content (including workflows, actions, and policies)
* Import packages, configurations, and resources

For more details on each vRealize Orchestrator service role, refer to the official documentation [here][vRA8.1-orchestrator-roles-official-doc-link].

<b><u>vRA Migration Assessment Service Roles</b></u><br>
There are 2 roles for the vRA Migration Assessment service:
1. Migration Assessment Administrator
* Perform a migration assessment
2. Migration Assessment Viewer
* Read only

For more details on the vRA Migration Assessment service, refer to the official documentation [here][vRA8.1-migration-assessment-service-official-doc-link].

[vRA8.1-easy-installer-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/installing-vrealize-automation-easy-installer.pdf
[vRA8.1-quickstart-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Getting-Started-Cloud-Assembly/GUID-91597976-E472-493B-8017-2D37DC8DC0E5.html
[vRA8.1-cloud-assembly-service-broker-roles-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Administering/GUID-F94CB09A-DD93-4571-9D39-7FC1E6FA68CF.html#GUID-F94CB09A-DD93-4571-9D39-7FC1E6FA68CF
[vRA8.1-code-stream-roles-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/Using-and-Managing-CodeStream/GUID-8EDC8310-232D-45FB-8C02-E4FB25687177.html
[vRA8.1-orchestrator-roles-official-doc-link]: https://docs.vmware.com/en/vRealize-Orchestrator/8.1/com.vmware.vrealize.orchestrator-using-client-guide.doc/GUID-D0FFDD2A-3D6C-485C-850B-FCD445391365.html
[vRA8.1-migration-assessment-service-official-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.1/migration-assessment/GUID-765814E6-FE56-436C-8053-D97AE023712B.html
