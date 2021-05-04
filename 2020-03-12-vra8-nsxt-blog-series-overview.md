# vRA 8 + NSX-T Blog Series Overview


With the recent release of vRA 8.0, I have been receiving many questions from customers about how vRA 8 can leverage NSX-T. 

This blog series will focus on demonstrating how you can create vRA 8 blueprints that utilize NSX-T constructs. 

Here is a list of blogs that I am planning on creating for this series (as of March 15, 2020):
* [Part 1: vRA 8 Blueprint with Existing NSX-T Networks][part1-link]
* [Part 2: vRA 8 Blueprint with On-demand Routed NSX-T Networks][part2-link]
* [Part 3: vRA 8 Blueprint with Existing Security Groups (network profile)][part3-link]
* [Part 4: vRA 8 Blueprint with Existing Security Groups (vRA tag)][part4-link]
* [Part 5: vRA 8 Blueprint with Existing Security Groups (segment port tag)][part5-link]
* [Part 6: vRA 8 Blueprint with On-demand Security Groups][part6-link]
* [Part 7: vRA 8 Blueprint with Existing NSX-T One-Arm Load Balancer][part7-link]
* [Part 8: vRA 8 Blueprint with On-demand NSX-T One-Arm Load Balancer][part8-link]
* ~~Part 9: vRA 8 Blueprint with On-demand NAT NSX-T Networks~~

Note: the list of blogs is subject to change. Last updated December 12, 2020.

<hr>

I set up a nested lab to create the examples included in this blog series.

## Product Versions
* vSphere 6.5 U3
* vRA 8.0.1 (including vRSLCM and vIDM)
* NSX-T 2.5.1
* vSAN 6.6.1

## vSphere Infrastructure Overview
2 vSAN clusters, enabled with DRS and HA:
* Management cluster - 4 ESXi hosts
* Compute/edge cluster - 3 ESXi hosts

The management cluster hosts the following appliances:
* vCSA 
* vRA appliance
* vRSLCM appliance 
* vIDM appliance 
* NSX-T manager

The compute/edge cluster hosts all the workloads and the NSX-T edge VMs.



[part1-link]: https://288clouds.com/2020-03-13-vra8-blueprint-nsxt-existing-network.html
[part2-link]: https://288clouds.com/2020-03-16-vra8-blueprint-nsxt-on-demand-routed-network.html
[part3-link]: https://288clouds.com/2020-03-18-vra8-nsxt-existing-security-group-using-network-profile.html
[part4-link]: https://288clouds.com/2020-03-19-vra8-nsxt-existing-security-group-using-vra-tags.html
[part5-link]: https://288clouds.com/2020-03-20-vra8-nsxt-existing-security-group-using-segment-port-tags.html
[part6-link]: https://288clouds.com/2020-03-23-vra8-nsxt-on-demand-security-group.html
[part7-link]: https://288clouds.com/2020-07-20-vracloud-nsxt-existing-one-arm-load-balancer.html
[part8-link]: https://288clouds.com/2020-07-22-vracloud-nsxt-on-demand-one-arm-load-balancer.html
