# Using vRA with VMC on AWS: Getting Started


vRealize Automation can be used to automate the deployment of workloads to a VMware Cloud on AWS Software-Defined Data Center (SDDC). Let's learn how you can get started with using vRealize Automation with VMware Cloud on AWS. 


## Demo Product Versions  
* vRA Cloud
* VMware Cloud on AWS (SDDC version 1.14v4)


## Prerequisites
VMC on AWS:
* SDDC deployed
* Access to the SDDC

## Process Overview
1. Deploy a vRA Cloud Proxy in the VMC on AWS SDDC.
2. Add firewall rules for vRA Cloud Proxy. 
3. Configure vRA resources to consume VMC on AWS SDDC. 

## Demo / Example

### Deploy a vRA Cloud Proxy
1. In vRA Cloud Assembly, go to Infrastructure > Cloud Proxies. Click "NEW". 
{{<image src="step1.png" linked="true">}}
2. Click "DOWNLOAD OVA". You can use the OVA file link as well, but from my experiences, downloading it locally has been more successful instead of using the link.
{{<image src="step2.png" linked="true">}}
3. Go to the VMC on AWS SDDC vSphere Client and deploy OVF template.
{{<image src="step3-1.png" linked="true">}}
{{<image src="step3-2.png" linked="true">}}
{{<image src="step3-3.png" linked="true">}}
{{<image src="step3-4.png" linked="true">}}
{{<image src="step3-5.png" linked="true">}}
Note that since this is a VMC on AWS SDDC, you can only select the WorkloadDatastore.
{{<image src="step3-6.png" linked="true">}}
{{<image src="step3-7.png" linked="true">}}
Go back to vRA Cloud Assembly to copy the One-Time Key (OTK). 
{{<image src="step3-8.png" linked="true">}}
{{<image src="step3-9.png" linked="true">}}
{{<image src="step3-10.png" linked="true">}}
I'm going to leave all the networking properties blank and let DHCP assign an IP address to this VM. 
{{<image src="step3-11.png" linked="true">}}
{{<image src="step3-12.png" linked="true">}}
4. Once the vRA Cloud Proxy is deployed, power it on. The Cloud Proxy will receive an IP address on the network that you've chosen during the deployment. Note the IP address. 
{{<image src="step4.png" linked="true">}}

### Create Firewall Rules for vRA Cloud Proxy
5. Go to VMC on AWS UI > Networking & Security > Groups > Management Groups. Click "ADD GROUP" to create a new group for the vRA Cloud Proxy VM. Click "Set Members". 
{{<image src="step5.png" linked="true">}}
6. Type in the vRA Cloud Proxy VM IP address that you noted down from earlier.
{{<image src="step6.png" linked="true">}}
7. Go to Networking & Security > Gateway Firewall > Management Gateway. Add three rules like shown in the screenshot below. These rules allow communications between the vRA Cloud Proxy and vCenter, ESXi, and NSX respectively. Remember to publish the rules. 
{{<image src="step7.png" linked="true">}}
8. Go to VMC on AWS UI > Networking & Security > Groups > Compute Groups. Create a new group for the vRA Cloud Proxy VM like you did in the Management Groups section.
9. Go to Networking & Security > Gateway Firewall > Compute Gateway. Add a rule that allows vRA Cloud Proxy to send outbound traffic to the Internet, as shown in the screenshot below. Remember to publish the rule.
{{<image src="step9.png" linked="true">}}
10. Go to vRA Cloud Assembly > Infrastructure > Cloud Proxies. After some time, you will see the Cloud Proxy VM here. This can take several hours. 
{{<image src="step10.png" linked="true">}}

### Configure vRA Cloud Assembly Resources
11. Create a VMC on AWS cloud account. 
{{<image src="step11.png" linked="true">}}
12. For the VMC API token, you can generate by clicking on your name > My Account > API Tokens > GENERATE TOKEN. 
Give access to VMware Cloud on AWS. Remember to save the token in a safe place if you'd like to use it again in the future. You will not be able to see the token again after you see it generated the first time. 
{{<image src="step12-1.png" linked="true">}}
{{<image src="step12-2.png" linked="true">}}
13. Go to "Compute" and click "Cluster-1 / Compute-ResourcePool". Give it a capability tag. This is the only place vRA is allowed to deploy workloads in a VMC on AWS SDDC. 
{{<image src="step13.png" linked="true">}}
14. Create a cloud zone. Give it a capability tag; use the same one you've used for the Compute-ResourcePool. 
{{<image src="step14.png" linked="true">}}
15. Click "Compute" and manually select "Cluster-1 / Compute-ResourcePool".
{{<image src="step15.png" linked="true">}}
16. Create a network profile and give it a capability tag; use the same on you've been using. Configure the network profile appropriately depending on the use case. 
{{<image src="step16.png" linked="true">}}
17. Create a storage profile and select "WorkloadDatastore". Give a same capability tag here as well.
{{<image src="step17.png" linked="true">}}
18. Remember to create an image mapping and a flavor mapping. 

vRA can now deploy to the VMC on AWS SDDC. Remember to use the capability tag that you've created when creating the template that deploys to the VMC on AWS SDDC! 

#### Demo / Example Template YAML
```
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: 'Ubuntu-20.04'
      flavor: 'size-small'
      networks:
        - network: '${resource.Cloud_Network_1.id}'
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints: 
        - tag: vmconaws-sddc-02
```

<br>

Credit: Thanks to my colleague, [Michael Patton][mp-linkedin-profile-link], for working with me on this.

[mp-linkedin-profile-link]: https://www.linkedin.com/in/pattonmichael/ 
