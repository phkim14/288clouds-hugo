# Parte 8 de la Serie Blog vRA 8 + NSX-T: Plantilla con 'One-Arm Load Balaner' (equilibrador de carge) Bajo Demanda de NSX-T

Puede crear una plantilla vRA Cloud para desplegar máquinas y ponerlas detrás de en un "one-arm load balancer" de NSX-T bajo demanda. Este proceso crea un enrutador lógico tier-1 bajo demanda y agrega los servicios de equilibrador de carga. Este proceso funciona con vRA 8.1, pero yo usé vRA Cloud para crear el ejemplo. 

Abajo es una diagrama simple de "one-arm load balancer" de NSX-T.
{{<image src="diagram.png" linked="true">}}

Nota que vRA 8.1 no es totalmente compatible con el "policy API" de NSX-T. Creo que empezando con vRA 8.2, vRA compatible con el "policy API" de NSX-T. 

## Versiones de Productos (Demo)
* VCF 3.9.1.0-15345960 (vSphere 6.7, NSX-V 6.4.6)
* NSX-T 2.5.0
* vRA Cloud

## Prerrequisitos
vRA Cloud:
* Cuenta conectada de NSX-T
* Infraestructura básica configurada (proyectos, zonas de nube, "image mappings" o asignaciones de imagen, "flavor mappings" o asignaciones de tipo)

NSX-T:
* enrutador lógico tier-0 configurado (puede ser un enrutador de "policy-API")
* "edge cluster" (los nodos NSX edge de tamaño mediano mínimo)


## Información General de Proceso
1. Cree un perfil de red que usa el "one-arm load balancer" de NSX-T bajo demanda.
2. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube), Cloud Network" (red de nube), "Cloud Load Balancer" (equilibrador de carga de nube).

Pasos opcionales:
* Cree entradas en la plantilla para personalizar el nombre de la máquina.


## Ejemplo

### Configure el Perfil de Red
1. En vRA UI, vaya a "Infraestructura" > "Perfiles de Red" (en el menú "Configurar") y de clic en el botón "+ NUEVO PERFIL DE RED" (o puede revisar un perfil de red existente).
2. Escoge la cuenta o región y nombre el perfil.
3. Configure redes existentes o redes bajo demanda. En este ejemplo, voy a usar un red existente de NSX-T.
4. Vaya a "Network Policies" y escoge un enrutador lógico tier-0 y un "edge cluster". Estos son obligatarios para desplegar un equilibrador de carga bajo demanda porque la plantilla crea un enrutador lógico tier-1 y conectalo al enrutador lógico tier-0 en el proceso. 
{{<image src="step4.png" linked="true">}}
5. Vaya a "Load Balancers" y asegurese que no hay equilibradores de carga en la lista. Si hay equilibradores de carga aquí, la plantilla no va a crear un equilibrador de carga ba jo demanda. La plantilla va a usar uno de los equilibradores de carga.

### Cree y Configure la Plantilla
6. Vaya a "Diseño", de clic en el botón "NOVEDADES DE" y de clic en el botón "Lienzo en blanco" para creer una nueva plantilla.
7. Nombra la plantilla y escoge el proyecto.
8. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube), "Cloud Network" (red de nube) y "Cloud Load Balancer" (equilibrador de carga de nube) en el lienzo en blanco. 
9. Conecta la "Cloud Agnostic Machine" y "Cloud Load Balancer" a "Cloud" Network" en el lienzo en blanco. También conecta la "Cloud Agnostic Machine" a "Cloud Load Balancer".
{{<image src="step9.png" linked="true">}}
10. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. Agregue `count` para indicar cuantas máquina quiere desplegar.
11. Debajo de `- network: `, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
12. Para la "Cloud network", asegúrese que dice `networkType: existing` debajo de `properties`. Debajo de `networkType`, agregue una línea `constraints:` y otra línea `- tag:` para escoger cual red existente quiere usar.
13. Configure el "load balancer". `protocol` y `port` debajo de `routes` son requeridos. `healthCheckConfiguration` es opcional - si proporciona detalles, la plantilla va a crear un nuevo monitor. Sino, la plantilla solamente va a crear el servidor virtual, grupo de servidores, y perfil de aplicación.
{{<image src="step13.png" linked="true">}}
14. De clic en el botón "PROBAR".
15. De clic en el botón "IMPLEMENTAR" para crear una nueva implementación.
16. Entra el nombre de la nueva implementación, escoge "Borrador actual" y de clic en el botón "IMPLEMENTAR".

### Verifique la Implementación
17. Supervise el progreso de la implementación. Cuando está completa, puede ver la implementación en la sección de vRA Cloud Assembly "Implementaciones".
{{<image src="step17.png" linked="true">}}
18. Inicie sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Advanced Networking & Security" > "Networking" > "Routers". Puede ver que el enrutador lógico creado.
{{<image src="step18.png" linked="true">}}
19. Vaya a "Advanced Networking & Security" > "Networking" > "Load Balancers" y puede ver el equilibrador de carga creado. 
{{<image src="step19.png" linked="true">}}
20. Si puede de clic en el servidor virtual, puede ver los "port" y "protocol".
{{<image src="step20.png" linked="true">}}
21. Puede ver las máquinas creadas de la plantilla si de clic en el grupo de servidores y ver los miembros.
{{<image src="step21.png" linked="true">}}
22. De clic en los perfiles y puede ver el perfil de aplicación. 
{{<image src="step22.png" linked="true">}}


### Código YAML de la Plantilla de Ejemplo
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
