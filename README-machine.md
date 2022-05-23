## Cura Settings

* Retraction distance: 7.5mm
* Retraction speed: 50mm/s

### G-Code

Found on [www.3dprintbeast.com](https://www.3dprintbeast.com/bltouch-gcode-start-and-end/) and adapted.

#### Start G-Code

```gcode
M140 S{material_bed_temperature_layer_0} ; Start heating the heated bed
M190 S{material_bed_temperature_layer_0} ; Wait until the heated bed reaches the desired temperature
M104 S160; Start heating the extruder to 160 degrees Celsius

G28        ; Auto Home
G29 L1     ; load UBL Mesh
G29 J      ; 3 point tilt
G29 F10.0  ; Fade to 10mm
G92 E0     ; Reset extruder origin

M104 S{material_print_temperature_layer_0}  ; Start heating the extruder
G1 X0.1 Y20 Z0.3 F5000.0                    ; Move printhead to the starting position
M109 S{material_print_temperature_layer_0}  ; Wait until the extruder reaches the desired temperature

G1 Z2.0 F3000 ; Move Z-Axis up to avoid scratching of the build plate
G12 ; clean nozzle
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; Move to the side
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line
G92 E0 ; Reset extruder origin
G1 Z2.0 F3000 ; Move Z-Axis up to avoid scratching of the build plate
G1 X5 Y20 Z0.3 F5000.0 ; Move over to prevent blob squish

```

#### Stop G-Code

```gcode
M400 ; Finish Moves
G91 ;Relative positioning
G1 E-2 F2700 ;Retract a bit
G1 E-2 Z0.2 F2400 ;Retract and raise Z
G1 X5 Y5 F3000 ;Wipe out
G1 Z50 ;Raise Z more
G90 ;Absolute positioning

M118 //action:pjhTakeSnapshot

G1 X0 Y{machine_depth} ;Present print
M106 S0 ;Turn-off fan
M104 S0 ;Turn-off hotend
M140 S0 ;Turn-off bed

M84 X Y E ;Disable all steppers but Z
```