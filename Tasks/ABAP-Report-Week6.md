# ABAP Report Programming Week6 Task

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
>Created a Input Fields with Select-options and with Parameters for the Radiobuttons

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

## 3-3. Selecting a Data 

```abap
START-OF-SELECTION.

  CASE 'X'.
    WHEN  pa_m.
      SELECT a~id a~name a~phone b~class b~grade
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
      FROM  zstudent_26 AS a INNER JOIN zstdclass_26 AS b
        ON a~id = b~id
       WHERE a~id IN so_id
       AND  a~name IN so_name
       AND a~gender = 'M'.

    WHEN  pa_f.
      SELECT a~id a~name a~phone b~class b~grade
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
      FROM  zstudent_26 AS a INNER JOIN zstdclass_26 AS b
        ON a~id = b~id
       WHERE a~id IN so_id
       AND  a~name IN so_name
       AND a~gender = 'F'.

  ENDCASE.
Call Screen 100.
```
>Using Case, when Statements.. Case 'X'. Bring up the Data When pa_m (Male)'s Button is Activated Check If ID, Name, Gender is Same.. Same goes for when pa_f (Female) is Activated

## 3-4. Creating a Screen 100

![image](https://user-images.githubusercontent.com/103248677/163212161-fd7e93d5-5eef-47b2-83e8-0edbd1f64778.png)
>Created a Screen 100

![image](https://user-images.githubusercontent.com/103248677/163212532-2ce7f82a-ac88-4d64-a758-095ce6c09a3c.png)
>Setting up Custom_control Area in Screen Painter

## 3-5. Creating OK_CODE

```abap
PROCESS AFTER INPUT.
 MODULE USER_COMMAND_0100.
```
> On PAI create a User_command_0100 Module

```abap
MODULE user_command_0100 INPUT.

case ok_code.
  when 'BACK'.
    LEAVE TO SCREEN 0.

    WHEN 'CANC'.
      LEAVE TO SCREEN 0.

      WHEN 'EXIT'.
        LEAVE PROGRAM.
ENDCASE.

ENDMODULE.
```
> Using Case Statement for OK_CODE

```abap
MODULE status_0100 OUTPUT.
 SET PF-STATUS 'S100'.
 SET TITLEBAR 'T100'.
ENDMODULE.
```
> On PBO's status_0100 Module I created Gui Status and Gui Title

![image](https://user-images.githubusercontent.com/103248677/163388310-75f65da7-fc33-4647-ace9-e309b73c54fd.png)
> Gui Status

![image](https://user-images.githubusercontent.com/103248677/163388366-4d5a13e4-9352-43ed-a6dc-69fedd4bd1e7.png)
> Gui Title 

![image](https://user-images.githubusercontent.com/103248677/163388416-26813de0-03c1-47ef-9047-1df13d09ef37.png)
> On Screen 100's Element list input OK_CODE on the Fields

```abap
DATA : ok_code TYPE sy-ucomm.
```
> Add this line to make sure OK_CODE Works

## 3-6. Creating ALV with Pattern Feature

```abap
DATA : go_container TYPE REF TO cl_gui_custom_container.
DATA : go_alv TYPE REF TO cl_gui_alv_grid.
```
>Defining Go_container and Go_alv

```abap
CREATE OBJECT go_container
  EXPORTING
*    parent                      =
    container_name              = 'CUSTOM_CONTROL'
*    style                       =
*    lifetime                    = lifetime_default
*    repid                       =
*    dynnr                       =
*    no_autodef_progid_dynnr     =
  EXCEPTIONS
    cntl_error                  = 1
    cntl_system_error           = 2
    create_error                = 3
    lifetime_error              = 4
    lifetime_dynpro_dynpro_link = 5
    others                      = 6
    .
IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.




CREATE OBJECT go_alv
  EXPORTING
*    i_shellstyle      = 0
*    i_lifetime        =
    i_parent          = go_container
*    i_appl_events     = space
*    i_parentdbg       =
*    i_applogparent    =
*    i_graphicsparent  =
*    i_name            =
*    i_fcat_complete   = SPACE
*  EXCEPTIONS
*    error_cntl_create = 1
*    error_cntl_init   = 2
*    error_cntl_link   = 3
*    error_dp_create   = 4
*    others            = 5
    .
IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.

CALL METHOD go_alv->set_table_for_first_display
 EXPORTING
*    i_buffer_active               =
*    i_bypassing_buffer            =
*    i_consistency_check           =
    i_structure_name              = 'ZSCLASS_26_1'
*    is_variant                    =
*    i_save                        =
    i_default                     = 'X'
*    is_layout                     =
*    is_print                      =
*    it_special_groups             =
*    it_toolbar_excluding          =
*    it_hyperlink                  =
*    it_alv_graphics               =
*    it_except_qinfo               =
*    ir_salv_adapter               =
  CHANGING
    it_outtab                     = gt_grade
*    it_fieldcatalog               =
*    it_sort                       =
*    it_filter                     =
*  EXCEPTIONS
*    invalid_parameter_combination = 1
*    program_error                 = 2
*    too_many_lines                = 3
*    others                        = 4
        .
IF sy-subrc <> 0.
* Implement suitable error handling here
ENDIF.
```
>By using Patterns you can easily Call method, classes and Creating Objects..


# 4. Results

![image](https://user-images.githubusercontent.com/103248677/163392707-89913445-797b-45ef-8b5f-36e09c7bf899.png)
>Selection-Screen

![image](https://user-images.githubusercontent.com/103248677/163393248-80f1a4ea-f122-4519-b917-bd45dda5d6f3.png)
>When Male

![image](https://user-images.githubusercontent.com/103248677/163393434-1634d4a8-c668-4c16-80b8-9490d5c086f6.png)
>When Female


