# Considerations When Using Layer 2 Bridges for NSX-V to NSX-T Migration


# ~ Este post será traducido al español próximamente ~


I recently had a customer who asked for some general feedback on their high-level plan to migrate from NSX-V to NSX-T using layer 2 bridges. Although every environment is different and the details of migration procedures should be tailored to each environment, I thought I'd share the general guidance I gave to this customer in case there are some points here that may be transferrable to other similar NSX-V to NSX-T migration plans. 


## Migration Plan Overview
1. Deploy a greenfield NSX-T environment.
2. Configure layer-2 bridges in both NSX-V and NSX-T environments. 
3. Migrate workloads to NSX-T environment. 
4. Decomission NSX-V environment & remove L2 bridges in NSX-T environment.

## Considerations

### Capacity
Ensure that layer 2 bridge instances in both NSX-V and NSX-T enviroments are able to handle expected traffic flow.

- In NSX-V, bridging instance gets enabled on the ESXi host where NSX-V DLR control VM is running; if multiple sets of layer 2 bridging instances are needed, try to spread bridging load across different ESXi hosts.
- In NSX-T, consider deploying NSX-T edge node VMs dedicated to bridging. 

### Availability
- Create redundancy in layer 2 bridges in both NSX-V and NSX-T environments.
- Estimate the downtime required for the network cutover from changing the default gateways after workloads are migrated to the NSX-T environment.

### Micro-segmentation
Ensure the distributed firewall rules and security constructs in NSX-V that are in use can be applied in NSX-T.

- NSX-T DFWs cannot use vCenter objects; IP sets work in both NSX-V and NSX-T – consider changing all the security constructs to IP sets for the migration.  
- If you are using security tags, they must be reapplied to the VMs after vMotion to NSX-T.
- When migrating the DFW rules, consider using scripting/APIs since NSX-V DFW is in XML and NSX-T DFW is in JSON or do it manually. 

### NSX-T Layer 2 Bridging Requirements
- Keep in mind that Layer 2 bridging requires allow forged transmit, MAC learning, and Virtual Guest Tagging (VGT). Refer to the official documentation for more details.
- Design the future state NSX-T architecture so that it is flexible enough to support temporary layer 2 bridges during the migration (i.e. N-VDS design, edge node sizing, etc.). 

### Sequence of Events
The steps in the detailed plan should be carefully sequenced to ensure success of the migration plan.

- Ensure NSX-T T1 router and NSX-V DLR are not using the same MAC address - instantiate NSX-T T1 router with a different MAC address by creating a transport zone with an API call, setting “nested=true”. Refer to the official NSX-T API guide for details.
- To minimize or eliminate any bridged traffic, prioritize the workloads to be migrated and keep highly interactive application VMs together on the same site where possible (i.e. App and DB).
- Consider migrating subnet by subnet so that each time an entire subnet is moved, the default gateway can be changed from NSX-V ESG to NSX-T T1 router.
- Set up and test NSX-T environment before starting the migration.

