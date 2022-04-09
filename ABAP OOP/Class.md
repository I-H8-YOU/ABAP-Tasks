# Class
Summary
* In ABAP Object you can define a Class as Global or Local variable
* By Using Definition & Implementation I can define a Class

# Chapter 1

## Making a Simple Program using a Definition & Implementation

```abap
REPORT ZTEST_00_26.

CLASS TEST DEFINITION.

 PUBLIC SECTION.
    METHODS WRITE_TEST.
    
ENDCLASS.

CLASS TEST IMPLEMENTATION.

  METHOD WRITE_TEST.
    WRITE : / 'Testing class'.
    
  ENDMETHOD.

ENDCLASS.

DATA GO_TEST1 TYPE REF TO TEST.

START-OF-SELECTION.
  CREATE OBJECT GO_TEST1.
  CALL METHOD GO_TEST1->WRITE_TEST.
```
> Very Simple code
* As you can see in the source code every Elements are declared in the 'Class (Name) Definition ~' Part.
* In the 'Class (Name) IMPLEMENTATION ~' part implementing a method's function.
* After Creating a Object with 'CREATE OBJECT GO_TEST1.' You can call Method like a source code. (using -> because its Instance Method if its Static Method should use =>)

## Methods
In the Class's Definition Part You can define a method and on Implement part you can implement functions about the method.
```abap
Method (Name)
~~~
Endmethod.
```
> this is how it goes..

### types of Methods
|Types of Methods    | In ABAP                |
| ----------- | ------------------------ |
| Instance Method | Methods             |
| Static | Class-Methods      |
| Special Method   | Constructor            |
* In Methods you can use Importing , Exporting to exchange Parameters

```abap
REPORT ZTEST_026.

CLASS LCL_CALC DEFINITION.
  PUBLIC SECTION.

    METHODS: CALC IMPORTING LV_NUM1   TYPE I
                            LV_OP     TYPE C
                            LV_NUM2   TYPE I
                  EXPORTING LV_RESULT TYPE I,

      DISPLAY IMPORTING GO_RESULT TYPE I.


ENDCLASS.

CLASS LCL_CALC IMPLEMENTATION.

  METHOD CALC.

        IF LV_OP = '/' AND LV_NUM2 = 0.
          MESSAGE I000(10) WITH 'Cannot Divide with 0' .
          EXIT.

        ELSEIF LV_NUM1 IS INITIAL OR LV_NUM2 IS INITIAL OR LV_OP IS INITIAL.
          MESSAGE I000(10) WITH 'Enter a value' .

          EXIT.
        ENDIF.

    CASE LV_OP.

      WHEN '+'.
        LV_RESULT = LV_NUM1 + LV_NUM2 .
      WHEN '-'.
        LV_RESULT = LV_NUM1 - LV_NUM2 .
      WHEN '*'.
        LV_RESULT = LV_NUM1 * LV_NUM2 .
      WHEN '/'.
        LV_RESULT = LV_NUM1 / LV_NUM2 .

    ENDCASE.

  ENDMETHOD.

  METHOD DISPLAY.
    DATA: ZV_RESULT TYPE I.
    MOVE: GO_RESULT TO ZV_RESULT.
    WRITE:/ '결과값은 ', ZV_RESULT.

  ENDMETHOD.

ENDCLASS.


DATA: GO_TEST1  TYPE REF TO LCL_CALC,
      GV_RESULT TYPE I.

PARAMETERS: PA_NUM1 TYPE I ,
            PA_OP   TYPE C ,
            PA_NUM2 TYPE I .


START-OF-SELECTION.
  CREATE OBJECT GO_TEST1.

  CALL METHOD GO_TEST1->CALC
    EXPORTING
      LV_NUM1   = PA_NUM1
      LV_OP     = PA_OP
      LV_NUM2   = PA_NUM2
    IMPORTING
      LV_RESULT = GV_RESULT.

END-OF-SELECTION.

  CALL METHOD GO_TEST1->DISPLAY
    EXPORTING
      GO_RESULT = GV_RESULT.
```
> Simple Calculator
