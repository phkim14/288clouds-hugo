# Customize Machine and On-demand Network Names


# ~ Este post será traducido al español próximamente ~

vRA 8 blueprint gives random names and numbers to the VMs and on-demand NSX-T networks deployed if you do not specify a naming convention. This blog will cover how you can customize the names of your VMs and on-demand NSX-T networks when they are deployed from a vRA 8 blueprint. 

## Demo Product Versions  
* vSphere 6.5 U3
* vRA 8.0.1 (including vRSLCM and vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

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
{{<image src="step3.png" linked="true">}}

### Create Blueprint Custom Inputs
4. Create a new blueprint and drag Cloud Agnostic Machine and NSX Network objects onto the canvas.
5. Configure the blueprint like the screenshot (i.e. machine image, machine flavor, machine networks assignment, network type, etc.).
{{<image src="step5.png" linked="true">}}6. Create a variable called `vm-name` under `inputs` to customize the machine name.
7. Give it a type `string`.
8. Give the variable a `title`, which is what the blueprint will use to ask the user for the input value when he/she deploys this blueprint.
9. Give a default value for this variable with `default:`.
10. Do the same for a variable `network-name` under `inputs` to customize the network name.
{{<image src="step10.png" linked="true">}}
Note: You can also create custom inputs by clicking the tab "Inputs" and using the GUI. 
11. Add `name:` properties for the machine and the network to use the input values. 
`name: '${input.<insert input variable name>}'`
{{<image src="step11.png" linked="true">}}

### Verify Deployment
Now if you deploy this blueprint, you should see the machine and the network created with customized names. 
{{<image src="step12.png" linked="true">}}
{{<image src="step12-1.png" linked="true">}}

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
