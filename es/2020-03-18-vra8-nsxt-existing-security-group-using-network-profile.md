# Parte 3 de la Serie Blog vRA 8 + NSX-T: Plantilla vRA 8 con Grupos de Seguridad Existentes (perfil de red)


Puede crear una plantilla vRA 8 para desplegar máquinas y ponerlas en un grupo de seguridad existente de NSX-T usando perfil de red. 

Nota que, con este proceso, todos los grupos de seguridad que selecciona en el perfil de red se aplicarán a las máquinas. Si tiene máquinas múltiples en una plantilla y quiere especificar grupo de seguridad diferente para cada máquina, tiene que usar etiqueta vRA o etiqueta de puerto de segmento (vea parte 4 y 5 de esta serie de blog por esos procesos).

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
* Grupos de seguridad configurados


## Información General de Proceso 
1. Cree o modifique un perfil de red.  
2. Seleccione grupo(s) de seguridad existente(s) que va a hacer usando. 
3. Cree una plantilla con los objectos: "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX).

Paso opcional:
* Cree entrada en la plantilla para personalizar el nombre de la máquina.


## Ejemplo

### Configure el Perfil de Red
1. Vaya a “Infraestructura” > “Perfiles de Red” (en el menú “Configurar”) y de clic en el botón “+ NUEVO PERFIL DE RED” (o puede revisar un perfil de red existente). 
2. Escoge la cuenta o región y nombre el perfil.
3. Configure redes existentes o redes bajo demanda.
4. Vaya a “Grupos de seguridad” y de clic en el botón “+ AGREGAR GRUPO DE SEGURIDAD”.
{{<image src="step4.png" linked="true">}}
5. Seleccione grupo(s) de seguridad que va a hacer aplicar.
{{<image src="step5.png" linked="true">}}
6. De clic en el botón “CREAR” para guardar el perfil.

### Cree y Configure la Plantilla
7. Vaya a “Diseño”, de clic en el botón “NOVEDADES DE” y de clic en el botón “Lienzo en blanco” para creer una nueva plantilla.
8. Nombre la plantilla y escoge el proyecto.
9. Pon la "Cloud Agnostic Machine" (máquina agnóstica de nube) y "NSX Network" (red NSX) en el lienzo en blanco. <b>Nota que no necesita un objeto “Security Group" (grupo de seguridad) en la plantilla. </b>
10. Conecta "Cloud Agnostic Machine" a "NSX Network" en el lienzo en blanco.
11. A la derecha en el código YAML, escoge una imagen y un tipo para la máquina. 
12. Debajo de `-network:`, agregue una línea `assignment: static` para dar una dirección IP estática a la máquina.
13. Para la “red NSX”, cambie `networkType` de `properties` dependiendo de si está usando una red existente o una red bajo demanda. En este ejemplo, estoy usando una red bajo demanda.  
{{<image src="step13.png" linked="true">}}
14. De clic en el botón “PROBAR”.
15. De clic en el botón “IMPLEMENTAR” a crear una nueva implementación.
16. Entra el nombre de la nueva implementación, escoge “Borrador actual” y de clic en el botón “IMPLEMENTAR”.

### Verifique la Implementación 
17. Cuando la implementación está completa, vaya a “Implementaciones” en vRA y nota la dirección IP de la nueva implementación.
{{<image src="step17.png" linked="true">}}
18. Inicie una sesión en el "NSX-T UI" (el cliente web de NSX-T) y vaya a "Inventory" > "Groups".
{{<image src="step18.png" linked="true">}}
19. De clic en el botón “Ver miembros” del “grupo de seguridad” que usó en el lienzo en blanco.
20. De clic en el botón “Direcciones IP” y puede ver la dirección IP de la implementación. 
{{<image src="step20.png" linked="true">}}
