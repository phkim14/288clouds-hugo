# Parte 4 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Grupos de Seguridad Existentes (etiqueta vRA)


Puede crear una plantilla vRA 8 para desplegar máquinas y ponerlas en un grupo de seguridad existente de NSX-T usando etiquetas vRA. 

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
2. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube), "NSX Network" (red NSX), "Security Group" (grupo de seguridad).
3. Especifique cual grupo de seguridad va a hacer usado añadiendo una etiqueta de restricción en el objecto “grupo de seguridad”. 

Paso opcional:
* Cree entradas en la plantilla para personalizar el nombre de la máquina. 

## Ejemplo

### Configure la Etiqueta “Grupo de Seguridad”
1. Vaya a “Infraestructura” > “Seguridad” (en el menú “Recursos”).
2. Seleccione el grupo de seguridad al que le quiere agregar la etiqueta.
3. De clic en el botón “Etiquetas”.
{{<image src="step3.png" linked="true">}}
4. Entre el nombre de la etiqueta en “Agregar etiquetas”, de clic en el botón “Enter” y guarde.
{{<image src="step4.png" linked="true">}}
5. Verifique que la etiqueta se ha creado correctamente.
{{<image src="step5.png" linked="true">}}

### Cree y Configure la Plantilla
6. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
7. Nombre la plantilla y escoge el proyecto.
8. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco.
9. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco.
10. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. 
11. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
12. Para la “red NSX”, cambie `networkType` de `properties` dependiendo de si está usando una red existente o una red bajo demanda. En este ejemplo, estoy usando una red existente.  

### Configure el Grupo de Seguridad
13. Pon el "Security Group" (grupo de seguridad) en el lienzo en blanco.
{{<image src="step13.png" linked="true">}}
14. Para el "Security Group", agregue una línea `constraints:` debajo de `securityGroupType`. Debajo de la línea `constraints:`, agregue otra línea `-tag:` y entra el nombre de la etiqueta del grupo de seguridad que quiere usar.
{{<image src="step14.png" linked="true">}}
15. Para la "Cloud Agnostic Machine", debajo de `networks`, agregue una línea `securityGroups:`.
16. Agregue una línea `- '${resource.<insert security group object name>.id}'` para conectar el "Security Group" á la "Cloud Agnostic Machine".
{{<image src="step16.png" linked="true">}}
17. De clic en el botón “PROBAR”.
18. De clic en el botón “IMPLEMENTAR” a crear una nueva implementación.
19. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.

### Verifique la Implementación 
20. Cuando la implementación está completa, vaya a “Implementaciones” en vRA y nota la dirección IP de la nueva implementación.
21. Inicie una sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Inventory" > "Groups".
22. De clic en el botón “Ver miembros” del “grupo de seguridad” que usó en el lienzo en blanco.
23. De clic en el botón “Direcciones IP” y puede ver la dirección IP de la implementación. 

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
