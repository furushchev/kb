## PS4JOY

PS3コントローラがもう買えなさそうなので，代わりに
PS3JOYを使っていた環境となるべく同じにすることを目標に
PS4コントローラをROSで使ってみる．

### ドライバをインストール

```bash
sudo pip install ds4drv
```

### ペアリング

```bash
furushchev@azuki:~$ sudo bluetoothctl 
[NEW] Controller 7C:5C:F8:F8:0F:BF azuki [default]
[bluetooth]# scan on
Discovery started
[CHG] Controller 7C:5C:F8:F8:0F:BF Discovering: yes
[NEW] Device DB:AD:99:F0:51:B1 DB-AD-99-F0-51-B1
[NEW] Device 73:D8:6D:DB:B6:CD 73-D8-6D-DB-B6-CD
[CHG] Device 73:D8:6D:DB:B6:CD RSSI: -65
[CHG] Device 73:D8:6D:DB:B6:CD Name: 73B2
[CHG] Device 73:D8:6D:DB:B6:CD Alias: 73B2
[NEW] Device 43:51:A9:0D:DC:6F 43-51-A9-0D-DC-6F
[NEW] Device 94:65:2D:BF:ED:8C OnePlus 5T
[NEW] Device 80:56:F2:6A:85:B6 80-56-F2-6A-85-B6
[CHG] Device 80:56:F2:6A:85:B6 Name: BRAVIA
[CHG] Device 80:56:F2:6A:85:B6 Alias: BRAVIA
[CHG] Device 80:56:F2:6A:85:B6 UUIDs: 00001200-0000-1000-8000-00805f9b34fb
[NEW] Device 1C:66:6D:7B:C6:0A Wireless Controller  # ←コレ
[CHG] Device DB:AD:99:F0:51:B1 RSSI: -63
[CHG] Device 73:D8:6D:DB:B6:CD RSSI: -76
[NEW] Device 7A:66:83:17:E5:63 7A-66-83-17-E5-63
[NEW] Device B4:B6:76:E9:B2:D5 B4-B6-76-E9-B2-D5

[bluetooth]# pair 1C:66:6D:7B:C6:0A  # ここでPS+SHAREを長押し．
Attempting to pair with 1C:66:6D:7B:C6:0A
[CHG] Device 1C:66:6D:7B:C6:0A Connected: yes
[CHG] Device 1C:66:6D:7B:C6:0A Modalias: usb:v054Cp05C4d0100
[CHG] Device 1C:66:6D:7B:C6:0A UUIDs: 00001124-0000-1000-8000-00805f9b34fb
[CHG] Device 1C:66:6D:7B:C6:0A UUIDs: 00001200-0000-1000-8000-00805f9b34fb
[CHG] Device 1C:66:6D:7B:C6:0A Paired: yes
Pairing successful
[CHG] Device 1C:66:6D:7B:C6:0A Connected: no

[bluetooth]# trust 1C:66:6D:7B:C6:0A # trustしないと接続に失敗するっぽい？
[CHG] Device 1C:66:6D:7B:C6:0A Trusted: yes
Changing 1C:66:6D:7B:C6:0A trust succeeded
[bluetooth]# info 1C:66:6D:7B:C6:0A                                                                   
Device 1C:66:6D:7B:C6:0A
        Name: Wireless Controller
        Alias: Wireless Controller
        Class: 0x002508
        Icon: input-gaming
        Paired: yes
        Trusted: yes
        Blocked: no
        Connected: no
        LegacyPairing: no
        UUID: Human Interface Device... (00001124-0000-1000-8000-00805f9b34fb)
        UUID: PnP Information           (00001200-0000-1000-8000-00805f9b34fb)
        Modalias: usb:v054Cp05C4d0100

# ここでコントローラのPSを押したら，青色点灯になった．
[Wireless Controller]# info 1C:66:6D:7B:C6:0A                                                         
Device 1C:66:6D:7B:C6:0A
        Name: Wireless Controller
        Alias: Wireless Controller
        Class: 0x002508
        Icon: input-gaming
        Paired: yes
        Trusted: yes
        Blocked: no
        Connected: yes   # Connectedがyesに変わった
        LegacyPairing: no
        UUID: Human Interface Device... (00001124-0000-1000-8000-00805f9b34fb)
        UUID: PnP Information           (00001200-0000-1000-8000-00805f9b34fb)
        Modalias: usb:v054Cp05C4d0100

```

### ドライバ起動

```bash
furushchev@azuki:~$ sudo ds4drv --hidraw 
[info][controller 1] Created devices /dev/input/js0 (joystick) /dev/input/event14 (evdev) 
[info][hidraw] Scanning for devices
m[info][controller 1] Connected to Bluetooth Controller (1C:66:6D:7B:C6:0A hidraw0)
[info][hidraw] Scanning for devices
[info][controller 1] Battery: Fully charged
[warning][controller 1] Signal strength is low (18 reports/s)
```

つながったっぽい？

```bash
furushchev@azuki:/dev/input$ jstest --old /dev/input/js0                                                                                                                                                      
Driver version is 2.1.0.
Joystick (Sony Computer Entertainment Wireless Controller) has 14 axes (X, Y, Z, Rx, Ry, Rz, Throttle, Rudder, Wheel, Hat0X, Hat0Y, ?, ?, ?)
and 14 buttons (BtnX, BtnY, BtnZ, BtnTL, BtnTR, BtnTL2, BtnTR2, BtnSelect, BtnStart, BtnMode, BtnThumbL, BtnThumbR, ?, ?).
Testing ... (interrupt to exit)
^Ces: X:128 Y:128 Buttons: A:off B:off
```

### キーマップをPS3 Joyに揃える

- https://github.com/ros-drivers/joystick_drivers/pull/120 が必要．

```bash
cd /path/to/catkin/workspace
wstool set -t src ros-drivers/joystick_drivers --git https://github.com/furushchev/joystick_drivers.git -v remap -u -y
rosdep install --from-paths src -i -r -n -y
catkin build joy
source devel/setup.bash
```

- ノードを起動

```bash
roslaunch joy ps4joy.launch
```

- これで`/joy`に
PS3 joyと同じキーアサインでパブリッシュされる．
- PS4 joyのもともとのキーアサインは`/joy_orig`にパブリッシュされる．

## PS4JOYドライバの自動起動設定

- PS4JOYのドライバをOS起動時に自動起動する設定

```conf
# /etc/systemd/system/ds4drv.service
[Unit]
Description=ds4drv daemon
Requires=bluetooth.service
After=bluetooth.service

[Service]
ExecStart=/usr/local/bin/ds4drv --hidraw
Restart=on-abort

[Install]
WantedBy=bluetooth.target
```

```bash
sudo ln -sf /etc/systemd/system/ds4drv.service /etc/systemd/system/bluetooth.target.wants/ds4drv.service
```

- 設定の有効化

  - 先にPS3JOYのドライバを無効にする（Optional）

  ```bash
  sudo systemctl stop sixad.service
  sudo systemctl disable sixad.service
  ```
  
  - PS4JOYのドライバ設定を有効にする

  ```bash
  sudo systemctl enable ds4drv.service
  sudo systemctl start ds4drv.service
  ```
  
  - OSを再起動して動作を確認する
