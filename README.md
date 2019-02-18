# monoprice-delta-mini

Here's what I found works for my printer.

I'm using Monoprice Mini Delta printer, and found excellent info at [mpminidelta.com](https://www.mpminidelta.com/starting_ending_g-code_scripts).

Using Cura 3.5.1, I ran into some plate warping issues where the 3-point auto leveling was insufficient. After some tinkering, I found the settings that worked for me included

- Printing PLA using a _cold_ plate - 20 degrees. No tape, no coating.
- Using 12 point auto leveling

The Cura 3.5 machine settings are as described below.

To reach the settings hit __preferences__ from the top menu, and click __Configure Cura...__ item.

![Main menu](/assets/cura-main-menu.png "main menu")

The __General__ section is shown. Choose __Printers__ from the left instead of General. And hit the __Add__ button.

![New Printer](/assets/cura-new-printer.png "New printer")

Expand the __Custom__ label instead of _Ultimaker_, then type in the name of the printer in the text box at the bottom of the dialog. Hit __Add Printer__ when done.

![Add custom printer](/assets/cura-custom-printer-add.png "Add custom printer")

The next dialog has 2 tabs. In the first tab, replace all values under __Printer Settings__ and __Printhead Settings__ to match the settings in the picture below. It is critical to set the printer settings correctly. 

### Printer Settings
|Setting| Value|
|---|---|
|X (Width)| 100|
|Y (Depth)| 100|
|Z (Height)| 110|
|Build Plate Shape|Elliptic|
|Origin At Center | [x] |
|Heated Bed| [x] |
|G-Code flavor| Marlin|

### Printerhead settings
|Setting|Value|
|---|---|
|X min| 20mm|
|y min| 20mm|
|X max| 20mm|
|y max| 20mm|
|Gantry Height| (leave all the 9's there)|
|Number Of Extruders|1|

![Printer settings tab](/assets/Printertab.png "Printer settings tab")

### Starting G-Code

```gcode
M140 S{material_bed_temperature_layer_0}   ; set bed temp
M190 S{material_bed_temperature_layer_0}   ; wait for bed temp
M104 S{material_print_temperature_layer_0} ; set extruder temp
M109 S{material_print_temperature_layer_0} ; wait for extruder temp

G21        ;metric values
G90        ;absolute positioning
M82        ;set extruder to absolute mode
M107       ;start with the fan off

G28
G29 P6 Z0.4 ; level 6x6 grid with 0.4mm vertical offset (upward)
G1 Z15.0 F4800 ;move the platform down 15mm
G92 E0                  ;zero the extruded length
G1 F200 E3              ;extrude 3mm of feed stock
G92 E0                  ;zero the extruded length again
G1 F4800
;Put printing message on LCD screen
M117 Printing...
```

### End G-Code

```gcode
M104 S0  ;extruder heater off
M140 S0  ;heated bed heater off (if you have it)
G91      ;relative positioning
G1 E-1 F300 ;retract the filament a bit before lifting the nozzle, to release some of the pressure
G1 Z+0.5 E-5 X-20 Y-20 F4800 ;move Z up a bit and retract filament even more
G28 X0 Y0 ;move X/Y to min endstops, so the head is out of the way
M84       ;steppers off
G90       ;absolute positioning
```

### Edtruder 1
Switch tabs to the extruder settings, there you can enter the Start G-Code which includes the leveling.

![Extruder tab](/assets/Extruder1tab.png "Extruder tab")


### Extruder Start G-Code

```gcode
M140 S{material_bed_temperature_layer_0}   ; set bed temp
M190 S{material_bed_temperature_layer_0}   ; wait for bed temp
M104 S{material_print_temperature_layer_0} ; set extruder temp
M109 S{material_print_temperature_layer_0} ; wait for extruder temp


G21        ;metric values
G90        ;absolute positioning
M82        ;set extruder to absolute mode
M107       ;start with the fan off

G28
G29 C-0.8 Z0.3
G1 Z15.0 F4800 ;move the platform down 15mm
G92 E0                  ;zero the extruded length
G1 F200 E3              ;extrude 3mm of feed stock
G92 E0                  ;zero the extruded length again
G1 F4800
;Put printing message on LCD screen
M117 Printing...
```

_The extruder end code remains empty._
