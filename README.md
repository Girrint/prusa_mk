# prusa_mk

```bash
[include mainsail.cfg]

[mcu]
serial: /dev/serial/by-id/usb-Klipper_stm32f072xb_35002F000D57475030383720-if00

[printer]
kinematics: cartesian
max_velocity: 150
max_accel: 1500
max_z_velocity: 15
max_z_accel: 100
square_corner_velocity: 5

[temperature_sensor Fly-D5]
sensor_type: temperature_mcu

[idle_timeout]
timeout: 600

[bed_mesh]
speed: 50
horizontal_move_z: 10
mesh_min: 5,-5
mesh_max: 205,170
probe_count: 8,8
mesh_pps: 2,2
algorithm: bicubic
bicubic_tension: 0.2
fade_start: 1
fade_end: 10

# ======================
# TRỤC X (Sensorless)
# ======================
[stepper_x]
step_pin: PC15
dir_pin: PC14
enable_pin: !PC2
microsteps: 16
rotation_distance: 40
endstop_pin: tmc2209_stepper_x:virtual_endstop
position_min: 0
position_endstop: 0
position_max: 225
homing_speed: 75          # Tốc độ đủ nhanh để Sensorless nhạy
homing_retract_dist: 5
homing_retract_speed: 3

[tmc2209 stepper_x]
uart_pin: PC13
interpolate: False
run_current: 0.8          # Giảm dòng khi home giúp máy đâm êm hơn
sense_resistor: 0.110
diag_pin: ^PB4            # Kiểm tra jumper Diag trên board
stealthchop_threshold: 0  # Tắt khi home để tránh lỗi triggered ảo
driver_SGTHRS: 110         # Nếu báo "still triggered", tăng lên 100. Nếu đâm mạnh quá, giảm về 60.

# ======================
# TRỤC Y (Sensorless)
# ======================
[stepper_y]
step_pin: PA1
dir_pin: PA0
enable_pin: !PA2
microsteps: 16
rotation_distance: 40
endstop_pin: tmc2209_stepper_y:virtual_endstop
position_endstop: 0
position_min: 0
position_max: 200
homing_speed: 60
homing_retract_dist: 5
homing_retract_speed: 3

[tmc2209 stepper_y]
uart_pin: PC3
uart_address: 0
interpolate: False
run_current: 0.8
sense_resistor: 0.110
stealthchop_threshold: 0
diag_pin: ^PB3
driver_SGTHRS: 90

# ======================
# TRỤC Z & Z1 (BLTouch)
# ======================
[stepper_z]
step_pin: PA5
dir_pin: PA4
enable_pin: !PA6
microsteps: 16
rotation_distance: 2
endstop_pin: probe:z_virtual_endstop # Dùng BLTouch làm endstop
position_max: 250
position_min: -5

[tmc2209 stepper_z]
uart_pin: PA3
run_current: 0.7

[stepper_z1]
step_pin: PB10
dir_pin: PB2
enable_pin: !PB11
microsteps: 16
rotation_distance: 2

[tmc2209 stepper_z1]
uart_pin: PB1
run_current: 0.7

[gcode_macro RELEASE_Z]
gcode:
    M18 Z
    M18 Z1
    
# ======================
# ĐẦU PHUN (EXTRUDER)
# ======================
[extruder]
step_pin: PC5
dir_pin: PC4
enable_pin: !PB0
microsteps: 16
full_steps_per_rotation: 200
rotation_distance: 22.5
gear_ratio: 50:10
nozzle_diameter: 0.4
filament_diameter: 1.75
heater_pin: PC6
sensor_type: EPCOS 100K B57560G104F
sensor_pin: PC1
min_temp: 0
max_temp: 300
min_extrude_temp: 170
control: pid
pid_Kp: 22.2
pid_Ki: 1.08
pid_Kd: 114.0
pressure_advance: 0.03
max_extrude_only_distance: 100

[tmc2209 extruder]
uart_pin: PA7
run_current: 0.7

# ======================
# BÀN NHIỆT (BED)
# ======================
[heater_bed]
heater_pin: PC7
sensor_type: EPCOS 100K B57560G104F
sensor_pin: PC0
min_temp: 0
max_temp: 120
control: pid
pid_Kp: 60.0
pid_Ki: 1.0
pid_Kd: 1000.0

[fan]
pin: PC8
kick_start_time: 1.0
off_below: 0.10
max_power: 1.0

# ======================
# BLTOUCH
# ======================
[bltouch]
sensor_pin: ^PB5
control_pin: PA8
x_offset: 0
y_offset: -30
#z_offset: 0     # Sau khi xong hãy chạy PROBE_CALIBRATE để chỉnh lại

[safe_z_home]
home_xy_position: 105,125 # Đưa Probe vào giữa bàn (đã cộng offset Y)
speed: 50
z_hop: 15
z_hop_speed: 5

[z_tilt]
z_positions:
    -15, 130    # Tọa độ cơ khí motor trái
    240, 130    # Tọa độ cơ khí motor phải
points:
    15, 60     # Điểm đo bên trái
    195, 60    # Điểm đo bên phải
speed: 100
horizontal_move_z: 10
retry_tolerance: 0.02
retries: 5

# ======================
# ĐÙN NHỰA
# ======================
[gcode_macro PREP_EXTRUDE]
gcode:
    {% set temp = params.T|default(220)|int %}
    {% set amount = params.E|default(30)|int %}

    G28
    G91
    G1 Z10 F1000

    M104 S{temp}
    M109 S{temp}
    
    M83
    G1 E{amount} F300
    
    RESPOND MSG="DONE"

# ======================
# KIỂM TRA VÙNG GIỚI HẠN
# ======================
[gcode_macro TEST_MESH_AREA]
gcode:
    {% set raw_min_x = printer.configfile.config["bed_mesh"]["mesh_min"].split(",")[0]|float %}
    {% set raw_min_y = printer.configfile.config["bed_mesh"]["mesh_min"].split(",")[1]|float %}
    {% set max_x = printer.configfile.config["bed_mesh"]["mesh_max"].split(",")[0]|float %}
    {% set max_y = printer.configfile.config["bed_mesh"]["mesh_max"].split(",")[1]|float %}

    {% set min_x = raw_min_x %}
    {% set min_y = raw_min_y if raw_min_y > 0 else 0 %}

    G90
    G1 Z10 F3000

    G1 X{min_x} Y{min_y} F6000
    G1 X{max_x} Y{min_y} F6000
    G1 X{max_x} Y{max_y} F6000
    G1 X{min_x} Y{max_y} F6000
    G1 X{min_x} Y{min_y} F6000

    RESPOND MSG="SAFE MESH AREA"

# ======================
# PRINT START
# ======================
[gcode_macro PRINT_START]
gcode:
    M140 S{params.BED|default(60)}
    M104 S{params.EXTRUDER|default(200)}

    M190 S{params.BED|default(60)}
    G28

    Z_TILT_ADJUST
    G28 Z

    BED_MESH_PROFILE LOAD=default

    M109 S{params.EXTRUDER|default(200)}

    G92 E0
    G1 Z5 F3000

    G90
    G1 X10 Y200 F6000
    G1 Z0.4
    G1 X120 E20 F1200
    G1 Y198
    G1 X10 E20 F1200
    G92 E0

# ======================
# PRINT END
# ======================
[gcode_macro PRINT_END]
gcode:
    {% set max_x = printer.configfile.config["stepper_x"]["position_max"]|float %}
    {% set max_y = printer.configfile.config["stepper_y"]["position_max"]|float %}
    {% set max_z = printer.configfile.config["stepper_z"]["position_max"]|float %}
    
    {% if printer.toolhead.position.x < (max_x - 20) %}
        {% set x_safe = 20.0 %}
    {% else %}
        {% set x_safe = -20.0 %}
    {% endif %}

    {% if printer.toolhead.position.y < (max_y - 20) %}
        {% set y_safe = 20.0 %}
    {% else %}
        {% set y_safe = -20.0 %}
    {% endif %}

    {% if printer.toolhead.position.z < (max_z - 2) %}
        {% set z_safe = 2.0 %}
    {% else %}
        {% set z_safe = max_z - printer.toolhead.position.z %}
    {% endif %}
    
    M400
    G92 E0
    G1 E-15.0 F3600
    G91
    G0 Z{z_safe} F3600
    G0 X{x_safe} Y{y_safe} F20000
    M104 S0
    M140 S0
    M106 S0
    G90
    G0 X{max_x / 2} Y{max_y} F3600
    BED_MESH_CLEAR

#*# <---------------------- SAVE_CONFIG ---------------------->
#*# DO NOT EDIT THIS BLOCK OR BELOW. The contents are auto-generated.
#*#
#*# [bltouch]
#*# z_offset = 2.025
#*#
#*# [bed_mesh default]
#*# version = 1
#*# points =
#*# 	-0.353125, -0.298750, -0.216875, -0.195625, -0.197500, -0.252500, -0.310000, -0.366875
#*# 	-0.281250, -0.221875, -0.178125, -0.111875, -0.123125, -0.163750, -0.216875, -0.299375
#*# 	-0.278125, -0.198750, -0.094375, -0.056875, -0.077500, -0.118125, -0.178750, -0.230000
#*# 	-0.221250, -0.166250, -0.087500, -0.025625, -0.024375, -0.051250, -0.088125, -0.173125
#*# 	-0.208125, -0.136250, -0.056250, -0.011250, 0.008125, -0.018125, -0.090625, -0.152500
#*# 	-0.240000, -0.163125, -0.087500, -0.022500, 0.008750, -0.033750, -0.076250, -0.161250
#*# 	-0.268125, -0.181250, -0.084375, 0.009375, 0.008125, -0.025000, -0.071250, -0.172500
#*# 	-0.301250, -0.185625, -0.099375, 0.006250, 0.043750, -0.018750, -0.066875, -0.187500
#*# x_count = 8
#*# y_count = 8
#*# mesh_x_pps = 2
#*# mesh_y_pps = 2
#*# algo = bicubic
#*# tension = 0.2
#*# min_x = 5.0
#*# max_x = 204.99
#*# min_y = -5.0
#*# max_y = 170.0
```
