```abap

TABLES : ztest_26, zorder_26, zproduct_26.
DATA : ok_code TYPE sy-ucomm.
DATA : go_container TYPE REF TO cl_gui_custom_container.
DATA : go_alv TYPE REF TO cl_gui_alv_grid.
  SELECTION-SCREEN BEGIN OF BLOCK va1 WITH FRAME TITLE TEXT-002.
  SELECT-OPTIONS : so_id FOR ztest_26-id,
                   so_name FOR ztest_26-name.
  SELECTION-SCREEN END OF BLOCK va1.


TYPES BEGIN OF t_prod.
TYPES : id      TYPE ztest_26-id,
        name    TYPE ztest_26-name,
        vip     TYPE ztest_26-vip,
        product TYPE zorder_26-product,
        address TYPE zorder_26-address,
        maker   TYPE zproduct_26-maker.
TYPES END OF t_prod.

DATA : gt_prod TYPE TABLE OF t_prod.

START-OF-SELECTION.
  SELECT * INTO CORRESPONDING FIELDS OF TABLE gt_prod FROM ztest_26 AS a INNER JOIN zorder_26 AS b
    ON a~id = b~id
    INNER JOIN zproduct_26 AS c
    ON b~product = c~product
    WHERE a~id IN so_id
    AND a~name IN so_name.


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



    CALL METHOD go_alv->set_table_for_first_display
      EXPORTING
*       i_buffer_active  =
*       i_bypassing_buffer            =
*       i_consistency_check           =
        i_structure_name = 'ZCOMPANY_26'
*       is_variant       =
*       i_save           =
        i_default        = 'X'
*       is_layout        =
*       is_print         =
*       it_special_groups             =
*       it_toolbar_excluding          =
*       it_hyperlink     =
*       it_alv_graphics  =
*       it_except_qinfo  =
*       ir_salv_adapter  =
      CHANGING
        it_outtab        = gt_prod
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
```
>Simple Code for ALV
![ss](https://user-images.githubusercontent.com/103248677/164462728-b8dea7eb-a50a-4906-8cd1-82b9de0c3e16.PNG)
> for PBO Modules Module status_0100, clear_code, alv_control / For PBA at user_command_0100.. can define ok_code with case~ when Statement
