# Parte 6 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Grupos de Seguridad Bajo Demanda


Puede crear una plantilla vRA 8 para desplegar máquinas y ponerlas en un grupo de seguridad bajo demanda. 

## Versiones de Productos Demostración (Demo)
* vSphere 6.5 U3
* vRA 8.0.1 (incluyendo vRSLCM y vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## Prerrequisitos
vRA 8:
* Cuenta conectada de NSX-T
* Infraestructura básica configurada (proyectos, zonas de nube, "image mappings" o asignaciones de imagen, "flavor mappings" o asignaciones de tipo)

NSX-T:
* red configurado 


## Información General de Proceso 
1. Cree o revise un perfil de red.
2. Seleccione "Create an on-demand security group" en el perfil de red.
3. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX).

Paso opcional:
* Cree entrada en la plantilla para personalizar el nombre de la máquina.


## Ejemplo

### Configure el Perfil de Red
1. Vaya a “Infraestructura” > “Perfiles de Red” (en el menú “Configurar”) y de clic en el botón “+ NUEVO PERFIL DE RED” (o puede revisar un perfil de red existente).
2. Escoge la cuenta o región y nombre el perfil. 
3. Configure redes existentes. 
4. Vaya a “Directivas de red” y seleccione “Create an on-demand security group”. 
{{<image src="step4.png" linked="true">}}
5.  De clic en el botón “CREAR” para guardar el perfil.

### Cree y Configure la Plantilla
6. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
7. Nombra la plantilla y escoge el proyecto.
8. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco. <b>Nota que no tiene que poner el "Security Group" (grupo de seguridad) en el lienzo en blanco.</b>
9. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco.
10. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. 
11. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
12. Para la "NSX network", escoje `private` o `outbound` para `networkType` debajo de `properties`
{{<image src="step14.png" linked="true">}}
13. De clic en el botón “PROBAR”.
14. De clic en el botón “IMPLEMENTAR” para crear una nueva implementación.
15. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.

### Verifique la Implementación 
16.  Inicie sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a “Advanced Networking & Security” > "Inventory" > Groups". Puede ver un nuevo grupo de seguridad con el nombre empezando con "isolation-securitygroup".
{{<image src="step17.png" linked="true">}}
17. Vaya a "Security" > "Distributed Firewall".
18. Puede ver una nueva sección de "Distributed Firewall" para el grupo de seguridad creado. Si ha elegido `networkType: private` en la plantilla, las reglas de "Distributed Firewall" bloquean todos los tráficos entrantes y salientes. Si ha elegido `networkType: outbound`, las reglas de "Distributed Firewall" solamente bloquean los tráficos entrantes.
{{<image src="step19.png" linked="true">}}

### Código YAML de la Plantilla de Ejemplo
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: vm-name
    default: on-demand-sg-vm
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
      # networkType must be private or outbound
      # if it's private, both inbound and outbound are blocked. if it's outbound, only inbound is blocked.
      # networkType existing does not make on-demand sg, it just deploys the machine
      networkType: private
      constraints:
        - tag: 'subnet-cidr:192.168.37.0/24'
```
