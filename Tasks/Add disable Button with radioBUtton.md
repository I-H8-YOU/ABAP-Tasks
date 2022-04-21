```abap
PARAMETERS : pa_model TYPE string,
             pa_cor   TYPE string,
             pa_we    TYPE i,
             va_1     RADIOBUTTON GROUP ra1 DEFAULT 'X' USER-COMMAND uc1,
             va_2     RADIOBUTTON GROUP ra1.


AT SELECTION-SCREEN OUTPUT.
  LOOP AT SCREEN.
    CASE 'X'.
      WHEN va_2.
        IF screen-name CS 'pa_cor'.
          screen-active = 0.
          MODIFY SCREEN.
        ENDIF.

      WHEN va_1.
        IF screen-name CS 'pa_we'.
          screen-active = 0.
          MODIFY SCREEN.
        ENDIF.
    ENDCASE.
  ENDLOOP.
```
