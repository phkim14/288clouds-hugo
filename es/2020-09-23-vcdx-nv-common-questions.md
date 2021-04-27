# Common Questions around VCDX-NV

# ~ Este post será traducido al español próximamente ~

When I was starting my journey to achieve the VCDX-NV certification, I had a lot of questions. Now after going through the certification process, I thought it might be useful if I help answer some of those questions in a blogpost. Here we go!


## Common FAQs

### Should I use NSX-V or NSX-T in my design? 
This is entirely up to you. Of course you have to be technical and extremely knowledgeable with the product (NSX-V or NSX-T), but remember that VCDX is an exam for architects. It is not just about the technical details - you also have to demonstrate your skills as an architect.

VCDX certification is a challenging exam as it is so I recommend choosing whichever you have more design experience with / more comfortable with. 


### If I submit a NSX-V design, do I need to know NSX-T? 
The answer to this question has always been "no". However, with NSX-V retiring soon and NSX-T being more prevalent these days, I think you should still know some of the basics of NSX-T even if you submit a NSX-V design. As an architect, I'm sure you have customers asking you what the differences are between the two products and why you might choose one over the other. 


### How much information about vSphere do I need to include? 
You should include as much information about vSphere as it relates to your NSX design, especially if you are using NSX-V. You should provide enough details to show how the vSphere infrastructure is supporting your NSX infrastructure and how specific design decisions you've made for vSphere affect your NSX design.


### How much information about storage do I need to include? 
Similar to the question about vSphere, my answer is to include as much information about storage as it relates to your NSX design. Whether you're using NSX-V or NSX-T, both products consist of virtual machines, and (as we all know) virtual machines need storage. You should know what type of storage is being used, what kind of connections you have to storage, etc. and how certain aspects of storage design may affect your design in terms of design qualities (AMPRS). 


### How in-depth do I need to be with the physical network details? 
"Physical network" is its own section in entirety if you look at the VCDX-NV exam blueprint so this is an important area. Be as detailed as you can with the physical network and include as much details as they relate to your NSX design. 


### How much do I have to know about the vRealize products? 
If you include any vRealize products (i.e. vRA, vRO, vRLI, vRNI) in your design submission, you need to be prepared to answer any questions regarding these products as they relate to NSX. This goes for any products, not just vRealize products. 


### Do I need screenshots in the supporting documents?
I've heard many different answers to this question - my answer to this is yes. Personally, I included screenshots in the step-by-step installation guide when I created my documents for the VCDX-NV submission.

I don't know if this is a hard written requirement, but you're already spending hundreds of hours on the documents... you  might as well spend couple more hours and include some screenshots, in case it is a hard requirement.
