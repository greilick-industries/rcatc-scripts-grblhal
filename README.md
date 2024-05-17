# RapidChange ATC Macros for GrblHAL

## Firmware Requirements

### GrblHAL Core
RapidChange ATC Macros for GrblHAL require GrblHAL core version 20240506 or higher.

### FlexiHAL
FlexiHAL boards will require the uFlexiNET Ethernet and SD Card Module.
[FlexiHAL supported builds](https://github.com/Expatria-Technologies/STM32F4xx/releases/tag/flexi-hal-v1.0.0.2)

### Other Boards
Your board must support reading from an SD card. To build supported firmware:
- [Visit the GrblHAL Web Builder](http://svn.io-engineering.com:8080/)
- Go through the appropriate tabs enabling what you need.
- Make sure RS274 NGC Expression support is checked.
- Generate and download the firmware.

## Important Notes

### Persistance

The current tool, tool offset, and RapidChange settings only persist for the duration of a single firmware power
cycle. This means that anytime the firmware is rebooted or powered on, these values will be lost and will need to
be initialized. P200 is included in this macro package to provide that initialization functionality. Once initialized,
the settings will persist for the remainder of the session.

To enable automatically loading your RapidChange settings at startup, you can use a Grbl startup block to call P200 
every time you power on or reboot the firmware.

First view your current startup blocks type `$N` from the console. You should see a response like this:
```
$N0=
$N1=
ok
```
Choose an empty startup block and assign the P200 macro like this:
```
$N0=G65 P200
```
This will run the P200 macro and store it to be called on startup.

### Tool 0
Changing tools automatically to a bare spindle may not be achieved using M6 T0. Calling M6 with 0 for the selected
tool will bypass the TC.macro and simply set the current tool to 0 without unloading. The included macros use Tool 98
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
- Execute `G65 P200` to update the settings.

## Included Macros

### Installation
Modify the P200 macro to contain the values for your configuration.

Upload the provided macros to your SD card. You may need to reboot the firmware after uploading 
the macros for the first time so that the firmware can recognize TC.macro.

Add the `G65 P200` call to the startup blocks.

### P200
P200.macro contains the settings for all RapidChange ATC macros. This file should be modified to
contain the appropriate values for your RapidChange ATC configuration. Call this macro upon startup using
the startup blocks as well as anytime you make changes to your configuration.
```
G65 P200
```

Enter the appropriate VALUE
```
#<_rc_setting_name> = VALUE
```
Example from P200.macro
```
; ******** BEGIN USER CONFIGURATION ********
; ATC Operations
; The units for your configuration: 20 = Inches, 21 = Millimeters
#<_rc_units> = 21
(debug, Units: #<_rc_units>)

; The number of pockets in your magazine.
#<_rc_pockets> = 6
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

## Workflow
Upon startup sync the current tool in the spindle with the firmware.
```
M61 Qx
```

If there is a tool loaded, measure it.
```
G65 P231
```

The tool is now synced and the TLO recorded. These values will persist and be tracked
for the remainder of your session.

If at any time during your session the tool gets out of sync through an unexpected stop
in the middle of a tool change cycle, follow the same steps before resuming operations.