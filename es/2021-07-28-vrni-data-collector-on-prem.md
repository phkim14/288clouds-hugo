# Using vRNI with VMC on AWS: Getting Started

# ~ Este post será traducido al español próximamente ~

vRealize Network Insight can be used to gain visibility into the workloads in a VMware Cloud on AWS Software-Defined Data Center (SDDC). Let's learn how you can get started with using vRealize Network Insight with VMware Cloud on AWS. 


## Demo Product Versions  
* vRNI Cloud
* VMware Cloud on AWS (SDDC version 1.14v4)


## Prerequisites
VMC on AWS:
* SDDC deployed
* Access to the SDDC

## Process Overview
1. Deploy a vRNI Data Collector in the VMC on AWS SDDC.
2. Add firewall rules for the vRNI Data Collector 
3. Configure the vRNI Data Collector.
4. Add VMC on AWS vCenter and NSX as data sources.  

## Demo / Example

### Deploy a vRNI Data Collector
1. In vRNI, go to Settings. 
{{<image src="step1.png" linked="true">}}
2. Click Infrastructure and Support > Overview and Updates. Click "ADD COLLECTOR VM".
{{<image src="step2.png" linked="true">}}
3. Click "DOWNLOAD" to download the vRNI Data Collector VM OVA.
{{<image src="step3.png" linked="true">}}
4. Go to the VMC on AWS SDDC vSphere Client and deploy OVF template.
{{<image src="step4-1.png" linked="true">}}
{{<image src="step4-2.png" linked="true">}}
{{<image src="step4-3.png" linked="true">}}
{{<image src="step4-4.png" linked="true">}}
{{<image src="step4-5.png" linked="true">}}
{{<image src="step4-6.png" linked="true">}}
{{<image src="step4-7.png" linked="true">}}
Note that since this is a VMC on AWS SDDC, you can only select the WorkloadDatastore.
{{<image src="step4-8.png" linked="true">}}
At the time of writing this post, you must select a native VMC on AWS segment (sddc-cgw-network-1) to deploy the vRNI Data Collector VM. 
{{<image src="step4-9.png" linked="true">}}
Go back to vRNI to copy the shared secret. 
{{<image src="step4-10.png" linked="true">}}
Shared secret is the only value you have to give for now. Other settings, such as IP address, DNS, and NTP, will be configured after the VM is deployed and powered on for the first time.
{{<image src="step4-11.png" linked="true">}}
{{<image src="step4-12.png" linked="true">}}
5. Once the vRNI Data Collector is deployed, power it on. 
{{<image src="step5.png" linked="true">}}

### Create Firewall Rules for vRNI Data Collector
6. Go to VMC on AWS UI > Networking & Security > Groups > Management Groups. Click "ADD GROUP" and create a new group for the vRNI Data Collector VM. Set the member with the IP address that you are planning to give the vRNI Data Collector VM. 
{{<image src="step6.png" linked="true">}}
7. Go to Networking & Security > Gateway Firewall > Management Gateway. Add two rules like shown in the screenshot below. These rules allow the vRNI Data Collector to communicate with vCenter and NSX. Remember to publish the rules. 
{{<image src="step7.png" linked="true">}}
8. Go to VMC on AWS UI > Networking & Security > Groups > Compute Groups. Create a new group for the vRNI Data Collector VM like you did in the Management Groups section.
{{<image src="step8.png" linked="true">}}
9. Go to Networking & Security > Gateway Firewall > Compute Gateway. Add a rule that allows vRNI Data Collector VM to communicate with vRNI platform, as shown in the screenshot below. Remember to publish the rule.
{{<image src="step9.png" linked="true">}}

### Configure vRNI Data Collector
10. Go to the VMC on AWS SDDC vSphere Client and launch a remote console for the vRNI Data Collector VM.
{{<image src="step10.png" linked="true">}}
11. Login as user `consoleuser` with password `console`. Then type `setup` to start configuring the appliance. You can see these instructions in the console too. 
{{<image src="step11.png" linked="true">}}
12. Create user passwords as instructed.  
{{<image src="step12.png" linked="true">}}
13. Give the IP address to the appliance. Make sure the IP address is same as the one you've used to create the security groups in the VMC on AWS for the firewalls in the previous section. You can use a public DNS like I did, or use an internal DNS server if you have one deployed in the VMC on AWS SDDC. 
{{<image src="step13.png" linked="true">}}
14. You can use a public NTP like Amazon Time Sync Service (169.254.169.123), which is what I'm doing here, or use an internal NTP server if you have one deployed in the VMC on AWS SDDC.  
{{<image src="step14.png" linked="true">}}
15. Creating a web-proxy is optional. I don't have one in my environment so I'm not going to configure this here. 
{{<image src="step15-1.png" linked="true">}}
Wait until the configuration is finished.
{{<image src="step15-2.png" linked="true">}}
16. Go to vRNI > Settings > Infrastructure and Support > Overview and Updates. You can now see the Data Collector VM you have deployed in the VMC on AWS SDDC.
{{<image src="step16.png" linked="true">}}

### Add VMC on AWS Data Sources
17. Go to Settings > Accounts and Data Sources. Click "ADD SOURCE". 
{{<image src="step17.png" linked="true">}}
18. Click "VMC on AWS - vCenter" under "VMware Cloud (VMC) on AWS" section.
{{<image src="step18.png" linked="true">}}
19. Select the vRNI Data Collector that you deployed in the VMC on AWS SDDC. Provide the IP address/FQDN for the VMC on AWS SDDC vCenter. This could be the public or the private IP of the vCenter, depending on how you have your SDDC set up. The validation will not be successful if the vRNI Data Collector cannot communicate back and forth with the vCenter. Since I don't have a web proxy set up, I'm going use the private vCenter IP here. 
{{<image src="step19-1.png" linked="true">}}
{{<image src="step19-2.png" linked="true">}}
20. Click "ADD SOURCE" to now add the NSX Manager. 
21. Click "VMC on AWS - NSX Manager" under "VMware Cloud (VMC) on AWS" section.
{{<image src="step21.png" linked="true">}}
22. Choose the VMC on AWS vCenter data source that we've just added. Select the vRNI Data Collector deployed in the VMC on AWS sddc. Provide the NSX Manager private IP adress and provide the API token where it says "CSP Refresh Token". 
{{<image src="step22-1.png" linked="true">}}
The API token can be generated by going to your account > API Tokens. Make sure that you're providing the API Token with the VMC on AWS NSX Cloud Admin role. Refer to [step 12 of my blog on deploying a vRA Cloud Proxy][vRA-cloud-proxy-link] if you're having trouble finding where to generate this API token.
{{<image src="step22-2.png" linked="true">}}
23. VMC NSX Manager data source has now been added. 
{{<image src="step23.png" linked="true">}}

vRNI can now collect and display information about workloads in the VMC on AWS SDDC!

### View VMC on AWS Environment
24. Go to Environments > VMware Cloud on AWS.
{{<image src="step24.png" linked="true">}}
25. Click on the VMC on AWS SDDC that you've just added. 
{{<image src="step25.png" linked="true">}}
26. View the main dashboard for the VMC on AWS SDDC. 
{{<image src="step26.png" linked="true">}}

<br>

Credit: Thanks to my colleague, [Asaf Blubshtein][ab-blog-link], for working with me on this.

[ab-blog-link]: https://softwaredefinedcoffee.com/ 
[vRA-cloud-proxy-link]: https://288clouds.com/2021-07-23-vrac-proxy-in-vmc.html
