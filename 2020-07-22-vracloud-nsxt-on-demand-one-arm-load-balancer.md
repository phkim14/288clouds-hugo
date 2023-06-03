# vRA 8 + NSX-T Blog Series Part 8: Create a vRA 8 (Cloud) Blueprint with On-demand NSX-T One-Arm Load Balancer


You can create a vRA Cloud blueprint to deploy machines and place them behind an on-demand NSX-T one-arm load balancer. This method creates a tier-1 router then configures load balancing services, adding virtual server, server pool, and application profile (monitor too if you want). This method should work in vRA 8.1, but I used vRA Cloud to create the demo.

Below is a simplified diagram of a NSX-T one-arm load balancer.
{{<image src="diagram.png" linked="true">}}

Note that vRA Cloud / vRA 8.1 does not fully support NSX-T policy API yet. Full support for NSX-T policy API is scheduled to be added in vRA 8.2 (but who knows, I'm not a product manager).

## Demo Product Versions  
* VCF 3.9.1.0-15345960 (vSphere 6.7, NSX-V 6.4.6)
* NSX-T 2.5.0
* vRA Cloud

## Prerequisites
vRA Cloud:
* NSX-T account connected
* Basic infrastructure configured in Cloud Assembly (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* tier-0 logical router configured (can be a policy API router)
* edge cluster configured (edge nodes must be at least medium size)


## Process Overview
1. Create a network profile to use an on-demand load-balancer.
2. Create a blueprint with Cloud Agnostic Machine, Cloud Network, and Cloud Load Balancer.

optional step:
* Create inputs in the blueprint to customize the machine name.


## Demo / Example

### Create Network Profile
1. In vRA Cloud (or vRA 8) Cloud Assmebly, go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE". (or you can choose to edit an existing network profile).
2. Choose an account/region and give the profile a name.
3. Go to "Networks" tab and configure existing networks or on-demand networks. In this demo, I'll be using an existing NSX-T network. Refer to previous vRA 8 + NSX-T blog series for more details on how to configure existing or on-demand networks.
4. Go to "Network Policies" tab and select tier-0 logical router and edge cluster. These are required to deploy an on-demand load balancer because the blueprint creates a tier-1 router in the process. 
{{<image src="step4.png" linked="true">}}
5. Go to "Load Balancers" tab, and make sure there aren't any existing load balancers on the list. If there are load balancers here, the blueprint will not create an on-demand load balancer. It will just add load balancing services to an existing load balancer.

### Create and Configure Blueprint
6. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
7. Give a name to the blueprint and choose a project.
8. Drag on a Cloud Agnostic Machine, Cloud Agnostic Network, and Cloud Agnostic Load Balancer onto the canvas. You can use environment-specific resources as well if you'd like. 
9. Connect the Machine and the Load Balancer to the Network on the canvas. Also connect the Machine to the Load Balancer. 
{{<image src="step9.png" linked="true">}}
10. On the right side in the YAML file, choose an image and size for the machine. Add `count` property to indicate how many machines you want to deploy.
11. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine.
12. Since I'll be placing the machines on an existing NSX-T network, I want to make sure it says `networkType: existing` under `properties`. Below `networkType`, I also add the line `constraints:` then another line `- tag:` to choose the existing network I want to use.
13. Configure the load balancer. `protocol` and `port` under `routes` property are required. `healthCheckConfiguration` is optional - if you provide details, custom monitor will be created. Otherwise, only the virtual server, server pool, and application profile will be created.
{{<image src="step13.png" linked="true">}}
14. Click "TEST".
15. Click "DEPLOY" to create a new deployment.
16. Give it a deployment name, choose "Current Draft", the click "DEPLOY".

### Verify Deployment
17. Go to "Deployments" tab in Cloud Assembly and check that the deployment is completed successfully.
{{<image src="step17.png" linked="true">}}
18. Now log into NSX-T UI and go to "Advanced Networking & Security" > "Networking" > "Routers" and you'll see that a tier-1 router has been created.
{{<image src="step18.png" linked="true">}}
19. Go to "Advanced Networking & Security" > "Networking" > "Load Balancers" and you'll see that a load balancer has been created. 
{{<image src="step19.png" linked="true">}}
20. If you click on the virtual server, you can see the port and protocol as you've defined in the blueprint.
{{<image src="step20.png" linked="true">}}
21. If you click the server pool and look at the pool members, you will see the machines that have been created by the blueprint. 
{{<image src="step21.png" linked="true">}}
22. If you click the profiles, you can see application profile that has been created.
{{<image src="step22.png" linked="true">}}


### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs: {}
resources:
  Cloud_LoadBalancer_1:
    type: Cloud.LoadBalancer
    properties:
      routes:
        - protocol: HTTP
          port: 80
          instancePort: 80
          instanceProtocol: HTTP
      network: '${resource.Cloud_Network_1.id}'
      instances: '${resource.Cloud_Machine_1[*].id}'
      internetFacing: false
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: im-CentOS7
      flavor: fl-small
      count: 2
      networks:
        - network: '${resource.Cloud_Network_1.id}'
          assignment: static
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.92.0/24'
```
