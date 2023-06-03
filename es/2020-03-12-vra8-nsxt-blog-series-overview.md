# Introducción a la Serie: vRA 8 + NSX-T


Con la reciente introducción de vRA 8.0, he estado recibido muchas preguntas de clientes acerca como usar NSX-T con vRA 8.

Esta serie de blog posts se va a enfocar en mostrar cómo crear plantillas vRA 8 que utilizan objectos NSX-T.

Esta es la lista de blogs que crearé para esta serie (a partir de 15 de marzo del 2020):
* [Parte 1: Plantilla vRA 8 con Redes NSX-T Existentes][part1-link]
* [Parte 2: Plantilla vRA 8 con Redes NSX-T Enrutables Bajo Demanda][part2-link]
* [Parte 3: Plantilla vRA 8 con Grupos de Seguridad Existentes (perfil de red)][part3-link]
* [Parte 4: Plantilla vRA 8 con Grupos de Seguridad Existentes (etiqueta vRA)][part4-link]
* [Parte 5: Plantilla vRA 8 con Grupos de Seguridad Existentes (etiqueta de puerto de segmento)][part5-link]
* [Parte 6: Plantilla vRA 8 con Grupos de Seguridad Bajo Demanda][part6-link]
* [Parte 7: Plantilla vRA 8 con “One-Arm Load Balancer” NSX-T Existente][part7-link]
* [Parte 8: Plantilla vRA 8 con “One-Arm Load Balancer” NSX-T Bajo Demanda][part8-link]
* ~~Parte 9: Plantilla vRA 8 con Redes NSX-T Bajo Demanda~~

Nota: la lista de blogs puede cambiar sin previo aviso. Última actualización el 12 de diciembre del 2020. 

<hr>

He creado un laboratorio anidado para crear los ejemplos incluidos en esta serie de blog posts. 

## Versiones de Productos (Demo)
* vSphere 6.5 U3
* vRA 8.0.1 (incluyendo vRSLCM y vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## Información General acerca de la Infraestructura vSphere
2 clústers vSAN, habilitados con DRS y HA: 
* Clúster de administración – 4 hosts ESXi
* Clúster de “Compute/Edge” – 3 hosts ESXi

El clúster de administración tiene las siguientes máquinas:
  * vCenter (vCSA) 
  * Máquina virtual para vRA
  * Máquina virtual para vRSLCM
  * Máquina virtual para vIDM
  * NSX-T manager

El clúster “compute/edge” contiene todas las cargas de trabajo y las machinas virtuales “NSX-T Edge”.


[part1-link]: https://288clouds.com/es/2020-03-13-vra8-blueprint-nsxt-existing-network.html
[part2-link]: https://288clouds.com/es/2020-03-16-vra8-blueprint-nsxt-on-demand-routed-network.html
[part3-link]: https://288clouds.com/es/2020-03-18-vra8-nsxt-existing-security-group-using-network-profile.html
[part4-link]: https://288clouds.com/es/2020-03-19-vra8-nsxt-existing-security-group-using-vra-tags.html
[part5-link]: https://288clouds.com/es/2020-03-20-vra8-nsxt-existing-security-group-using-segment-port-tags.html
[part6-link]: https://288clouds.com/es/2020-03-23-vra8-nsxt-on-demand-security-group.html
[part7-link]: https://288clouds.com/es/2020-07-20-vracloud-nsxt-existing-one-arm-load-balancer.html
[part8-link]: https://288clouds.com/es/2020-07-22-vracloud-nsxt-on-demand-one-arm-load-balancer.html
