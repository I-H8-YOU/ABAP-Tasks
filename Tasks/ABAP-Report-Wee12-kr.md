# ABAP Report Programming Week12 Task

> [한국어](ABAP-Report-Wee12-kr.md)
>  &nbsp;·&nbsp;
> [**English**](ABAP-Report-Week12.md)

 
과제 목표
* ALV에서 여러 라인을 선택하고 ‘학점조회’버튼을 누르면 학생 이름과 학점이 팝업 형태의
ALV 로 조회되도록 구현![aa1](https://user-images.githubusercontent.com/103248677/170183032-73d09d97-c48c-416f-b197-162be4c7006d.png)



# 1. 구조체 추가 선언 ( Types 구문 활용)

```abap
TYPES : BEGIN OF gty_salv.
TYPES : name  TYPE zstudent_26-name,
        cname TYPE zsubject_26-cname,
        grade TYPE zstdclass_26-grade,
        END OF gty_salv.
        
DATA: go_table TYPE REF TO cl_salv_table,
      gt_salv  TYPE TABLE OF gty_salv,
      gs_salv  TYPE gty_salv.
```
> SALV에 조회가될 항목들을 구조체로 선언해 주었습니다
> ,또한 인터널 테이블과 스트럭쳐로 선언해 주었고, 'Cl_salv_table'을 참조하는 Go_table도 선언해 주었습니다

# 2. Handler Method 구현

```abap
 METHOD on_toolbar.
    DATA ls_button TYPE stb_button.

*   Sepearator
    ls_button-butn_type = 3.
    INSERT ls_button INTO TABLE e_object->mt_toolbar.

    CLEAR ls_button.
    ls_button-function = 'DIS_GRADE'.
    ls_button-text = '학점조회'(mar).
    ls_button-quickinfo = TEXT-z01.
    ls_button-butn_type = 0.
    INSERT ls_button INTO TABLE e_object->mt_toolbar.

  ENDMETHOD.
  
  
   METHOD on_user_command. " implementation for button
    DATA :
      lv_name   TYPE string,
      lv_grade  TYPE string,
      lv_text   TYPE string,

*         needed for method get_selected_rows
      lt_row_no TYPE lvc_t_roid,
      ls_row_no TYPE lvc_s_roid.
    DATA: lr_content TYPE REF TO cl_salv_form_element.
    DATA: lr_selections TYPE REF TO cl_salv_selections.

    CASE e_ucomm. " Case for user-command
      WHEN 'DIS_GRADE'. " when 'DIS_GRADE is Selected
        
        go_alv->get_selected_rows(
           IMPORTING
             et_row_no = lt_row_no ). "return the selected rows number
        IF lines( lt_row_no ) > 0.   " if selected rows are bigger than 0
          TRY.
              LOOP AT lt_row_no INTO ls_row_no.
                READ TABLE gt_grade INTO gs_grade INDEX ls_row_no-row_id. "read data from gt_grade 
                gs_salv-cname = gs_grade-cname. "Inserting data to gs_alv
                gs_salv-name = gs_grade-name.
                gs_salv-grade = gs_grade-grade.
                APPEND gs_salv TO gt_salv.

              ENDLOOP.
            CATCH cx_root.
              MESSAGE lv_text TYPE 'I'.
          ENDTRY.
        ENDIF.
       cl_salv_table=>factory(
          EXPORTING
           list_display = ''
          IMPORTING
           r_salv_table = go_table
          CHANGING
           t_table = gt_salv ).


        go_table->set_screen_popup(     " Adjusting size of pop up window
            start_column = 1
            end_column   = 50
            start_line   = 1
            end_line     = 10 ).

        lr_selections = go_table->get_selections( ).
        lr_selections->set_selection_mode( if_salv_c_selection_mode=>row_column ).

        go_table->display( ).

clear gt_salv.


    ENDCASE.
  ENDMETHOD.
```
> Factory에서 출력할 데이터와 SALV 팝업 형태를 조정해 주었고 display 메소드를 통해 리스트가 조회되도록 하였습니다

# 3. Set Handler

```abap
    SET HANDLER: lcl_handler=>on_dobule FOR go_alv,
                 lcl_handler=>top_print    FOR go_alv,
                 lcl_handler=>on_toolbar   FOR go_alv,
              lcl_handler=>on_user_command FOR go_alv.
```
> Set handler를 통해 이벤트 연결 해주었습니다

# 4. 결과

![aa1](https://user-images.githubusercontent.com/103248677/170183032-73d09d97-c48c-416f-b197-162be4c7006d.png)

# 5. ALV 메소드

## 1. get_current_cell

▪ALV GRID 컨트롤에 커서가 놓은 위치의 값과 속성들을 반환.

## 2. get_selected_columns

▪선택된 컬럼들의 정보를 LVC_T_COL 테이블로 반환.

## 3. get_selected_cells

▪현재 선택된 여러개의 셀 정보를 LVC_T_CELL 타입의 테이블로 반환. 

## 4. get_seleted_rows

▪선택된 여러 라인의 정보를 LVC_T_ROW 타입의 테이블로 반환.

