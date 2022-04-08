# Making a table 
> Please note that this is a summary of what I learned on my university lecture time. I'm not an expert in ABAP so there can be an error or lack of explanation. Also keep in mind that since English is not my first language there can be mistakes too. I'm using English to Improve my Skills.


Making a Data Table in SE11 (summary)

1. Go to SE11.
2. Choose Database Table.
3. Insert a Field and Data Element. (set the keys too)
4. Make a Domain in Data Element.
5. Save and Activate the Table.

## Setting up Delivery Class
![image](https://user-images.githubusercontent.com/103248677/162426846-ae20bb98-bc6b-4f0a-a1af-887ba3601d12.png)

 1. **There's a Several Delivary Classes You can choose. I will Choose a 'A'(Master data, Transaction Data) for making and editing the fields.**
  
 2. **Must Change 'Data Browser/Table View Editing' to 'Display/Maintenance' In order to Insert a Data into a Table I will create.**
 
 
## Inserting a Field and Data Element
![image](https://user-images.githubusercontent.com/103248677/162428058-692178d3-e66a-4db5-b76d-b0042f87bd31.png)
* In this one I'm making a Table related to Students Information and Students Grade.

* After Inserting a Field You should Set up a Data elements.
 
 ![image](https://user-images.githubusercontent.com/103248677/162429145-6134b7ae-6f4b-4c1d-b2af-d53ac1f0f488.png)
*  As you can see above pic there's a two options you can choose in a Elementary type Domain and Built-in type when you're using a Built-in type you can simply set up a Data Type and length.
  
![image](https://user-images.githubusercontent.com/103248677/162429647-17fba4f2-6b20-4511-b475-809eaf3d3fae.png) ![image](https://user-images.githubusercontent.com/103248677/162430124-c66a245e-1c77-48cb-852f-7cbbff6fc6bb.png)

*  In Gender I Created a Domain. But why use Domain? There can be two reasons.
1. When there's similar Data type.
2. When you have to set a Value Ranges.

### Once You're done with setting up the Tables Save & Activate the Table.

