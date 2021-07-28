# Parte 7 de la Serie Blog vRA 8 + NSX-T: Plantilla con 'One-Arm Load Balaner' (equilibrador de carge) Existente de NSX-T


Puede crear una plantilla vRA Cloud para desplegar máquinas y ponerlas detrás de en un "one-arm load balancer" existente de NSX-T. Este proceso no crea los enrutadors lógicos bajo demanda. El proceso usa un "one-arm load balancer" existente y simplemente agregar "virtual server" (servidor virtual), "server pool" (grupo de servidores), y "application profile" (perfil de aplicación). Puede crear un "monitor" (monitor de máquina virtual) también si quiere. Este proceso funciona con vRA 8.1, pero yo usé vRA Cloud para crear el ejemplo. 

Abajo es una diagrama simple de "one-arm load balancer" de NSX-T.
{{<image src="diagram.png" linked="true">}}

Nota que vRA 8.1 no son totalmente compatible el "policy API" de NSX-T. Entonces, si intenta a crear una plantilla usando un enrutador lógico tier-0 de "policy API", la implementación de la plantilla no sera exitosa. Va a recibir una mensaje de error: `[289] [Principal 'admin' with role '[enterprise_admin]' attempts to delete or modify an object of type LoadBalancerService it doesn't own. (createUser=nsx_policy, allowOverwrite=null)]`
{{<image src="error.png" linked="true">}}


## Versiones de Productos (Demo)
* VCF 3.9.1.0-15345960 (vSphere 6.7, NSX-V 6.4.6)
* NSX-T 2.5.0
* vRA Cloud

## Prerrequisitos
vRA Cloud:
* Cuenta conectada de NSX-T
* Infraestructura básica configurada (proyectos, zonas de nube, "image mappings" o asignaciones de imagen, "flavor mappings" o asignaciones de tipo)

NSX-T:
* enrutador lógico tier-0 configurado
* (non-policy-API) enrutador lógico tier-1 configurado
* "edge cluster" (los nodos NSX edge de tamaño mediano mínimo)


## Información General de Proceso
1. Cree un "one-arm load balancer" de NSX-T.
2. Cree un perfil de red que usa el "one-arm load balancer" de NSX-T existente. 
3. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube), Cloud Network" (red de nube), "Cloud Load Balancer" (equilibrador de carga de nube).

Pasos opcionales:
* Cree entradas en la plantilla para personalizar el nombre de la máquina.
* Cree múltiples "load balancers" y aplicar una etiqueta en la plantilla para desplegar los servicios de "load balancing" usando un "load balancer" específico. 

## Ejemplo

### Cree el "One-Arm Load Balancer" de NSX-T
1. Inicie una sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Advanced Networking & Security" > "Networking" > "Load Balancers".
2. De clic en el botón "+" para crear un nuevo "load balancer".
{{<image src="step2.png" linked="true">}}
3. De clic en el nuevo "load balancer" y de clic en el botón "EDIT".
{{<image src="step3.png" linked="true">}}
4. Seleccione un enrutador lógico tier-1 a provisar los servicios de "load balancing". El enrutador lógico tier-1 no puede ser un enrutador de "policy-API". Entonces, si no tiene un enrutador lógico tier-1 de "Management API", vaya a "Advanced Networking & Security" > "Networking" > "Routers" y cree un enrutador lógico tier-1.
{{<image src="step4.png" linked="true">}}
5. No tiene que configurar servidor virtual o grupos de servidores ahora. La plantilla va a configurar el "load balancer". 

### Configure el Perfil de Red
6. Vaya a "Infraestructura" > "Perfiles de Red" (en el menú "Configurar") y de clic en el botón "+ NUEVO PERFIL DE RED" (o puede revisar un perfil de red existente).
7. Escoge la cuenta o región y nombre el perfil.
8. Configure redes existentes o redes bajo demanda. En este ejemplo, voy a usar un red existente de NSX-T.
9. Cuando está usando un "load balancer" existente en la plantilla, no tiene que seleccionar un enrutador lógico tier-0 o un clúster de edge en la sección "Directivas de Red". 
10. Vaya a "Load Balancers", de clic en el botón "+ ADD LOAD BALANCER" y escoge el "load balancer" existente.
{{<image src="step10.png" linked="true">}}

### Cree y Configure la Plantilla
11. Vaya a "Diseño", de clic en el botón "NOVEDADES DE" y de clic en el botón "Lienzo en blanco" para creer una nueva plantilla.
12. Nombra la plantilla y escoge el proyecto.
13. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube), "Cloud Network" (red de nube) y "Cloud Load Balancer" (equilibrador de carga de nube) en el lienzo en blanco. 
14. Conecta la "Cloud Agnostic Machine" y "Cloud Load Balancer" a "Cloud" Network" en el lienzo en blanco. También conecta la "Cloud Agnostic Machine" a "Cloud Load Balancer". 
{{<image src="step14.png" linked="true">}}
15. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. Agregue `count` para indicar cuantas máquina quiere desplegar.
16. Debajo de `- network: `, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
17. Para la "Cloud network", asegúrese que dice `networkType: existing` debajo de `properties`. Debajo de `networkType`, agregue una línea `constraints:` y otra línea `- tag:` para escoger cual red existente quiere usar.
18. Configure el "load balancer". `protocol` y `port` debajo de `routes` son requeridos. `healthCheckConfiguration` es opcional - si proporciona detalles, la plantilla va a crear un nuevo monitor. Sino, la plantilla solamente va a crear el servidor virtual, grupo de servidores, y perfil de aplicación.
{{<image src="step18.png" linked="true">}}
19. De clic en el botón "PROBAR".
20. De clic en el botón "IMPLEMENTAR" para crear una nueva implementación.
21. Entra el nombre de la nueva implementación, escoge "Borrador actual" y de clic en el botón "IMPLEMENTAR".

### Verifique la Implementación
22. Supervise el progreso de la implementación. Cuando está completa, puede ver la implementación en la sección de vRA Cloud Assembly "Implementaciones".
{{<image src="step22.png" linked="true">}}
23. Inicie sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Advanced Networking & Security". Nota que no puede verificar la implementación en el" NSX-T Simplified UI" (el cliente web simple de NSX-T). Puede ver que el "load balancer" existente ahora tiene un servidor virtual. 
{{<image src="step23.png" linked="true">}}
24. Si puede de clic en el servidor virtual, puede ver los "port" y "protocol".
{{<image src="step24.png" linked="true">}}
25. Puede ver las máquinas creadas de la plantilla si de clic en el grupo de servidores y ver los miembros. 
{{<image src="step25.png" linked="true">}}
26. De clic en los perfiles y puede ver el perfil de aplicación. 
{{<image src="step26.png" linked="true">}}
27. La plantilla tiene la propiedad `healthCheckConfiguration`. Entonces, puede ver un nuevo monitor creado.
{{<image src="step27.png" linked="true">}}

Nota que en mi ejemplo, el grupo de servidores no está funcionando porque no abrí el "port" que las máquinas necesitan para comunicar. 

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

### (Opcional) Múltiples "load balancers" en el Perfil de Red
Si tiene múltiples "load balancers" en el perfil de red, puede agregar una etiqueta en cada "load balancer" y usa la etiqueta en la plantilla para escoger el "load balancer" existente.
* Vaya al perfil de red y agregue una etiqueta en cada "load balancer". 
{{<image src="optional-1.png" linked="true">}}
* Agregue una etiqueta de "constraint" en la plantilla para indicar cual "load balancer" quiere usar.
{{<image src="optional-2.png" linked="true">}}
* Cuando comienza una nueva implementación, puede ver en los detalles de la implementación o en la diagrama de la implementación cuando se usa la etiqueta de "constraint".
{{<image src="optional-3.png" linked="true">}}
* Cuando la implementación está completa, inicie sesión en el "NSX-T UI" (el cliente web de NSX-T) y puede verificar que la plantilla utilizó el "load balancer" existente lo que queria.
{{<image src="optional-4.png" linked="true">}}

### Código YAML de la Plantilla de Ejemplo Opcional
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
