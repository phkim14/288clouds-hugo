# Parte 2 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Redes NSX-T Enrutables Bajo Demanda


Puede crear una plantilla vRA 8 que crear las redes NSX-T enrutables bajo demanda y desplegar máquinas conectadas a las nuevas redes creadas. Cuando está usando la plantilla para crear red NSX enrutable bajo demanda, en realidad, vRA 8 crea un enrutador lógico tier-1 y lo conecta a un enrutador lógico tier-0 existente. Después, vRA 8 crea una red NSX-T bajo demanda y la conecta al enrutador lógico tier-1.


## Versiones de Productos (Demo)
* vSphere 6.5 U3
* vRA 8.0.1 (incluyendo vRSLCM y vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## Prerrequisitos
vRA 8:
* Cuenta conectada de NSX-T
* Infraestructura básica configurada (proyectos, zonas de nube, "image mappings" o asignaciones de imagen, "flavor mappings" o asignaciones de tipo)

NSX-T:
* enrutador lógico tier-0 configurado 


## Información General de Proceso 
1. Cree un perfil de red que crea redes bajo demanda. 
2. Escoge enrutador lógico tier-0 y el clúster de los nodos NSX Edge en perfil de red.
3. Provea los detalles para la red a petición (por ejemplo, "CIDR", tamaño de subred, etc.)
4. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX).
5. Escoge `routed` (rutable) para el tipo de red. 

Paso opcional:
* Cree entrada en la plantilla para personalizar el nombre de la máquina y el nombre de la red.


## Ejemplo

### Configure el Perfil de Red
1. Vaya a “Infraestructura” > “Perfiles de Red” (en el menú “Configurar”) y de clic en el botón “+ NUEVO PERFIL DE RED”.
2. Escoge la cuenta o región y nombre el perfil. 
3. Vaya a “Directivas de red” y seleccione “Red a petición”
4. Escoge la zona de transporte.  
5. Entre "CIDR".
6. Seleccione tamaño de subred. 
7. Seleccione tipo de asignación de rangos de IP. Este ejemplo usa asignación estática porque no hay "DHCP" en el laboratorio. 
8. En la sección “Recursos de red”, seleccione enrutador lógico tier-0. 
9. En la sección “Recursos de red”, seleccione clúster de NSX Edge. 
10. De clic en el botón “CREAR” para guardar el perfil.
{{<image src="step10.png" linked="true">}}

### Cree y Configure la Plantilla
11. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
12. Nombra la plantilla y escoge el proyecto.
13. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco.
14. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco.
15. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. 
16. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
17. Para la "NSX network", asegúrese que dice `networkType: routed` debajo de `properties`.
{{<image src="step17.png" linked="true">}}
18. De clic en el botón “PROBAR”.
19. De clic en el botón “IMPLEMENTAR” para crear una nueva implementación.
20. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.

### Verifique la Implementación 
21. Inicie sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a “Advanced Networking & Security”. Nota que no puede verificar la implementación en el "NSX-T Simplified UI" (el cliente web simple de NSX-T).
22. Vaya a “Networking” > “Switching”, puede ver segmento bajo demanda que ha creado de la plantilla.
{{<image src="step22.png" linked="true">}}
23. Vaya a “Networking” > “Routers”, puede ver enrutador lógico tier-1 que ha creado y conectado al enrutador lógico tier-0 que seleccionó en el perfil de red.
{{<image src="step23.png" linked="true">}}

En el "UI" (el cliente web) de vRA Cloud Assembly, puede ver la implementación completa en “Implementaciones”. Puede ver también la nueva máquina creada y la nueva red NSX-T creada en el cliente web de vSphere.

### Código YAML de la Plantilla de Ejemplo
```
formatVersion: 1
inputs:
  vm-name:
    type: string
    title: name
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
<i>Crédito: Muchas gracias a mi colega, <a href="https://www.linkedin.com/in/pattonmichael/" target="_blank" rel="noopener noreferrer">Michael Patton</a>, para crear este ejemplo conmigo.</i> 
