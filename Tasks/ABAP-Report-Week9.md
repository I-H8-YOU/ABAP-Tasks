# ABAP Report Programming Week9 Task

> [한국어](ABAP-Report-Week9-kr.md)
>  &nbsp;·&nbsp;
> [**English**](CleanABAP.md)

 
Goals for this Task
* Implementing a light, Row color, Cell color,Sorting feature, Apply field catalog
* Will be using a ALV program used in last task and add some features I wrote above

# 1. Editing Fields ( Types statement and declaring a data for ALV design)

## 1-1. Types statement
```abap
TYPES BEGIN OF t_grade.
TYPES : id     TYPE zstudent_26_1-id,
        name   TYPE zstudent_26_1-name,
        phone  TYPE zstudent_26_1-phone,
        class  TYPE zstdclass_26_1-class,
        grade  TYPE zstdclass_26_1-grade,
        cname  TYPE zsubject_26_1-cname,
        tname  TYPE zprof_26_1-tname,
        major  TYPE zstudent_26_1-major,
        light  TYPE c LENGTH 1,  " For light
        color  TYPE c LENGTH 4,  " For row color
        it_col TYPE lvc_t_scol.  " For Cell color
TYPES END OF t_grade.
```
> Added a fields that I would like to output one the ALV list

## 1-2. Data for ALV design
```abap
DATA : gv_variant           TYPE disvariant, 
       gv_save              TYPE c LENGTH 1,
       gs_layout            TYPE lvc_s_layo,
       gt_sorting_criteria  TYPE lvc_t_sort, "Sorting ascending or descending depending on how I give conditions below..
       gs_sorting_criterion TYPE lvc_s_sort.
       
DATA : gt_fieldcat TYPE lvc_t_fcat, " For Field catalog (can be used instead of structure)
       gs_fieldcat TYPE lvc_s_fcat.
       
DATA : gs_colfield LIKE LINE OF gs_grade-it_col. "Cell color
```
> Added a features for ALV

# 2. InnerJoin new table

```abap
 SELECT a~id a~name a~phone a~major b~class b~grade c~cname d~tid d~tname 
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
      FROM  zstudent_26_1 AS a INNER JOIN zstdclass_26_1 AS b
        ON a~id = b~id
        INNER JOIN zsubject_26_1 AS c
        ON b~class = c~class
          INNER JOIN zprof_26_1 AS d
        ON c~tid = d~tid
       WHERE a~id IN so_id
       AND  a~name IN so_name
      AND a~gender = '남성'.
```
> InnerJoin 'Prof table' with same column 'tid' and select 'Major', 'tname'

# 3. Implementing Light

```abap
 IF gs_grade-grade = 'A'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'B'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'B+'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'C+'.
          gs_grade-light = '2'.
        ELSEIF gs_grade-grade = 'C'.
          gs_grade-light = '2'.
        ELSE.
          gs_grade-light = '1'.
        ENDIF.
```
> Using if Statemnts.. added a light with condition I have given. 3(Green) 2(yellow) 1(red)

# 4. Row colors

```abap
        IF gs_grade-id = '1000'. 
          gs_grade-color = 'C' && 5 && '10'.
        ENDIF.
``` 
> If ID is 1000 set row color as c (default) 5(green) 10( 1 stands for deepen color and 0 stands for no inverting color)

# 5. Cell colors

```abap
   IF gs_grade-grade = 'D+'. 
      gs_colfield-fname = 'GRADE'.
      gs_colfield-color-col = col_negative.
      gs_colfield-color-int = '1'.
      gs_colfield-color-inv = '0'.
      APPEND gs_colfield TO gs_grade-it_col.
    ENDIF.
```
> Set 'D+' cell color as col_negative (RED)

![image](https://user-images.githubusercontent.com/103248677/166884584-0381824a-0bcf-408d-b450-03ce67ff2a5f.png)

# 5. Sorting & Field Catalog

## 5-1. Sorting

```abap
    gs_sorting_criterion-fieldname = 'ID'.
    gs_sorting_criterion-down = 'X'.
    APPEND gs_sorting_criterion TO  gt_sorting_criteria.
```
> Sorting By ID with descending

## 5-2. Field catalog

```abap
FORM set_fcat.
  gs_fieldcat-fieldname = 'MAJOR'. "name
  gs_fieldcat-ref_table = 'ZSTUDENT_26_1'.
  gs_fieldcat-ref_field = 'MAJOR'. "ref table col
  gs_fieldcat-col_pos = '4'. " column position
  APPEND gs_fieldcat TO gt_fieldcat.
  CLEAR gs_fieldcat.
ENDFORM.
```

## 5-3. Applying a Field catalog & Sorted data

```abap
    CALL METHOD go_alv->set_table_for_first_display
      EXPORTING
*       i_buffer_active               =
*       i_bypassing_buffer            =
*       i_consistency_check           =
        i_structure_name              = 'ZSclass_26_1'
        is_variant                    = gv_variant
        i_save                        = 'X'
        i_default                     = 'X'
        is_layout                     = gs_layout
*       is_print                      =
*       it_special_groups             =
*       it_toolbar_excluding          =
*       it_hyperlink                  =
*       it_alv_graphics               =
*       it_except_qinfo               =
*       ir_salv_adapter               =
      CHANGING
        it_outtab                     = gt_grade
        it_fieldcatalog               = gt_fieldcat "field catalog
        it_sort                       = gt_sorting_criteria "Sorting
*       it_filter                     =
      EXCEPTIONS
        invalid_parameter_combination = 1
        program_error                 = 2
        too_many_lines                = 3
        OTHERS                        = 4.
    IF sy-subrc <> 0.
* Implement suitable error handling here
    ENDIF.
```
# 6. Code overiew
```abap
REPORT zalv_week9_26_3.
TABLES: zstudent_26_1, zstdclass_26_1, zsubject_26_1, zprof_26_1.

DATA : ok_code TYPE sy-ucomm.
DATA : go_container TYPE REF TO cl_gui_custom_container.
DATA : go_alv TYPE REF TO cl_gui_alv_grid.

SELECTION-SCREEN BEGIN OF BLOCK va1 WITH FRAME TITLE TEXT-001.
SELECT-OPTIONS : so_id FOR zstudent_26_1-id,
                 so_name FOR zstudent_26_1-name.
SELECTION-SCREEN END OF BLOCK va1.
PARAMETERS pa_lv TYPE disvariant-variant.
PARAMETERS : pa_m RADIOBUTTON GROUP ra1 DEFAULT 'X',
             pa_f RADIOBUTTON GROUP ra1.

DATA : gv_variant           TYPE disvariant, "Variants
       gv_save              TYPE c LENGTH 1,
       gs_layout            TYPE lvc_s_layo,
       gt_sorting_criteria  TYPE lvc_t_sort, "sorting ascending or descending with conditions..
       gs_sorting_criterion TYPE lvc_s_sort.
DATA : gt_fieldcat TYPE lvc_t_fcat, "Field catalogs
       gs_fieldcat TYPE lvc_s_fcat.

TYPES BEGIN OF t_grade.
TYPES : id     TYPE zstudent_26_1-id,
        name   TYPE zstudent_26_1-name,
        phone  TYPE zstudent_26_1-phone,
        class  TYPE zstdclass_26_1-class,
        grade  TYPE zstdclass_26_1-grade,
        cname  TYPE zsubject_26_1-cname,
        tname  TYPE zprof_26_1-tname,
        major  TYPE zstudent_26_1-major,
        light  TYPE c LENGTH 1,
        color  TYPE c LENGTH 4,
        it_col TYPE lvc_t_scol.
TYPES END OF t_grade.

DATA : gt_grade TYPE TABLE OF t_grade,
       gs_grade TYPE t_grade.
DATA gs_colfield LIKE LINE OF gs_grade-it_col. "Cell color
DATA gt_exc TYPE ui_functions.

START-OF-SELECTION.

  CASE 'X'.
    WHEN  pa_m.
      SELECT a~id a~name a~phone a~major b~class b~grade c~cname d~tid d~tname
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
      FROM  zstudent_26_1 AS a INNER JOIN zstdclass_26_1 AS b
        ON a~id = b~id
        INNER JOIN zsubject_26_1 AS c
        ON b~class = c~class
          INNER JOIN zprof_26_1 AS d
        ON c~tid = d~tid
       WHERE a~id IN so_id
       AND  a~name IN so_name
      AND a~gender = '남성'.

      LOOP AT gt_grade INTO gs_grade.
        IF gs_grade-grade = 'A'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'B'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'B+'.
          gs_grade-light = '3'.
        ELSEIF gs_grade-grade = 'C+'.
          gs_grade-light = '2'.
        ELSEIF gs_grade-grade = 'C'.
          gs_grade-light = '2'.
        ELSE.
          gs_grade-light = '1'.
        ENDIF.

        IF gs_grade-id = '1000'. "ID 1000번 열을 녹색으로
          gs_grade-color = 'C' && 5 && '10'.
        ENDIF.

    IF gs_grade-grade = 'D+'.  "D+만 빨간색으로..
      gs_colfield-fname = 'GRADE'.
      gs_colfield-color-col = col_negative.
      gs_colfield-color-int = '1'.
      gs_colfield-color-inv = '0'.
      APPEND gs_colfield TO gs_grade-it_col.
    ENDIF.
        MODIFY gt_grade FROM gs_grade.
      ENDLOOP.

    WHEN  pa_f.
      SELECT a~id a~name a~phone b~class b~grade c~cname d~tid d~tname a~major
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
      FROM  zstudent_26_1 AS a INNER JOIN zstdclass_26_1 AS b
        ON a~id = b~id
                INNER JOIN zsubject_26_1 AS c
        ON b~class = c~class
        INNER JOIN zprof_26_1 AS d
        ON c~tid = d~tid
       WHERE a~id IN so_id
       AND  a~name IN so_name
      AND a~gender = '여성'.

      LOOP AT gt_grade INTO gs_grade.
        IF gs_grade-grade = 'A'.
          gs_grade-light = '1'.
        ELSEIF gs_grade-grade = 'C'.
          gs_grade-light = '2'.
        ELSE.
          gs_grade-light = '3'.
        ENDIF.
        IF gs_grade-id = '1000'.
          gs_grade-color = 'C' && col_negative && '10'.
        ENDIF.
        MODIFY gt_grade FROM gs_grade.
      ENDLOOP.


  ENDCASE.

  CALL SCREEN 100.
*&---------------------------------------------------------------------*
*& Module STATUS_0100 OUTPUT
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
MODULE status_0100 OUTPUT.
  SET PF-STATUS 'S100'.
  SET TITLEBAR 'T100'.
ENDMODULE.
*&---------------------------------------------------------------------*
*&      Module  USER_COMMAND_0100  INPUT
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
MODULE user_command_0100 INPUT.

  CASE ok_code.
    WHEN 'BACK'.
      LEAVE TO SCREEN 0.

    WHEN 'CANC'.
      LEAVE TO SCREEN 0.

    WHEN 'EXIT'.
      LEAVE PROGRAM.
  ENDCASE.



ENDMODULE.
*&---------------------------------------------------------------------*
*& Module CLEAR_CODE OUTPUT
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
MODULE clear_code OUTPUT.
  CLEAR ok_code.
ENDMODULE.
*&---------------------------------------------------------------------*
*& Module ALV_CONTROL OUTPUT
*&---------------------------------------------------------------------*
*&
*&---------------------------------------------------------------------*
MODULE alv_control OUTPUT.

  IF go_container IS INITIAL.
    CREATE OBJECT go_container
      EXPORTING
*       parent                      =
        container_name              = 'CUSTOM_CONTROL'
*       style                       =
*       lifetime                    = lifetime_default
*       repid                       =
*       dynnr                       =
*       no_autodef_progid_dynnr     =
      EXCEPTIONS
        cntl_error                  = 1
        cntl_system_error           = 2
        create_error                = 3
        lifetime_error              = 4
        lifetime_dynpro_dynpro_link = 5
        OTHERS                      = 6.
    IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
    ENDIF.

    CREATE OBJECT go_alv
      EXPORTING
*       i_shellstyle      = 0
*       i_lifetime        =
        i_parent = go_container
*       i_appl_events     = space
*       i_parentdbg       =
*       i_applogparent    =
*       i_graphicsparent  =
*       i_name   =
*       i_fcat_complete   = SPACE
*  EXCEPTIONS
*       error_cntl_create = 1
*       error_cntl_init   = 2
*       error_cntl_link   = 3
*       error_dp_create   = 4
*       others   = 5
      .
    IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
*            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
    ENDIF.
    PERFORM set_fcat.
    gv_variant-report = sy-cprog.
    gv_variant-variant = pa_lv.
    gs_layout-zebra = 'X'.
    gs_layout-cwidth_opt = 'X'.
    gs_layout-sel_mode = 'D'. "selection mode D ( multiple selection )
    gs_layout-excp_fname = 'light'. "Applying a light on ALV
    gs_sorting_criterion-fieldname = 'ID'. "sorting by ID .. Descending
    gs_sorting_criterion-down = 'X'.
    APPEND gs_sorting_criterion TO  gt_sorting_criteria.
    gs_layout-info_fname = 'COLOR'. "applying Row color
    gs_layout-ctab_fname = 'it_col'. "applying Cell color
    PERFORM exclude_button.


    CALL METHOD go_alv->set_table_for_first_display
      EXPORTING
*       i_buffer_active               =
*       i_bypassing_buffer            =
*       i_consistency_check           =
        i_structure_name              = 'ZSclass_26_1'
        is_variant                    = gv_variant
        i_save                        = 'X'
        i_default                     = 'X'
        is_layout                     = gs_layout
*       is_print                      =
*       it_special_groups             =
*       it_toolbar_excluding          =
*       it_hyperlink                  =
*       it_alv_graphics               =
*       it_except_qinfo               =
*       ir_salv_adapter               =
      CHANGING
        it_outtab                     = gt_grade
        it_fieldcatalog               = gt_fieldcat "field catalog
        it_sort                       = gt_sorting_criteria "Sorting
*       it_filter                     =
      EXCEPTIONS
        invalid_parameter_combination = 1
        program_error                 = 2
        too_many_lines                = 3
        OTHERS                        = 4.
    IF sy-subrc <> 0.
* Implement suitable error handling here
    ENDIF.

  ENDIF.

ENDMODULE.
*&---------------------------------------------------------------------*
*& Form EXCLUDE_BUTTON
*&---------------------------------------------------------------------*
*& text
*&---------------------------------------------------------------------*
*& -->  p1        text
*& <--  p2        text
*&---------------------------------------------------------------------*
FORM exclude_button .

  APPEND cl_gui_alv_grid=>mc_fc_filter TO gt_exc.
  APPEND cl_gui_alv_grid=>mc_mb_sum TO gt_exc.
  APPEND cl_gui_alv_grid=>mc_fc_detail TO gt_exc.
ENDFORM.

FORM set_fcat.
  gs_fieldcat-fieldname = 'MAJOR'. "name
  gs_fieldcat-ref_table = 'ZSTUDENT_26_1'.
  gs_fieldcat-ref_field = 'MAJOR'. "ref table col
  gs_fieldcat-col_pos = '4'. " column position
  APPEND gs_fieldcat TO gt_fieldcat.
  CLEAR gs_fieldcat.
ENDFORM.
```
