# DMEE-Node-Path
DMEE Node Path usefull for XML DMEE CGI_XML_CT SEPA_CT


![Image1](https://github.com/vidyadharg/DMEE-Node-Path/blob/master/images/image1.png)
![Image2](https://github.com/vidyadharg/DMEE-Node-Path/blob/master/images/image2.png)

## CODE :
 Include              LDMEE1_ABACIC

### 1.METHOD handle_node_cont_men_req_dmee. In CLASS lcl_caller IMPLEMENTATION.  
```
  ENHANCEMENT 1  ZFIIMP_DMEE_PATH.    "active version @END of METHOD
   IF processing_type = 'D' AND node_key NE 'ROOT'. "display
      menu->ADD_SEPARATOR( ).
      menu->add_function( fcode = 'ZZDMEEPATH' text  = 'DMEE Node Path' ).
      menu->add_function( fcode = 'ZZDMEEPATHC' text  = 'Copy - DMEE Node Path' ).
   ENDIF.
  ENDENHANCEMENT.
```
### 2.	METHOD handle_node_cont_men_sel_dmee.
```	
ENHANCEMENT 2  zfiimp_dmee_path.    "active version @END of METHOD
DATA :
  lv_curr_node TYPE REF TO lcl_dmee_node,
  lv_string    TYPE string,
  lv_rc        TYPE i,
  lt_string    LIKE STANDARD TABLE OF lv_string.
    CASE fcode.
      WHEN 'ZZDMEEPATH' OR
           'ZZDMEEPATHC'.

        DATA(lv_node_key) = node_key.

        CALL METHOD dmeecontainer->get_node
          EXPORTING
            node_id        = lv_node_key
          IMPORTING
            node           = lv_curr_node
          EXCEPTIONS
            node_not_found = 1.


        IF lv_curr_node IS NOT INITIAL.
          lv_string = '<' && lv_curr_node->tech_name && '>' && lv_string.
    *        parent_node
          DO.
            IF lv_curr_node->parent_node IS INITIAL.
              EXIT.
            ELSE.
              lv_curr_node = lv_curr_node->parent_node.
              lv_string = '<' && lv_curr_node->tech_name && '>' && lv_string.
            ENDIF.
          ENDDO.
        ENDIF.

        IF lv_string IS NOT INITIAL.
          IF fcode EQ 'ZZDMEEPATHC'.

            APPEND lv_string TO lt_string.

            CALL METHOD cl_gui_frontend_services=>clipboard_export
              IMPORTING
                data = lt_string
              CHANGING
                rc   = lv_rc.
            IF lv_rc = 0.
              MESSAGE 'Path copied to clipboard' TYPE 'S'.
            ENDIF.

          ELSE.
            lv_string = escape( val    = lv_string
                               format = cl_abap_format=>e_html_text ).

            cl_demo_output=>new(
            )->begin_section( 'DMEE Node Path'
            )->write_html( lv_string

            )->display( ).
          ENDIF.
        ENDIF.
        RETURN.
    ENDCASE.
ENDENHANCEMENT.
```
