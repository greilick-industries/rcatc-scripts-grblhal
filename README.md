# RapidChange ATC Scripts for GrblHAL

## Macros

### P200
P200.macro contains the settings for all RapidChange ATC macros. This macro must be run after any firmware
reboot. 

The User Configuration section at the beginning provides the variable declarations for all user settings.
Enter the appropriate values into this section.

```
; ******** BEGIN USER CONFIGURATION ********
; ATC Operations
; The units for your configuration: 20 = Inches, 21 = Millimeters
#<_rc_units> = 20
(debug, Units: #<_rc_units>)

; The number of pockets in your magazine.
#<_rc_pockets> = 0
(debug, Pockets: #<_rc_pockets>)

...

; The optional reference position for TLO. This may remain at it's default of 0 or be customized.
#<_rc_tlo_ref> = 0
(debug, Tool Measure TLO Ref Pos: #<_rc_tlo_ref>)
; ********* END USER CONFIGURATION *********
```

## Usage

### Macro Installation
Upload the provided macros to your SD card. You may need to reboot the firmware after uploading 
the macros for the first time so that the firmware recognizes TC.macro and will call it when encountering
an M6 command.