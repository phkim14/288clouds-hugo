# On-demand NSX-T Networks with vRA Cloud in VMC on AWS VS. On-Prem vSphere

# ~ Este post será traducido al español próximamente ~

You can create on-demand NSX-T networks using a vRA Cloud template in a VMC on AWS environment as well as in an on-prem vSphere environment. However, there are some differences between how on-demand NSX-T networks are created in these two environments due to limitations with vRA at the time of writing this post. 

## On-demand Network Types
vRA Cloud Assembly has several on-demand network types available depending on the resource you choose. 

For a Cloud Agnostic Network resource, there are two on-demand network types: private and outbound.
{{<image src="cloud-agnostic-network-resource.png" linked="true">}}

For a NSX-specific Network resource, there are three on-demand network types: routed, private, and outbound.
{{<image src="nsxt-specific-network-resource.png" linked="true">}}

* Private network blocks both ingress and egress traffic. It only allows traffic to occur between machines deployed on this same network. 
* Outbound network block ingress traffic but allows egress traffic. NAT occurs at the tier-1 router.
* Routed network allows machines deployed on the network to communicate with other networks that are attached to the same tier-1 router or other routes that are routable with the tier-1 router. 

You can refer to the official VMware documentation about vRA network types [here][vra-network-settings-vmware-doc-link]. 


## On-demand Network Creation in On-Prem vSphere 
vRA creates an on-demand network in an on-prem vSphere environment differently depending on the network type that you choose in the vRA Cloud template. 

{{<image src="on-prem-networks.png" linked="true">}}

As you can see, if you choose `networkType: private` then vRA creates a NSX-T segment that is not connected to any logical routers. Hence, creating an isolated and unroutable network. 

If you choose `networkType: outbound`, vRA creates a tier-1 logical router and attaches it to an existing tier-0 logical router that you specify in the network profile. This tier-1 logical router will advertise NAT routes. vRA creates a NSX-T segment and attaches it to the tier-1 logical router that has been created.

If you choose `networkType: routed`, vRA creates a tier-1 logical router and attaches it to an existing tier-0 logical router that you specify in the network profile, similar to when you choose `networkType: outbound`. This tier-1 logical router will advertise NSX connected routes. vRA creates a NSX-T segment and attaches it to the tier-1 logical router that has been created. 

You can find an example of creating an on-demand routed network in a separate blog post [here][on-demand-routed-network-link].


## On-demand Network Creation in VMC on AWS
At the time of writing this blog, vRA can only create a routed on-demand network even though the template allows you to choose other on-demand network types, like outbound or private. Also, at this time, you can only select the default compute gateway provided by the VMC on AWS SDDC for the network domain in the network profile. 
{{<image src="vmc-on-demand-network-np.png" linked="true">}}

So if you choose `networkType: private` in a vRA cloud template and deploy it to a VMC on AWS environment using the network profile (like the sample shown in the image above), vRA will create a NSX-T segment on demand according to the network profile, but it will attach it to the default compute gateway, making it a routed network. 
{{<image src="vmc-on-demand-private-network.png" linked="true">}}

If you want to create on-demand networks that are truly private or outbound, you have the following options at this time:
* <b> Option 1. </b> Create an existing security group with appropriate firewall rules for the network type that you want. Apply this existing security group in the vRA cloud template that creates on-demand networks. 
* <b> Option 2. </b> Create a new security group with appropriate on-demand firewall rules for the network type that you want in the vRA cloud template that creates on-demand networks. 

If you'd like more information on how you can create on-demand firewall rules in VMC on AWS with vRA cloud template, you can refer to another blog post [here][on-demand-fw-vmc-link].

<br>

Credit: Thanks to my colleague, [Michael Patton][mp-linkedin-profile-link], for working with me on this.

[vra-network-settings-vmware-doc-link]: https://docs.vmware.com/en/vRealize-Automation/8.3/Using-and-Managing-Cloud-Assembly/GUID-68197096-1155-49C0-8043-D6DDE4EED28E.html
[on-demand-routed-network-link]: https://288clouds.com/2020-03-16-vra8-blueprint-nsxt-on-demand-routed-network.html
[on-demand-fw-vmc-link]: https://288clouds.com/2021-03-29-vrac-vmc-nsxt-on-demand-dfw.html
[mp-linkedin-profile-link]: https://www.linkedin.com/in/pattonmichael/ 
