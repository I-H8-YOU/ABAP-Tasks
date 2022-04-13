#ABAP Report Programming Week6 Task

Goals for this Task
* Implemanting a ALV list and inquire the list
* Create a Tables and define a Foreign key, Structure based on the last lectures I attended
* In this task I should make a program that when users enter a Student ID, Name, Gender to Input Fields ALV should show the users Following data : *ID, Name, Phone number, Class, Grade*
![image](https://user-images.githubusercontent.com/103248677/163201566-aeefb95c-1b69-4264-87f3-0a9348722f70.png)
>Selection Screen will look like upper Image




# 1. Creating Tables & Structure

## 1-1. Creating Tables and Foreign key
![image](https://user-images.githubusercontent.com/103248677/163203040-a4e8e9c6-4007-4756-80a2-c22ab604e1ca.png)
> 1st Table (Student Table)

![image](https://user-images.githubusercontent.com/103248677/163203272-a911e189-39ff-4e06-bff5-a5c1e2ee2f73.png)
>2nd Table (Class Table)

![image](https://user-images.githubusercontent.com/103248677/163203454-5f171cd2-f9e5-427e-8fe4-2330d7d26a01.png)
> In 2nd Table I set up Foreign key with 1st table I have Created above
* For Cardinality One Student can listen to more than up to 1 classes so the relationship will be 1 : CN

## 1-2. Creating Structure

![image](https://user-images.githubusercontent.com/103248677/163205340-b0cf8d61-8aef-4fd5-b03b-1d58c0269169.png)
> Structure will Look like this

# 2. Inseritng a Entries into table

Insert a entries at SE16

![image](https://user-images.githubusercontent.com/103248677/163205959-c75c09df-b714-4f71-89e9-316060c1eca4.png)
>Student Table

![image](https://user-images.githubusercontent.com/103248677/163206034-607375e8-73fb-46ee-a485-d838b64db127.png)
>Class Table

# 3. Implementing ALV

## 3-1. Creating Input Fields 
```abap
SELECTION-SCREEN BEGIN OF BLOCK va1 WITH FRAME TITLE TEXT-001.
SELECT-OPTIONS : so_id FOR zstudent_26-id,
                 so_name FOR zstudent_26-name.
SELECTION-SCREEN END OF BLOCK va1.

PARAMETERS : pa_m RADIOBUTTON GROUP ra1 DEFAULT 'X',
             pa_f RADIOBUTTON GROUP ra1.

```
* Created a Input Fields with Select-options and with Parameters for the Radiobuttons

## 3-2. Defining a Types for the Internal Table
```abap
TYPES BEGIN OF t_grade.
TYPES : id    TYPE zstudent_26-id,
        name  TYPE zstudent_26-name,
        phone TYPE zstudent_26-phone,
        class TYPE zstdclass_26-class,
        grade TYPE zstdclass_26-grade.
TYPES END OF t_grade.

DATA : gt_grade TYPE TABLE OF t_grade.

```
>With 'Types' Statement I have defined a types for Internal table 'gt_grade' 

