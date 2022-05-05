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

## 5-2.
