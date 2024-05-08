# RapidChange ATC Scripts for GrblHAL

## Macros

### P200
P200.macro contains the settings for all RapidChange ATC macros. This macro must be run after any firmware
reboot. 

The User Configuration section at the beginning provides the variable declarations for all user settings.
Enter the appropriate values into this section.



## Usage

### Macro Installation
Upload the provided macros to your SD card. You may need to reboot the firmware after uploading 
the macros for the first time so that the firmware recognizes TC.macro and will call it when encountering
an M6 command.