# Common Questions around VCDX-NV


When I was starting my journey to achieve the VCDX-NV certification, I had a lot of questions. Now as a panelist in training and an occasional helper for VCDX workshops, I've noticed that many candidates have the same questions as the ones I've had when I was a candidate. 

So I decided it might be useful if I help answer some of those questions in a blogpost. Here we go!


## Common FAQs

### Should I use NSX-V or NSX-T in my design? 
This is entirely up to you. Of course you have to be technical and extremely knowledgeable with the product (NSX-V or NSX-T), but remember that VCDX is an exam for architects. It is not just about the technical details - you also have to demonstrate your skills as an architect.

VCDX certification is a challenging exam as it is so I recommend choosing whichever you have more design experience with / more comfortable with. 


### If I submit a NSX-V design, do I need to know NSX-T? 
The answer to this question has always been "no". However, with NSX-V retiring soon and NSX-T being more prevalent these days, I think you should know some of the basics of NSX-T even if you submit a NSX-V design.

The panelists will not ask you in-depth, technical details about NSX-T if you submit a NSX-V design, and vice versa if you submit a NSX-T design. However, as an architect, you should know some of the major differences between the two products and be able to discuss some of the implications of deploying NSX-V in year 2020 when the End of Support date is in January 2022.


### How much information about vSphere do I need to include? 
You should include as much information about vSphere as it relates to your NSX design, especially if you are using NSX-V. 

Unlike the VCDX-DCV exam, you don't have to go into details about every single feature/configuration in vSphere or provide in-depth details on resource planning. However, you should provide enough details to show how the vSphere infrastructure is supporting your NSX infrastructure and how specific design decisions you've made for vSphere affect your NSX design (i.e. availability of NSX managers, VDS/N-VDS, etc.).


### How much information about storage do I need to include? 
This one was a big questions for me as storage is my weakest knowledge area. 

Similar to the question about vSphere, my answer is to include as much information about storage as it relates to your NSX design. Whether you're using NSX-V or NSX-T, both products consist of virtual machines, and (as we all know) virtual machines need storage. 

You don't have to go in-depth with details on storage processors, disk pools, disk configurations, and such, as you would for a VCDX-DCV submission. But you should know what type of storage is being used, what kind of connections you have to storage, etc. and how certain aspects of storage design may affect your design in terms of design qualities (AMPRS). 

Note: AMPRS = Availability, Manageability, Performance, Recoverability, Security


### How in-depth do I need to be with the physical network details? 
"Physical network" is its own section in entirety if you look at the VCDX-NV exam blueprint so this is an important area. 

Be as detailed as you can with the physical network and include as much details as they relate to your NSX design. You don't have to list every single port configuration, ACLs, or HSRP/vPC configurations, but you want to include any and all details if they affect the NSX design. 

A good example would be including details on vPC configuration if you are going to configure dynamic routing between NSX-V edges and TOR switches over vPCs. 


### How much do I have to know about the vRealize products? 
If you include any vRealize products (i.e. vRA, vRO, vRLI, vRNI) in your design submission, you need to be prepared to answer any questions regarding these products as they relate to NSX. This goes for any products, not just vRealize products. 

For example, if you include in your design that vRealize Automation will be used to consume NSX-T, you should know what you can and cannot do with vRealize Automation regarding NSX-T consumption. 


### Do I need screenshots in the supporting documents?
Although I've heard many different answers to this question, my answer to this is yes. I included screenshots in the step-by-step installation guide when I created my documents for the VCDX-NV submission.

I don't think this is a hard written requirement to get your design submission accepted and be invited to the defense, but you're already spending hundreds of hours on the documents... you should might as well spend couple more hours and include some screenshots and avoid getting rejected for not having screenshots, in case it is a hard requirement.
