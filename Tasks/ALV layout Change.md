```abap
TABLES : zstudent_26, zstdclass_26, zsubject_26.
DATA : ok_code TYPE sy-ucomm.
DATA : go_container TYPE REF TO cl_gui_custom_container,
       go_grid      TYPE REF TO cl_gui_alv_grid.




DATA : gs_layout TYPE lvc_s_layo. " ALV Layout Change
DATA : gv_variant TYPE disvariant.  " For variant


SELECT-OPTIONS : so_id FOR zstudent_26-id,
                 so_name FOR zstudent_26-name.

PARAMETERS : pa_m RADIOBUTTON GROUP ra1 DEFAULT 'X',
             pa_f RADIOBUTTON GROUP ra1.
PARAMETERS : pa_lv TYPE disvariant-variant.  " For variant




TYPES BEGIN OF t_grade.
TYPES : id    TYPE zstudent_26-id,
        name  TYPE zstudent_26-name,
        grade TYPE zstdclass_26-grade,
        cname TYPE zsubject_26-cname,
        light TYPE c LENGTH 1. " Traffic light
TYPES END OF t_grade.

DATA : gt_grade TYPE TABLE OF t_grade,
       gs_grade TYPE t_grade.

START-OF-SELECTION.

  CASE 'X'.
    WHEN pa_m.
      SELECT a~id a~name b~grade c~cname
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
        FROM zstudent_26 AS a INNER JOIN zstdclass_26 AS b
        ON a~id = b~id
        INNER JOIN zsubject_26 AS c
        ON b~class = c~class
        WHERE a~id IN so_id
        AND a~name IN so_name
        AND a~gender = 'M'.

      LOOP AT gt_grade INTO gs_grade. " 신호등
        IF gs_grade-grade = 'D'.
          gs_grade-light = '1'.
        ELSEIF gs_grade-grade = 'A'.
          gs_grade-light = '3'.
        ELSE.
          gs_grade-light = '2'.
        ENDIF.
        MODIFY gt_grade FROM gs_grade.
      ENDLOOP.

    WHEN pa_f.
      SELECT a~id a~name b~grade c~cname
        INTO CORRESPONDING FIELDS OF TABLE gt_grade
        FROM zstudent_26 AS a INNER JOIN zstdclass_26 AS b
        ON a~id = b~id
        INNER JOIN zsubject_26 AS c
        ON b~class = c~class
        WHERE a~id IN so_id
        AND a~name IN so_name
        AND a~gender = 'F'.

      LOOP AT gt_grade INTO gs_grade.
        IF gs_grade-grade = 'D'.
          gs_grade-light = '1'.
        ELSEIF gs_grade-grade = 'A'.
          gs_grade-light = '3'.
        ELSE.
          gs_grade-light = '2'.
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
      PERFORM free_control.   " Free Control
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



    CREATE OBJECT go_grid
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

    gv_variant-report = sy-cprog. " For variant
    gv_variant-variant = pa_lv.  " For variant

    gs_layout-grid_title = 'ALV Grid Title Test'. " ALV Layout Change
    gs_layout-zebra = 'X'. " ALV Layout Change
    gs_layout-cwidth_opt = 'X'. " ALV Layout Change
    gs_layout-sel_mode = 'D'. "cell select option


    gs_layout-excp_fname = 'LIGHT'.



    CALL METHOD go_grid->set_table_for_first_display
      EXPORTING
*       i_buffer_active  =
*       i_bypassing_buffer            =
*       i_consistency_check           =
        i_structure_name = 'ZSCLASS_26_1'
        is_variant       = gv_variant " For variant
        i_save           = 'X'       " For variant
*       i_default        = 'X'
        is_layout        = gs_layout   " ALV Layout Change
*       is_print         =
*       it_special_groups             =
*       it_toolbar_excluding          =
*       it_hyperlink     =
*       it_alv_graphics  =
*       it_except_qinfo  =
*       ir_salv_adapter  =
      CHANGING
        it_outtab        = gt_grade
*       it_fieldcatalog  =
*       it_sort          =
*       it_filter        =
*  EXCEPTIONS
*       invalid_parameter_combination = 1
*       program_error    = 2
*       too_many_lines   = 3
*       others           = 4
      .
    IF sy-subrc <> 0.
* Implement suitable error handling here
    ENDIF.


  ENDIF.

ENDMODULE.

FORM free_control .
  CALL METHOD go_container->free
    EXCEPTIONS
      cntl_error        = 1
      cntl_system_error = 2
      OTHERS            = 3.
  IF sy-subrc <> 0.
* Implement suitable error handling here
  ENDIF.

  CALL METHOD go_grid->free
    EXCEPTIONS
      cntl_error        = 1
      cntl_system_error = 2
      OTHERS            = 3.
  IF sy-subrc <> 0.
* Implement suitable error handling here
  ENDIF.

  FREE : go_container, go_grid.

ENDFORM.
```
> For layout change.. such as saving variants, traffic light, custom title, Zebra Apperance
