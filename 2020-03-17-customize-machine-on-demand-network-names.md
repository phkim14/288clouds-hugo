# Customize Machine and On-demand Network Names


vRA 8 blueprint gives random names and numbers to the VMs and on-demand NSX-T networks deployed if you do not specify a naming convention. This blog will cover how you can customize the names of your VMs and on-demand NSX-T networks when they are deployed from a vRA 8 blueprint. 


## Prerequisites
vRA 8:
* NSX-T account connected
* Basic infrastructure configured (Projects, Cloud Zones, Flavor Mappings, Image Mappings)

NSX-T (if you want to use on-demand network name):
* tier-0 logical router configured


## Process Overview
1. In the project, create a naming convention.
2. In the blueprint, create a custom input for each object you want to name.
3. Use the input variable name to edit the `name` property of the machine / network.


## Demo / Example

### Create Project Naming Convention
1. Go to "Infrastructure" > "Projects" and click on the Project that you'd like to add the naming convention.
2. Go to "Provisioning" tab.
3. Under "Custom Naming" section, specify the machine name template: `${resource.name}-${##}`
![Step3](step3.png)

### Create Blueprint Custom Inputs
4. Create a new blueprint and drag Cloud Agnostic Machine and NSX Network objects onto the canvas.
5. Configure the blueprint like the screenshot (i.e. machine image, machine flavor, machine networks assignment, network type, etc.).
![Step5](step5.png)
6. Create a variable called `vm-name` under `inputs` to customize the machine name.
7. Give it a type `string`.
8. Give the variable a `title`, which is what the blueprint will use to ask the user for the input value when he/she deploys this blueprint.
9. Give a default value for this variable with `default:`.
10. Do the same for a variable `network-name` under `inputs` to customize the network name.
![Step10](step10.png)
Note: You can also create custom inputs by clicking the tab "Inputs" and using the GUI. 
11. Add `name:` properties for the machine and the network to use the input values. 
`name: '${input.<insert input variable name>}'`
![Step11](step11.png)

### Verify Deployment
Now if you deploy this blueprint, you should see the machine and the network created with customized names. 
![Step12](step12.png)
![Step12-1](step12-1.png)

### Demo / Example Blueprint YAML File
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: vm-name
    default: on-demand-net-vm
  network-name:
    type: string
    title: network-name
    default: on-demand-ov
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: centos-temp
      flavor: small
      customizationSpec: linux
      name: '${input.vm-name}'
      networks:
        - network: '${resource.Cloud_NSX_Network_1.id}'
          assignment: static
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: routed
      name: '${input.network-name}'
```
<br>
<i>Credit: Huge thanks to my colleague, <a href="https://www.linkedin.com/in/pattonmichael/" target="_blank" rel="noopener noreferrer">Michael Patton</a>, for working with me on this demo.</i> 
