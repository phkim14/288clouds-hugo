# Personalice Nombres de Máquinas y Redes Bajo Demanda


La plantilla vRA 8 da nombres y números aleatorios a las máquinas y redes NSX-T bajo demanda en implementaciones si no especifica nomenclatura personalizada. Este blog va a demostrar cómo puede personalizar nombres de su máquinas y redes NSX-T cuando son creados de una plantilla vRA 8. 


## Versiones de Productos (Demo)
* vSphere 6.5 U3
* vRA 8.0.1 (incluyendo vRSLCM y vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## Prerrequisitos
vRA 8:
* Cuenta conectada de NSX-T
* Infraestructura básica configurada (proyectos, zonas de nube, "image mappings" o asignaciones de imagen, "flavor mappings" o asignaciones de tipo)

NSX-T (si quiere personalizar el nombre del red bajo demanda):
* enrutador lógico (tier-0 o tier-1) configurado 


## Información General de Proceso 
1. En el proyecto, cree nomenclatura personalizada. 
2. En la plantilla, cree una entrada personalizada para cada objeto que quiere nombrar. 
3. Use nombre de variable de entrada para editar propiedad `name` de máquina o red.

## Ejemplo

### Cree Nomenclatura Personalizada
1. Vaya a “Infraestructura” > “Proyectos” (en el menú “Configurar”) y de clic en el proyecto que quiere agregar nomenclatura personalizada.
2. Vaya a sección “Aprovisionamiento”.
3. En la sección “Propiedades personalizadas”, especifique una plantilla de nomenclatura: `${resource.name}-${##}`
{{<image src="step3.png" linked="true">}}

### Cree Entradas Personalizadas de Plantilla 
4. Cree una nueva plantilla y pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco.
5. Configure la plantilla como la imagen (por ejemplo, imagen de máquina, tipo de máquina, asignación de redes de máquina, tipo de red, etc.).
{{<image src="step5.png" linked="true">}}
6. Cree un variable `vm-name` en la sección `inputs` para personalizar el nombre de máquina.
7. Escoge el tipo `string`.
8. Entra `title` de variable que la plantilla utilizará a preguntar el usuario por el valor de entrada cuando implementa la plantilla.
9. Entra el valor de forma predeterminada para la propiedad `default:` de este variable.
10. Haga lo mismo para un variable `network-name` en la sección `inputs` a personalizar nombre de red.
{{<image src="step10.png" linked="true">}}
Nota: Puede creer entradas personalizadas en la sección “Entradas” usando en el "UI" (el cliente web) de vRA Cloud Assembly. 
11. Agregue propiedades `name` para máquina y red a usar valores de entradas. 
`name: '${input.<insert input variable name>}'`
{{<image src="step11.png" linked="true">}}

### Verifique la Implementación 
Ahora si implementa esta plantilla, máquina y red se crean con nombres personalizados. 
{{<image src="step12.png" linked="true">}}
{{<image src="step12-1.png" linked="true">}}

### Código YAML de la Plantilla de Ejemplo 
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
<i>Crédito: Muchas gracias a mi colega, <a href="https://www.linkedin.com/in/pattonmichael/" target="_blank" rel="noopener noreferrer">Michael Patton</a>, para crear este ejemplo conmigo.</i> 
