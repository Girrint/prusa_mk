```bash
[include mainsail.cfg]
#[include KAMP_Settings.cfg]
[exclude_object]
#[include sp_mmu.cfg]
#[include timelapse.cfg]
#[include adxl.cfg]

[pause_resume]

[display_status]

[delayed_gcode my_delayed_gcode]
initial_duration: 1.0
gcode:
    BED_MESH_PROFILE LOAD="default"

[force_move]
enable_force_move: True

#[temperature_sensor OrangePI]
#sensor_type: temperature_host
#min_temp: 10
#max_temp: 100

[temperature_sensor MCU]
sensor_type: temperature_mcu


[input_shaper]
shaper_type_x: 2hump_ei #3hump_ei
shaper_freq_x: 112.1 #125.6
shaper_type_y: mzv #3hump_ei
shaper_freq_y: 60.4 #101

[mcu]
serial: /dev/serial/by-id/usb-Klipper_stm32f072xb_35002F000D57475030383720-if00

[virtual_sdcard]
path: /home/klipper/printer_data/gcodes
on_error_gcode: CANCEL_PRINT

[stepper_x]
step_pin: PC15
dir_pin: !PC14
enable_pin: !PC2
microsteps: 16
rotation_distance: 40   ##rotation_distance = ((360°/1.8°) * microsteps) / 80 # # 旋转距离 = （圆周360°/步距角）*细分/每MM脉冲值
homing_retract_dist: 0
endstop_pin:tmc2209_stepper_x:virtual_endstop #X-Min, PE4:X-Max
position_endstop: 0
position_min: -1
position_max: 285
homing_speed: 20

[stepper_y]
step_pin: PA1
dir_pin: !PA0
enable_pin: !PA2
microsteps: 16
rotation_distance: 40
homing_retract_dist: 0
#endstop_pin: tmc2209_stepper_y:virtual_endstop  #Y-Min, PJ0:Y-Max
endstop_pin: ^!PB3
position_endstop: 0
position_min: -40
position_max: 230
homing_speed: 20

[extruder]
step_pin: PC5
dir_pin: PC4
enable_pin: !PB0
microsteps: 16
rotation_distance: 5.6
nozzle_diameter: 0.4
filament_diameter: 1.750
heater_pin: PC6
sensor_type: ATC Semitec 104GT-2
sensor_pin: PC1
min_temp: 0
max_temp: 350
#control: watermark
#control: pid
#pid_Kp: 22.2
#pid_Ki: 1.08
#pid_Kd: 114
#pid_calibrate heater=extruder target=240
pressure_advance: 0.02
pressure_advance_smooth_time: 0.06
max_extrude_only_velocity: 130
max_extrude_only_accel: 3000
max_extrude_only_distance: 1000.0
max_extrude_cross_section: 5
min_extrude_temp: 0 

[stepper_z]
step_pin: PA5
dir_pin: !PA4
enable_pin: !PA6
microsteps: 16
rotation_distance: 8
endstop_pin: probe:z_virtual_endstop #Z-Min, PD2:Z-Max
#position_endstop: 0
position_max: 250
position_min: -3
homing_speed: 20

[stepper_z1]
step_pin: PB10
dir_pin: !PB2
enable_pin: !PB11
microsteps: 16
rotation_distance: 8
endstop_pin: probe:z_virtual_endstop #Z-Min, PD2:Z-Max

[heater_bed]
heater_pin: PC7
sensor_type: ATC Semitec 104GT-2
sensor_pin: PC0
min_temp: -273.15
max_temp: 130
#control: watermark
#control: pid
#pid_Kp: 54.027
#pid_Ki: 0.770
#pid_Kd: 948.182

[fan]
pin: PC8

########################################
# TMC UART configuration
########################################

[tmc2209 stepper_x]
uart_pin: PC13
run_current: 0.8
interpolate: False
stealthchop_threshold: 0
diag_pin: ^PB4      # Set to MCU pin connected to TMC DIAG pin
driver_SGTHRS: 50   # 255 is most sensitive value, 0 is least sensitiv      # Set to MCU pin connected to TMC DIAG pin

[tmc2209 stepper_y]
uart_pin: PC3
run_current: 0.8
interpolate: False
stealthchop_threshold: 0
diag_pin: ^PB3      # Set to MCU pin connected to TMC DIAG pin
#driver_SGTHRS: 36 # 255 is most sensitive value, 0 is least sensitiv

# --- BỘ ĐÙN (EXTRUDER) ---
[tmc2209 extruder]
uart_pin: PA7
interpolate: False          # Tắt nội suy để Pressure Advance hoạt động chính xác nhất
run_current: 0.7            # 0.65A hơi yếu nếu in nhanh. Thử 0.7A (nếu motor nóng quá thì giảm)
hold_current: 0.7
stealthchop_threshold: 0    # QUAN TRỌNG: = 0 để chạy SpreadCycle (Khỏe, phản hồi nhanh)
diag_pin: ^PB5      # Set to MCU pin connected to TMC DIAG pin

# --- TRỤC Z TRÁI ---
[tmc2208 stepper_z]
uart_pin: PA3
interpolate: True           # Z cần êm, không cần phản hồi quá gắt
run_current: 0.8           # Tăng nhẹ lên 0.65A để giữ bàn in chắc hơn khi in nhanh
hold_current: 0.7          # Giữ nguyên dòng khi dừng để tránh trôi bàn (Z-drop)
stealthchop_threshold: 999999 # Ép chạy StealthChop cho êm (vì Z chạy chậm)

# --- TRỤC Z PHẢI (Z1) ---
[tmc2208 stepper_z1]
uart_pin: PB1
interpolate: True           # Đồng bộ với Z trái
run_current: 0.8           # Đồng bộ dòng điện
hold_current: 0.7
stealthchop_threshold: 999999

[printer]
kinematics: corexy
max_velocity: 1000
max_accel: 20000
max_z_velocity: 50
max_z_accel: 400
square_corner_velocity: 5.0

[bltouch]
sensor_pin:^PB5
control_pin:PA8
x_offset:0
y_offset:32
samples:2
speed:2
#z_offset: 1.6
pin_up_reports_not_triggered:true
pin_up_touch_mode_reports_triggered: false

[z_tilt]
z_positions:
 10,83
 275,83
points:
 10,83
 275,83
speed: 100
horizontal_move_z: 7
retries: 20
retry_tolerance: 0.02

# [safe_z_home]
# home_xy_position: 145, 85 # Change coordinates to the center of your print bed
# speed: 50
# z_hop: 10                 # Move up 10mm
# z_hop_speed: 5
# move_to_previous: true

[bed_mesh]
speed: 120
probe_count: 7, 7
horizontal_move_z: 5
algorithm: bicubic
mesh_min : 30, 30
mesh_max : 260, 200
mesh_pps: 2,2
fade_start: 1
fade_end: 10
fade_target: 0

[screws_tilt_adjust]
screw1: 143, 67
screw1_name: Center

screw2: 0, 0
screw2_name: Front Left

screw3: 143, 0
screw3_name: Front Middle

screw4: 285, 0
screw4_name: Front Right

screw5: 0, 167
screw5_name: Back Left

screw6: 143, 167
screw6_name: Back Middle

screw7: 285, 167
screw7_name: Back Right

speed: 150
horizontal_move_z: 10
screw_thread: CW-M3

[verify_heater heater_bed]
max_error: 120
check_gain_time: 150
hysteresis: 5
heating_gain: 2

[verify_heater extruder]
max_error: 120
check_gain_time: 60
hysteresis: 5
heating_gain: 2

[homing_override]
axes: xyz
gcode:
    # 1. Nhấc Z lên né kẹp giấy
    G90
    SET_KINEMATIC_POSITION Z=0
    G0 Z10 F600

    # 2. HẠ ĐỘ NHẠY XUỐNG CỰC THẤP (CHÌA KHÓA LÀ Ở ĐÂY)
    # Cũ là 60 -> Giờ hạ xuống 30.
    # Nếu 30 vẫn bị -> Hạ xuống 20 (Nhưng cẩn thận nó đâm mạnh)
    #SET_TMC_FIELD STEPPER=stepper_x FIELD=SGTHRS VALUE=45
    #SET_TMC_FIELD STEPPER=stepper_y FIELD=SGTHRS VALUE=40
    
    # 3. Chờ 2 giây cho Driver ổn định (Bắt buộc)
    #G4 P2000 

    # 4. Homing X
    G28 X
    G91
    G1 X2 F1200 # Nhích vào trong 10mm
    G90
    
    # Chờ 1s
    #G4 P1000

    # 5. Homing Y
    G28 Y
    G91
    G1 Y2 F1200 # Nhích vào trong 10mm
    G90

    # 6. Trả lại độ nhạy chuẩn để in (Ví dụ 100)
    # Lúc in thì cần nhạy xíu để detect crash
    #SET_TMC_FIELD STEPPER=stepper_x FIELD=SGTHRS VALUE=100
    #SET_TMC_FIELD STEPPER=stepper_y FIELD=SGTHRS VALUE=100

    # 7. Ra giữa bàn để Home Z
    G0 X143 Y67 F3600 
    G28 Z
    G0 Z10 F1200

    # 8. Về tọa độ 0 0
    G0 X0 Y0

[gcode_macro G29]
gcode:
    BED_MESH_CLEAR
    G28
    Z_TILT_ADJUST
    BED_MESH_CALIBRATE
    BED_MESH_PROFILE SAVE="default"
    BED_MESH_PROFILE LOAD="default"
    G28

[gcode_macro LINE_PURGE]
gcode:
    # Đảm bảo dùng absolute
    G90

    # Di chuyển ra mép trái phía trước bàn
    G1 X5 Y5 F5000
    G1 Z0.3 F1000

    # Reset extruder
    G92 E0

    # Kẻ line 1
    G1 X5 Y200 E15 F1200

    # Dịch sang phải một chút
    G1 X7 F5000

    # Kẻ line 2
    G1 X7 Y5 E30 F1200

    # Reset lại extruder
    G92 E0

    # Nhấc đầu in lên
    G1 Z2 F1000

[gcode_macro PRINT_START]
gcode:
    # Lấy tham số nhiệt độ từ Slicer
    {% set BED = params.BED|default(60)|float %}
    {% set EXTRUDER = params.EXTRUDER|default(190)|float %}

    # 1. Khởi động
    M140 S{BED}         ; Đặt nhiệt bàn in (không chờ)
    G90                      ; Chế độ tuyệt đối
    G28                      ; Home tất cả các trục

    # 2. Làm nóng sơ bộ (Preheat) để tránh chảy nhựa
    M104 S150                ; Đặt đầu in 150 độ (đủ nóng để không dính nhựa cứng, nhưng không chảy)

    # 3. Cân chỉnh máy (Lúc này bàn đã nóng, kết quả mới chính xác)
    Z_TILT_ADJUST            ; Cân bằng trục X (cho CoreXY Dual Z)
    G28 Z                    ; Home lại Z sau khi Tilt (Bắt buộc)

    # Chờ bàn in đạt nhiệt độ yêu cầu
    M190 S{BED}         ; Đợi bàn in đạt nhiệt độ (để giãn nở nhiệt ổn định)
    
    # Nếu dùng KAMP (Adaptive Mesh), bỏ comment dòng dưới, xóa dòng BED_MESH_PROFILE LOAD
    # BED_MESH_CALIBRATE
    #BED_MESH_PROFILE LOAD="default" 
    BED_MESH_CALIBRATE
    # 4. Nung nóng đầu in để in
    G1 Z5 F3000              ; Nhấc đầu in lên tí cho an toàn
    G1 X0 Y0 F5000           ; Di chuyển về góc chờ (tránh chảy nhựa ra giữa bàn)
    M109 S{EXTRUDER}    ; Đợi đầu in đạt nhiệt độ in chính thức

    # 5. In đường mồi (Prime Line)
    LINE_PURGE               ; Dùng KAMP để kẻ vạch (Xóa VORON_PURGE đi nhé)

[gcode_macro PRINT_END]
gcode:
    # Tắt hết nhiệt
    TURN_OFF_HEATERS
    M106 S0 ; Tắt quạt

    # Di chuyển an toàn (Tránh đâm trần)
    {% set max_z = printer.toolhead.axis_maximum.z|float %}
    {% set act_z = printer.toolhead.position.z|float %}
    {% set z_safe = 0.0 %}
    
    {% if act_z < (max_z - 10.0) %}
        {% set z_safe = 10.0 %}
    {% else %}
        {% set z_safe = max_z - act_z %}
    {% endif %}

    G91 ; Chế độ tương đối
    G1 E-2 F300 ; Rút nhựa nhẹ 2mm để tránh dây
    G1 Z{z_safe} F900 ; Nhấc Z lên an toàn
    G90 ; Chế độ tuyệt đối

    # Đưa đầu in vào góc trong cùng (Max Y) để dễ lấy vật in
    G1 X{printer.toolhead.axis_maximum.x - 10} Y{printer.toolhead.axis_maximum.y - 10} F3600
    
    # Tắt động cơ (Giữ Z nếu cần, nhưng thường tắt hết cho mát)
    M84

[gcode_macro PAUSE]
rename_existing: BASE_PAUSE
gcode:
    # Lưu trạng thái hiện tại
    SAVE_GCODE_STATE NAME=PAUSE_state
    BASE_PAUSE
    
    # Rút nhựa và nhấc đầu in ra xa
    G91
    G1 E-2 F2100
    G1 Z10
    G90
    # Di chuyển đầu in về góc X0 Y0 (hoặc vị trí thay nhựa)
    G1 X10 Y10 F6000

[gcode_macro RESUME]
rename_existing: BASE_RESUME
gcode:
    # Đùn nhựa lại một chút trước khi in tiếp
    G91
    G1 E2 F2100
    G90
    # Khôi phục trạng thái cũ và in tiếp
    RESTORE_GCODE_STATE NAME=PAUSE_state MOVE=1
    BASE_RESUME

[gcode_macro CANCEL_PRINT]
rename_existing: BASE_CANCEL_PRINT
gcode:
    TURN_OFF_HEATERS
    CLEAR_PAUSE
    SDCARD_RESET_FILE
    BASE_CANCEL_PRINT
    # Gọi luôn macro END_PRINT để nhấc đầu in lên cho an toàn
    END_PRINT 

[gcode_macro HOME]
gcode:
    G90
    # Home Z
    G28 Z0
    G1 Z10 F1200
    # Home Y
    G28 Y0
    G1 Y5 F1200
    # Home X
    G4 P2000
    G28 X0
    G1 X5 F1200

[gcode_macro TEST_SPEED]
gcode:
    # Speed
    {% set speed  = params.SPEED|default(printer.configfile.settings.printer.max_velocity)|int %}
    # Iterations
    {% set iterations = params.ITERATIONS|default(5)|int %}
    # Acceleration
    {% set accel  = params.ACCEL|default(printer.configfile.settings.printer.max_accel)|int %}
    # Bounding inset for large pattern (helps prevent slamming the toolhead into the sides after small skips, and helps to account for machines with imperfectly set dimensions)
    {% set bound = params.BOUND|default(20)|int %}
    # Size for small pattern box
    {% set smallpatternsize = SMALLPATTERNSIZE|default(20)|int %}
    
    # Large pattern
        # Max positions, inset by BOUND
        {% set x_min = printer.toolhead.axis_minimum.x + bound %}
        {% set x_max = printer.toolhead.axis_maximum.x - bound %}
        {% set y_min = printer.toolhead.axis_minimum.y + bound %}
        {% set y_max = printer.toolhead.axis_maximum.y - bound %}
    
    # Small pattern at center
        # Find X/Y center point
        {% set x_center = (printer.toolhead.axis_minimum.x|float + printer.toolhead.axis_maximum.x|float ) / 2 %}
        {% set y_center = (printer.toolhead.axis_minimum.y|float + printer.toolhead.axis_maximum.y|float ) / 2 %}
        
        # Set small pattern box around center point
        {% set x_center_min = x_center - (smallpatternsize/2) %}
        {% set x_center_max = x_center + (smallpatternsize/2) %}
        {% set y_center_min = y_center - (smallpatternsize/2) %}
        {% set y_center_max = y_center + (smallpatternsize/2) %}

    # Save current gcode state (absolute/relative, etc)
    SAVE_GCODE_STATE NAME=TEST_SPEED
    
    # Output parameters to g-code terminal
    { action_respond_info("TEST_SPEED: starting %d iterations at speed %d, accel %d" % (iterations, speed, accel)) }
    
    # Home and get position for comparison later:
        M400 # Finish moves - https://github.com/AndrewEllis93/Print-Tuning-Guide/issues/66
        G28
        # QGL if not already QGLd (only if QGL section exists in config)
        {% if printer.configfile.settings.quad_gantry_level %}
            {% if printer.quad_gantry_level.applied == False %}
                QUAD_GANTRY_LEVEL
                G28 Z
            {% endif %}
        {% endif %} 
        # Move 50mm away from max position and home again (to help with hall effect endstop accuracy - https://github.com/AndrewEllis93/Print-Tuning-Guide/issues/24)
        G90
        G1 X{printer.toolhead.axis_maximum.x-50} Y{printer.toolhead.axis_maximum.y-50} F{30*60}
        M400 # Finish moves - https://github.com/AndrewEllis93/Print-Tuning-Guide/issues/66
        G28 X Y
        G0 X{printer.toolhead.axis_maximum.x-1} Y{printer.toolhead.axis_maximum.y-1} F{30*60}
        G4 P1000 
        GET_POSITION

    # Go to starting position
    G0 X{x_min} Y{y_min} Z{bound + 10} F{speed*60}

    # Set new limits
    SET_VELOCITY_LIMIT VELOCITY={speed} ACCEL={accel} ACCEL_TO_DECEL={accel / 2}

    {% for i in range(iterations) %}
        # Large pattern diagonals
        G0 X{x_min} Y{y_min} F{speed*60}
        G0 X{x_max} Y{y_max} F{speed*60}
        G0 X{x_min} Y{y_min} F{speed*60}
        G0 X{x_max} Y{y_min} F{speed*60}
        G0 X{x_min} Y{y_max} F{speed*60}
        G0 X{x_max} Y{y_min} F{speed*60}
        
        # Large pattern box
        G0 X{x_min} Y{y_min} F{speed*60}
        G0 X{x_min} Y{y_max} F{speed*60}
        G0 X{x_max} Y{y_max} F{speed*60}
        G0 X{x_max} Y{y_min} F{speed*60}
    
        # Small pattern diagonals
        G0 X{x_center_min} Y{y_center_min} F{speed*60}
        G0 X{x_center_max} Y{y_center_max} F{speed*60}
        G0 X{x_center_min} Y{y_center_min} F{speed*60}
        G0 X{x_center_max} Y{y_center_min} F{speed*60}
        G0 X{x_center_min} Y{y_center_max} F{speed*60}
        G0 X{x_center_max} Y{y_center_min} F{speed*60}
        
        # Small patternbox
        G0 X{x_center_min} Y{y_center_min} F{speed*60}
        G0 X{x_center_min} Y{y_center_max} F{speed*60}
        G0 X{x_center_max} Y{y_center_max} F{speed*60}
        G0 X{x_center_max} Y{y_center_min} F{speed*60}
    {% endfor %}

    # Restore max speed/accel/accel_to_decel to their configured values
    SET_VELOCITY_LIMIT VELOCITY={printer.configfile.settings.printer.max_velocity} ACCEL={printer.configfile.settings.printer.max_accel} # ACCEL_TO_DECEL={printer.configfile.settings.printer.max_accel_to_decel} 

    # Re-home and get position again for comparison:
        M400 # Finish moves - https://github.com/AndrewEllis93/Print-Tuning-Guide/issues/66
        G28 # This is a full G28 to fix an issue with CoreXZ - https://github.com/AndrewEllis93/Print-Tuning-Guide/issues/12
        # Go to XY home positions (in case your homing override leaves it elsewhere)
        G90
        G0 X{printer.toolhead.axis_maximum.x-1} Y{printer.toolhead.axis_maximum.y-1} F{30*60}
        G4 P1000 
        GET_POSITION

    # Restore previous gcode state (absolute/relative, etc)
    RESTORE_GCODE_STATE NAME=TEST_SPEED

#*# <---------------------- SAVE_CONFIG ---------------------->
#*# DO NOT EDIT THIS BLOCK OR BELOW. The contents are auto-generated.
#*#
#*# [bed_mesh default]
#*# version = 1
#*# points =
#*# 	  0.141233, 0.198733, 0.196233, 0.156233, 0.089983, 0.073733, 0.102483
#*# 	  0.141233, 0.203733, 0.184983, 0.111233, 0.059983, 0.074983, 0.133733
#*# 	  0.138733, 0.177483, 0.127483, 0.036233, 0.016233, 0.083733, 0.152483
#*# 	  0.098733, 0.144983, 0.061233, -0.020017, -0.001267, 0.086233, 0.154983
#*# 	  0.058733, 0.147483, 0.091233, 0.054983, 0.054983, 0.153733, 0.158733
#*# 	  0.014983, 0.097483, 0.113733, 0.097483, 0.027483, 0.091233, 0.074983
#*# 	  -0.062517, 0.076233, 0.106233, 0.101233, -0.001267, -0.003767, -0.016267
#*# x_count = 7
#*# y_count = 7
#*# mesh_x_pps = 2
#*# mesh_y_pps = 2
#*# algo = bicubic
#*# tension = 0.2
#*# min_x = 30.0
#*# max_x = 259.98
#*# min_y = 30.0
#*# max_y = 199.97999999999996
#*#
#*# [bltouch]
#*# z_offset = 2.720
#*#
#*# [extruder]
#*# control = pid
#*# pid_kp = 42.683
#*# pid_ki = 16.738
#*# pid_kd = 27.210
#*#
#*# [heater_bed]
#*# control = pid
#*# pid_kp = 74.460
#*# pid_ki = 1.456
#*# pid_kd = 952.156
```
