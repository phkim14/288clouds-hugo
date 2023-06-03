# vRA 8 + NSX-T Blog Series Part 7: Create a vRA 8 (Cloud) Blueprint with Existing NSX-T One-Arm Load Balancer


You can create a vRA Cloud blueprint to deploy machines and place them behind an existing NSX-T one-arm load balancer. This method does not create any on-demand tier-1 routers. It uses the existing load balancer and simply adds virtual server, server pool, and application profile (monitor too if you want). This method should work in vRA 8.1 as well, but I used vRA Cloud to create the demo.

Below is a simplified diagram of a NSX-T one-arm load balancer.
{{<image src="diagram.png" linked="true">}}

Note that vRA Cloud / vRA 8.1 does not fully support NSX-T policy API yet. Full Support for NSX-T policy API is scheduled to be added in vRA 8.2 (but who knows, I'm not a product manager).
If you try to create a blueprint using policy tier-1 router and load balancer, the blueprint deployment will fail and display the following error message: `[289] [Principal 'admin' with role '[enterprise_admin]' attempts to delete or modify an object of type LoadBalancerService it doesn't own. (createUser=nsx_policy, allowOverwrite=null)]`
{{<image src="error.png" linked="true">}}


## Demo Product Versions  
* VCF 3.9.1.0-15345960 (vSphere 6.7, NSX-V 6.4.6)
* NSX-T 2.5.0
* vRA Cloud

## Prerequisites
vRA Cloud:
* NSX-T account connected
* Basic infrastructure configured in Cloud Assembly (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T:
* tier-0 logical router configured
* (non-policy-API) tier-1 logical router configured
* edge cluster configured (edge nodes must be at least medium size)


## Process Overview
1. Create a NSX-T one-arm load balancer.
2. Create a network profile to use an existing load-balancer.
3. Create a blueprint with Cloud Agnostic Machine, Cloud Network, and Cloud Load Balancer.

optional steps:
* Create inputs in the blueprint to customize the machine name.
* Create multiple load balancers and apply a constraint tag to deploy load balancing services on a specific load balancer. 


## Demo / Example

### Create NSX-T One-Arm Load Balancer
1. In the NSX-T UI, go to "Advanced Networking & Security" > "Networking" > "Load Balancers".
2. Click "+" to create a new load balancer.
{{<image src="step2.png" linked="true">}}
3. Click on the load balancer you've just created and click "EDIT" next to "Attachment".
{{<image src="step3.png" linked="true">}}
4. Select a Tier-1 logical router to provide load balancing services. The tier-1 router cannot be a policy-API router. Create a non-policy-API tier-1 router under "Advanced Networking & Security" > "Networking" > "Routers" if you do not have one.
{{<image src="step4.png" linked="true">}}
5. You don't need to configure virtual server or server pools at this time. They will be created by the blueprint. 

### Create Network Profile
6. In vRA Cloud (or vRA 8) Cloud Assmebly, go to "Infrastructure" > "Network Profiles" (under Configure) and click "+ NEW NETWORK PROFILE". (or you can choose to edit an existing network profile).
7. Choose an account/region and give the profile a name.
8. Go to "Networks" tab and configure existing networks or on-demand networks. In this demo, I'll be using an existing NSX-T network. Refer to previous vRA 8 + NSX-T blog series for more details on how to configure existing or on-demand networks.
9. When using an existing NSX-T load balancer in a blueprint, you do not have to select tier-0 logical router or edge cluster under "Network Policies" tab.
10. Go to "Load Balancers" tab, click "+ ADD LOAD BALANCER" and choose the load balancer created in previous steps.
{{<image src="step10.png" linked="true">}}

### Create and Configure Blueprint
11. Go to "Blueprints" and Click "+ NEW" to create a new blueprint.
12. Give a name to the blueprint and choose a project.
13. Drag on a Cloud Agnostic Machine, Cloud Agnostic Network, and Cloud Agnostic Load Balancer onto the canvas. You can use environment-specific resources as well if you'd like. 
14. Connect the Machine and the Load Balancer to the Network on the canvas. Also connect the Machine to the Load Balancer. 
{{<image src="step14.png" linked="true">}}
15. On the right side in the YAML file, choose an image and size for the machine. Add `count` property to indicate how many machines you want to deploy.
16. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine.
17. Since I'll be placing the machines on an existing NSX-T network, I want to make sure it says `networkType: existing` under `properties`. Below `networkType`, I also add the line `constraints:` then another line `- tag:` to choose the existing network I want to use.
18. Configure the load balancer. `protocol` and `port` under `routes` property are required. `healthCheckConfiguration` is optional - if you provide details, custom monitor will be created. Otherwise, only the virtual server, server pool, and application profile will be created.
{{<image src="step18.png" linked="true">}}
19. Click "TEST".
20. Click "DEPLOY" to create a new deployment.
21. Give it a deployment name, choose "Current Draft", the click "DEPLOY".

### Verify Deployment
22. Go to "Deployments" tab in Cloud Assembly and check that the deployment is completed successfully.
{{<image src="step22.png" linked="true">}}
23. Now log into NSX-T UI and go to "Advanced Networking & Security" > "Networking" > "Load Balancers" and you'll see that the existing load balancer now has a virtual server. 
{{<image src="step23.png" linked="true">}}
24. If you click on the virtual server, you can see the port and protocol as you've defined in the blueprint.
{{<image src="step24.png" linked="true">}}
25. If you click the server pool and look at the pool members, you will see the machines that have been created by the blueprint. 
{{<image src="step25.png" linked="true">}}
26. If you click the profiles, you can see application profile that has been created.
{{<image src="step26.png" linked="true">}}
27. Since this example had `healthCheckConfiguration` property specified in the blueprint, you can see custom monitor that has been created.
{{<image src="step27.png" linked="true">}}

Note that in my example, the virtual server pool operational state is down because I did not open the appropriate port on the machines to communicate. Assuming your servers are configured correctly, the load balancer should work as intended.  

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
          healthCheckConfiguration:
            port: 80
            protocol: HTTP
            healthyThreshold: 2
            intervalSeconds: 5
            timeoutSeconds: 15
            unhealthyThreshold: 5
            urlPath: /
      network: '${resource.Cloud_Network_1.id}'
      instances: '${resource.Cloud_Machine_1[*].id}'
      internetFacing: false
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: im-CentOS7
      flavor: fl-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
          assignment: static
      count: 2
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.92.0/24'

```

### (Optional) Multiple Load Balancers in Network Profile
If you have multiple load balancers in a network profile, you can put tags on each load balancer then specify the tag in a blueprint to deploy the load balancing services to a specific load balancer.
* Go to the network profile and add tags to each load balancer. 
{{<image src="optional-1.png" linked="true">}}
* Add a constraint tag in the blueprint to indicate which load balancer you want to use.
{{<image src="optional-2.png" linked="true">}}
* Once you deploy this blueprint, you will see in the request details / provisioning diagram that the constraint tag is applied.
{{<image src="optional-3.png" linked="true">}}
* After the deployment is complete, go to the NSX-T UI and you'll see that the load balancing services have been deployed to the desired load balancer.
{{<image src="optional-4.png" linked="true">}}

### Using Constraint Tag to Indicate Load Balancer Example Blueprint YAML File
```
formatVersion: 1
inputs: {}
resources:
  Cloud_LoadBalancer_1:
    type: Cloud.LoadBalancer
    properties:
      routes:
        - protocol: HTTPS
          port: 443
          instancePort: 443
          instanceProtocol: HTTPS
      network: '${resource.Cloud_Network_1.id}'
      instances: '${resource.Cloud_Machine_1[*].id}'
      internetFacing: false
      constraints: 
        - tag: 'nonpolicy-LB-02'
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: im-CentOS7
      flavor: fl-small
      networks:
        - network: '${resource.Cloud_Network_1.id}'
          assignment: static
      count: 2
  Cloud_Network_1:
    type: Cloud.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.92.0/24'
```
