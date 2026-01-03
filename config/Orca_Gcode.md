Machine Start G-Code:

M107
G90
M140 S[bed_temperature_initial_layer_single]
M104 S140 
M190 S[bed_temperature_initial_layer_single]
M109 S140
M106 S255 
;G30
G28
BED_MESH_CALIBRATE_CUSTOM MESH_MIN_X={adaptive_bed_mesh_min[0]} MESH_MIN_Y={adaptive_bed_mesh_min[1]} MESH_MAX_X={adaptive_bed_mesh_max[0]} MESH_MAX_Y={adaptive_bed_mesh_max[1]}
;BED_MESH_CALIBRATE
;AUTO_LEVELING_2
M106 S0
G21
M83
M109 S[nozzle_temperature_initial_layer]
P0 M1
P28
P2 A1
{if total_toolchanges == 0}
T0
{endif}
SET_PRINT_STATS_INFO TOTAL_LAYER=[total_layer_count]


Layer Change G-Code:

;AFTER_LAYER_CHANGE [layer_num] @ [layer_z]mm
SET_PRINT_STATS_INFO CURRENT_LAYER={layer_num + 1}


Change Filament G-Code:

; =================================================================
; Filament Change Sequence with Z-Sandwich
; =================================================================
; IMPORTANT: Requires PG101 and ORCA_PURGE macros from AddOn.cfg
; See README.md for setup instructions.
;
; Z-Sandwich Logic:
;   1. Slicer lifts Z +3mm at start
;   2. PG101 + Firmware do their work (no Z changes in macros)
;   3. Slicer restores Z -3mm at end
; This prevents Z stacking errors from competing Z moves.
; =================================================================

; --- 1. GLOBAL SAFETY LIFT ---
G91
G1 Z3 F12000      ; Lift Z +3mm (maintained throughout process)
G90
M83

; --- 2. EXECUTE CHANGE ---
; Retract OLD filament
G1 E-{retraction_length[current_extruder]} F1800

; Tool Change (Calls PG101 -> Firmware Unload/Load)
T[next_extruder]

; Purge & Wipe
ORCA_PURGE FLUSH={flush_length} RETRACT={retraction_length[next_extruder]}

; --- 3. GLOBAL RESTORE ---
G91
G1 Z-3 F12000     ; Restore Z -3mm (back to print height)
G90
