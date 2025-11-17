# pan_tilt_cam

# System Architecture

                         [ AGX ORIN ]
                        USB 3.0 PORT
                               |
                               v
                     +------------------------+
                     |    SLIP RING (USB3)    |
                     |  - SSTX+ / SSTX-        |
                     |  - SSRX+ / SSRX-        |
                     |  - USB2 D+ / D-         |
                     |  - GND / SHIELD         |
                     |  - 24V POWER            |
                     +-----------+-------------+
                                 |
                                 v
                +---------------------------------------+
                |            ROTATING BOARD             |
                |---------------------------------------|
                |  (1) POWER SYSTEM                     |
                |     - 24V -> 12V  (U2D2 Power Hub)    |
                |     - 24V -> 5V   (USB3 Hub Power)    |
                |                                       |
                |  (2) USB3 SUPER-SPEED PASS            |
                |     Slip Ring SSTX/SSRX -> USB3 Hub   |
                |                                       |
                |  (3) USB2 SPLIT (D+ / D-)             |
                |     - USB Hub Upstream                |
                |     - U2D2 USB Input                  |
                +-------------------+--------------------+
                                    |
                      +-------------+--------------+
                      |                            |
                      v                            v
             +--------------------+      +-------------------------+
             |     USB3.0 HUB     |      |     U2D2 + POWER HUB    |
             |   (Upstream Input) |      |-------------------------|
             +---------+----------+      | USB2 Data: D+ / D-       |
                       |                 | 12V Power Input           |
             +---------+----------+      | TTL/RS485 -> Dynamixel    |
             |                    |      +-------------+-------------+
             v                    v                    |
     +--------------+     +--------------+             v
     |  CAMERA 1    |     |  CAMERA 2    |      +--------------+
     |   USB 3.0     |     |   USB 2.0    |      |  DYNAMIXEL   |
     +--------------+     +--------------+      |   PAN / TILT  |
                                                +--------------+

# 1. Slip Ring (USB3 + 24V Power)

Slip Ring은 회전부와 고정부 사이의  
USB 3.0 고속 데이터 + USB2.0 + 24V 전원을 전달하는 핵심 장치이다.

## 🔌 Recommended Pin Map (12~22 Wire Slip Ring)

| Pin | Signal     | Description         |
|-----|------------|---------------------|
| 1   | SSTX+      | USB3 SuperSpeed TX+ |
| 2   | SSTX−      | USB3 SuperSpeed TX− |
| 3   | SSRX+      | USB3 SuperSpeed RX+ |
| 4   | SSRX−      | USB3 SuperSpeed RX− |
| 5   | USB2 D+    | USB2.0 Data +       |
| 6   | USB2 D−    | USB2.0 Data −       |
| 7   | USB GND    | USB Signal Ground   |
| 8   | USB Shield | Cable Shield        |
| 9   | +24V Power | 24V Power Input     |
| 10  | Power GND  | Power Ground        |
| 11  | Reserve    | 예비                |
| 12  | Reserve    | 예비                |

# 2. Rotating Board (Custom PCB)

Slip Ring으로부터 전달된 24V 전원과 USB 신호를  
변환, 보호, 분기(Split)하는 회전부 메인 PCB.

## 2.1 Power Protection Specification

| Protection Item      | Recommended Spec             | Purpose            |
|----------------------|------------------------------|--------------------|
| Fuse (F1)            | 24V / 3~5A Slow Blow         | 과전류 보호         |
| TVS Diode            | SMBJ28A or SMBJ33A           | Surge/ESD 보호     |
| Reverse MOSFET       | P-MOSFET 30V, Rds_on < 10mΩ  | 역전압 보호         |
| Input Capacitor      | 1000µF / 35V                 | 전원 안정화         |
| LC Filter (optional) | 10µH + 47µF                  | 고주파 노이즈 제거 |

## 2.2 Power Conversion

✔ 24V → 12V (U2D2 Power Hub용)  
Output: 12V / 3A 이상  

✔ 24V → 5V (USB3 Hub용)  
Output: 5V / 3A  

# 3. USB Routing

## 3.1 USB3.0 SuperSpeed Pass (SSTX/SSRX)
 
직결(pass-through)  

```
Slip Ring (SSTX/SSRX)
          |
          v
   Rotating Board
          |
          v
     USB3.0 Hub
```

## 3.2 USB2 Split (D+ / D−)

```
Slip Ring D+ / D-
        |
        v
  Rotating Board
        |
   +----+----+
   |         |
USB3 Hub   U2D2
(Upstream) (Motor Control)
```

# 4. U2D2 + Power Hub (Motor Control)

| Input               | Source                    |
|---------------------|---------------------------|
| USB2 Data (D+/D−)   | Rotating Board (Split)    |
| 12V Power           | Rotating Board DC/DC 12V  |
| Motor Output        | RS485 or TTL → Dynamixel  |

# 5. Dynamixel Pan/Tilt Motors

- RS485 또는 TTL 제어  
- U2D2 Power Hub로 12V 공급  (모터와 직접연결 될 수 도)
- Pan/Tilt 2축 제어  
- Sync Read/Write 고속 처리  

# Installation Guide

- Slip Ring → Rotating Board 연결  
- Rotating Board → USB Hub / U2D2 연결  
- Dynamixel → Power Hub 연결  
- USB3 Upstream → AGX Orin 연결  

# Bill of Materials (BOM)

- USB3 Slip Ring (12~22 wire)  
- Rotating Board PCB  
- USB3 Hub (MH4UC-U3)  
- U2D2 + Power Hub(power pin)  
- DC/DC 24→12V  
- DC/DC 24→5V  
- Dynamixel Pan/Tilt  
