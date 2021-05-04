# Parte 1 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Redes NSX-T Existentes


Puede crear una plantilla vRA 8 para desplegar máquinas conectadas a redes NSX-T existentes. 

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
* enrutador lógico (tier-0 o tier-1) configurado 
* red lógico conectado a un enrutador lógico


## Información General de Proceso 
1. Configure las redes existentes con rangos de IP que desea utilizar para las VMs (máquinas virtuales).
2. Cree un perfil de red.
3. Agregue las redes existentes que desea utilizar en el perfil de red.
4. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX).
5. Especifique cual red que desea utilizar agregando una etiqueta de restricción en el objeto "NSX Network" (red NSX). 

Pasos opcionales:
* Cree etiquetas para las redes existentes con los nombres del deseo.
* Cree entrada en la plantilla para personalizar el nombre de la máquina.


## Ejemplo

### Configure el Rango de IP para Red Existente
1. Inicie sesión en el UI (interfaz de usario) de vRA Cloud Assembly.
2. Vaya a “Infraestructura” > “Redes” (en el menú “Recursos”).
3. De clic en la red existente que desea utilizar y asegúrese que toda la información está completa. 
{{<image src="step3.png" linked="true">}}
4. De clic en la "checkbox" (la casilla de verificación) al lado de la red existente que desea utilizar y de clic en el botón “ADMINISTRAR RANGOS DE IP”.
{{<image src="step4.png" linked="true">}}
5. De clic en el botón “+ NUEVO RANGO DE IP”.
6. Cree rango de IP que desea utilizar para dar las direcciones IP a las máquinas implementadas de la plantilla.
{{<image src="step6.png" linked="true">}}
7. Revise otra vez la primera y última direccion IP en el rango creado para asegurar que están correctas.
{{<image src="step7.png" linked="true">}}

### Configure el Perfil de Red 
8. Vaya a “Infraestructura” > “Perfiles de Red” (en el menú “Configurar”) y de clic en el botón “+ NUEVO PERFIL DE RED”. 
{{<image src="step8.png" linked="true">}}
9. Escoge la cuenta o región y nombre el perfil. 
{{<image src="step9.png" linked="true">}}
10. Seleccione “Redes” y de clic en el botón “+ AGREGAR RED”.
{{<image src="step10.png" linked="true">}}
11. Escoge la red existente que desea utilizar en la plantilla y agréguela. 

### Cree la Plantilla
12. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
13. Nombra la plantilla y escoge el proyecto. 
{{<image src="step13.png" linked="true">}}
14. Arrastra "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) al lienzo en blanco.  
{{<image src="step14.png" linked="true">}}
15. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco. 
{{<image src="step15.png" linked="true">}}

### Configure la Plantilla
16. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. Un menú de las opciones disponibles aparecerá cuando de clic en el espacio entre las comillas.
{{<image src="step16.png" linked="true">}}
17. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina desde el rango de IP. 
18. Para la "NSX network", asegúrese que dice `networkType: existing` debajo de `properties`. 
19. Debajo de `networkType`, agregue una línea `constraints` y otra línea `-tag:` para escoger la red existente que desea utilizar. 
{{<image src="step19.png" linked="true">}}
20. De clic en el botón “PROBAR”.
{{<image src="step20.png" linked="true">}}
21. De clic en el botón “IMPLEMENTAR” para crear una nueva implementación.
22. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.
{{<image src="step22.png" linked="true">}}

### Verifique la Implementación 
23. Supervise el progreso de la implementación. Cuando está completa, puede ver la implementación en la sección de vRA Cloud Assembly “Implementaciones” y la nueva máquina creada en el cliente web de vSphere. 
{{<image src="step23.png" linked="true">}}
{{<image src="step23-1.png" linked="true">}}
{{<image src="step23-2.png" linked="true">}}

### Código YAML de la Plantilla de Ejemplo 
```
formatVersion: 1
inputs: {}
resources:
  Cloud_Machine_1:
    type: Cloud.Machine
    properties:
      image: 'centos-temp'
      flavor: 'small'
      networks:
        - network: '${resource.Cloud_NSX_Network_1.id}'
          assignment: static
  Cloud_NSX_Network_1:
    type: Cloud.NSX.Network
    properties:
      networkType: existing
      constraints: 
        - tag: subnet-cidr: 192.168.34.0/24
```
