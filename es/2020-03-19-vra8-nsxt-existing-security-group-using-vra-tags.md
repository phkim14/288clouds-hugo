# Parte 4 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Grupos de Seguridad Existentes (etiqueta vRA)


# ~ Este post será traducido al español próximamente ~

Puedes crear una plantilla vRA 8 para desplegar máquinas y ponerlas en un grupo de seguridad existente de NSX-T usando etiquetas vRA. 

## Versiones de Productos Demostración (Demo)
* vSphere 6.5 U3
* vRA 8.0.1 (incluyendo vRSLCM y vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1


## Prerrequisitos
vRA 8:
* Cuenta conectada de NSX-T 
* Infraestructura básica configurada (Proyectos, Zonas de nube, Asignaciones de imagen, Asignaciones de tipo)

NSX-T:
* Grupos de seguridad configurados 


## Información General de Proceso
1. Cree una etiqueta en el grupo de seguridad existente.
2. Cree una plantilla con los objetos: “máquina agnóstica de nube”, “red NSX” y “grupo de seguridad”.
3. Especifique cual grupo de seguridad va a hacer usado añadiendo una etiqueta de restricción en el objecto “grupo de seguridad”. 

Paso opcional:
* Cree entradas en la plantilla para personalizar el nombre de la máquina. 

## Ejemplo

### Configure la Etiqueta “Grupo de Seguridad”
1. Vaya a “Infraestructura” > “Seguridad” (en el menú “Recursos”).
2. Seleccione el grupo de seguridad al que le quiere añadir la etiqueta.
3. Clic “Etiquetas”.
{{<image src="step3.png" linked="true">}}
4. Entre el nombre de la etiqueta en “Agregar etiquetas”, presione “Enter” y guarde.
{{<image src="step4.png" linked="true">}}
5. Verifique que la etiqueta se ha creado correctamente.
{{<image src="step5.png" linked="true">}}

### Cree y Configure la Plantilla
6. Go to "Blueprints" and Click "+ NEW" to create a new blueprint. (or you can choose to use an existing blueprint and skip this section).
7. Give a name to the blueprint and choose a project.
8. Drag on a Cloud Agnostic Machine and a NSX Network onto the canvas. 
9. Connect the Cloud Agnostic Machine to the NSX Network on the canvas.
10. On the right side in the YAML file, choose an image and size for the machine. 
11. Under `- network: `, add the line `assignment: static` to give a static IP address to the machine from the IP range we've created.
12. For the NSX network, change the `networkType` under `properties` accordingly depending on whether you are using existing or on-demand network. In this demo, I'll be using an existing network.

### Configure Blueprint Security Group
13. Drag a Security Group onto the canvas.
{{<image src="step13.png" linked="true">}}
14. For the security group, below `securityGroupType`, add the line `constraints:` then another line `- tag:` and type the tag of the existing security group you want to use.
{{<image src="step14.png" linked="true">}}
15. For the machine, under `networks`, add a line under `assignment` called `securityGroups:`.
16. Add the line `- '${resource.<insert security group object name>.id}'` below to connect the security group to the machine. 
{{<image src="step16.png" linked="true">}}
17. Click "TEST".
18. Click "DEPLOY" to create a new deployment.
19. Give it a deployment name, choose "Current Draft", the cick "DEPLOY".

### Verify Deployment
20. Once deployed, go to "Deployments" tab in vRA and note the IP address of the deployment.
21. Now log into NSX-T UI and go to "Inventory" > "Groups".
22. Click "View Members" of the security group you have selected in the network profile.
23. Click "IP Addresses" and you'll see the IP address of the deployment.

### Código YAML de la Plantilla de Ejemplo
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
    default: existing-sg-vm
resources:
  Cloud_SecurityGroup_1:
    type: Cloud.SecurityGroup
    properties:
      securityGroupType: existing
      constraints:
        - tag: vra-test-sg
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
          securityGroups:
            - '${resource.Cloud_SecurityGroup_1.id}'
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.35.0/24'
```
