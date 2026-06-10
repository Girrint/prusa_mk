# Prusa mk config.cfg

# Auto link up CAN ebb36 klipper

```
sudo nano /etc/udev/rules.d/99-can.rules
ACTION=="add", SUBSYSTEM=="net", KERNEL=="can0", RUN+="/usr/sbin/ip link set can0 type can bitrate 1000000 restart-ms 100", RUN+="/usr/sbin/ip link set can0 up"
```

reboot


```
sudo nano /etc/systemd/system/klipper.service
[Unit]
Description=Klipper 3D Printer Firmware
Documentation=https://www.klipper3d.org/
After=network-online.target
Wants=udev.target

[Service]
Type=simple
User=klipper
WorkingDirectory=/home/klipper/klipper
EnvironmentFile=/home/klipper/printer_data/systemd/klipper.env
ExecStart=/home/klipper/klippy-env/bin/python $KLIPPER_ARGS
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl daemon-reload
sudo systemctl restart klipper
sudo systemctl status klipper
```
