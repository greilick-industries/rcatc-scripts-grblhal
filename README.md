# RapidChange ATC Scripts for GrblHAL

## Firmware Requirements

### FlexiHAL
[FlexiHAL supported builds](https://github.com/Expatria-Technologies/STM32F4xx/releases/tag/flexi-hal-v1.0.0.2)


## Important Notes

### Persistance

The current tool, tool offset, and RapidChange settings only persist for the duration of a single firmware power
cycle. This means that anytime the firmware is rebooted or powered on, these values will be lost and will need to
be initialized. P200 is included in this macro package to provide that initialization functionality. Once initialized,
the settings will persist for the remainder of the session.

### Nesting
Nesting of macro calls is not currently supported in GrblHAL core. P200 is also designed to provide a workaround to
this limitation. Since the included functional macros may not call a configuration macro, they instead make a logic 
check to ensure that the RapidChange settings have already been loaded with P200.

### Tool 0
Changing tools automatically to a bare spindle may not be achieved using T0. Calling M6 with 0 for the selected
tool will bypass the TC.macro and simply set the current tool to 0 without unloading. These macros use Tool 98
as a substitute for Tool 0 as a workaround for this behavior. All of the macros treat Tool 0 and Tool 98 as the 
same tool(None).

### Debug Messages
The included macros use the DEBUG function to provide messages to the user and aid in debugging when there is an
unexpected issue. In order for the messages to print to the console, setting $534 must be set to 1.

### Updating Settings
It is a good idea to store a local copy of P200.macro, so that you can make changes to the settings in a text editor.
Whenever you want to update one or more settings:
- Make the appropriate changes to your local P200.macro.
- Upload the modified P200.macro to your SD card.
- Execute G65 P200 Q0 to update the settings.

## Included Macros

### Installation
Upload the provided macros to your SD card. You may need to reboot the firmware after uploading 
the macros for the first time so that the firmware can recognize TC.macro.

### P200
P200.macro contains the settings for all RapidChange ATC macros. Run this macro after a firmware
reboot to initialize tool state and RapidChange ATC settings. 

```
G65 P200 Q-
```
Q-- specifies the current tool in the spindle (if any) to initialize.

- Q0 loads RapidChange settings but makes no change to current tool. This is useful updating your settings.

- Q98 loads RapidChange settings and sets the current tool to 98(None).

- Using any other valid tool number will load RapidChange settings and set the current tool to that number. 
It will then perform a tool measurement, leaving you in ready state.

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

### P208
P208.macro opens the dust cover if enabled.

### P209
P209.macro closes the dust cover if enabled.

### P231
P231.macro performs a tool measurement if there is a valid current tool.

### TC.macro
TC.macro is called whenever an M6 with a valid selected tool is encountered.
