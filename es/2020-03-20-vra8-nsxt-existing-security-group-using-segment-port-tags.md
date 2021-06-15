# Parte 5 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Grupos de Seguridad Existentes (etiqueta de puerto de segmento)


Puede crear una plantilla vRA 8 para desplegar máquinas y ponerlas en un grupo de seguridad existente de NSX-T usando etiquetas de puerto de segmento de NSX-T. 

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
1. Configure los criterios de pertenencia para los grupos de seguridad existentes.
2. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX).
3. Configure una etiqueta para la red de la máquina en la plantilla para poner una etiqueta en un puerto de segmento de la máquina. 

Paso opcional:
* Cree entrada en la plantilla para personalizar el nombre de la máquina.


## Ejemplo

### Configure Criterios de Pertenencia Para Grupo de Seguridad 
1. Inicie una sesión en el cliente web de NSX-T y vaya a “Inventario” > “Grupos”
2. Revise el grupo de seguridad que quiere configurar. 
{{<image src="step2.png" linked="true">}}
3. De clic en el botón “Establecer miembros”
{{<image src="step3.png" linked="true">}}
4. De clic en el botón “+ AGREGAR CRITERIOS”.
5. Seleccione “Puerto de segmento” en la primera columna en la sección “Criterios 1”
6. Entre el valor de la etiqueta y alcance. Tiene que completar los ambos para la plantilla vRA.
{{<image src="step6.png" linked="true">}}

### Cree y Configure la Plantilla 
7. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
8. Nombre la plantilla y escoge el proyecto.
9. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco. <b>Nota que no necesita un objeto “Security Group" (grupo de seguridad) en la plantilla. </b>
10. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco.
11. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. 
12. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
13. Para la “red NSX”, cambie `networkType` de `properties` dependiendo de si está usando una red existente o una red bajo demanda. En este ejemplo, estoy usando una red existente.  

### Agregue Etiqueta de Puerto de Segmento en la Plantilla 
14. Para la máquina, debajo de `-network:`, agregue una línea `assignment` que se llama `tags:`. 
15. Agregue la línea `- key: <insert scope name>`.
16. Agregue la línea `value: <insert tag name>` debajo.
Nota que puede usar un “hashtag” para comentar en código YAML.
{{<image src="step16.png" linked="true">}}
17. De clic en el botón “PROBAR”.
18. De clic en el botón “IMPLEMENTAR” a crear una nueva implementación.
19. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.

### Verifique la Implementación 
20. Cuando la implementación está completa, vaya a “Implementaciones” en vRA y nota la dirección IP de la nueva implementación.
{{<image src="step20.png" linked="true">}}
21. Inicie una sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Inventory" > "Groups".
22. De clic en el botón “Ver miembros” del “grupo de seguridad” que usó.
23. De clic en el botón “Direcciones IP” y puede ver la dirección IP de la implementación.
{{<image src="step23.png" linked="true">}}
24.Si quiere ver la etiqueta de puerto de segmento que ha aplicado, inicie una sesión en el cliente web de NSX-T y vaya a “Advanced Networking & Security” > “Switching”.
25. Seleccione el segmento que la máquina está usando.
26. Encontre el puerto de segmento lógico lo que pertenece a la máquina. Puede ver el nombre de la máquina en la columna “Attachment”. Si no sabe el nombre de la máquina creada, vaya el cliente web de vRA y puede ver la implementación en “Implementaciones”.
{{<image src="step26.png" linked="true">}}
27. Seleccione el puerto de segmento lógico y de clic en el botón "Actions" > "Manage Tags".
{{<image src="step27.png" linked="true">}}
28. Puede ver la etiqueta lo que especificó en la plantilla.
{{<image src="step28.png" linked="true">}}

### Código YAML de la Plantilla de Ejemplo
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
    default: existing-sg-vm-tag-vm
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
          # this is a tag on the segment port, NOT the VM
          tags:
            - key: Three-Tier-App # Scope (defined in NSX-T)
              value: web # Tag (defined in NSX-T)
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints:
        - tag: 'subnet-cidr:192.168.35.0/24'
```

