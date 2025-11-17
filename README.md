# pan_tilt_cam

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
                |  (2) USB3 SUPER-SPEED PASS             |
                |     Slip Ring SSTX/SSRX -> USB3 Hub    |
                |                                       |
                |  (3) USB2 SPLIT (D+ / D-)              |
                |     - USB Hub Upstream                 |
                |     - U2D2 USB Input                   |
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


## 1. Slip Ring (USB3 + 24V Power)

Slip Ring은 회전부와 고정부 사이의
USB 3.0 고속 데이터 + USB2.0 + 24V 전원을 전달하는 핵심 장치이다.

🔌 Recommended Pin Map (12~22 Wire Slip Ring)
Pin	Signal	Description
1	SSTX+	USB3 SuperSpeed TX+
2	SSTX−	USB3 SuperSpeed TX−
3	SSRX+	USB3 SuperSpeed RX+
4	SSRX−	USB3 SuperSpeed RX−
5	USB2 D+	USB2.0 Data +
6	USB2 D−	USB2.0 Data −
7	USB GND	USB Signal Ground
8	USB Shield	Cable Shield
9	+24V Power	Power Input (from base)
10	Power GND	Power Ground
11	Reserve	예비
12	Reserve	예비
## 2. Rotating Board (Custom PCB)

Slip Ring으로부터 전달된 24V 전원과 USB 신호를 분기·변환·전달하는 회전부 메인 PCB이다.

### 2.1 Power Protection Specification
✔ 24V Input Protection
Component	Specification	Purpose
Fuse F1	24V 3~5A Slow Blow	과전류 보호
TVS Diode	SMBJ28A or SMBJ33A	서지/노이즈 보호
Reverse MOSFET	P-MOSFET 30V, Rds_on < 10mΩ	역극성 보호
Input Capacitor	1000µF / 35V	부하 안정화
LC Filter	10µH + 47µF	노이즈 필터링
### 2.2 Power Conversion
✔ 24V → 12V

Output: 12V / 3A 이상

Use: U2D2 Power Hub → Dynamixel

✔ 24V → 5V

Output: 5V / 3A

Use: USB3.0 Hub 전원

### 2.3 Slip Ring Port Design (PCB Side)
Function	Connector Type	Reason
USB3.0 신호	2×5 Header, 0.8mm pitch	SS/D+/D−/GND 안정적 연결
24V 전원	XT30 or JST-VH 2-pin	5A 이상 안정적
Shield	Single-Pin Shield Pad	노이즈 방지, 외피 차폐 분리 접지
## 3. USB Routing (SuperSpeed + USB2 Split)
### 3.1 USB3.0 SuperSpeed (SSTX/SSRX)

✔ Slip Ring → Rotating Board → USB3.0 Hub
✔ 직결(pass-through), split 불가
✔ 90Ω 차동 유지
✔ Via 최소화 권장

SS_TX/RX from Slip Ring
            |
            v
    Rotating Board (straight-through)
            |
            v
     USB3.0 Hub Upstream

### 3.2 USB2.0 Split (D+ / D−)

Slip Ring에서 올라온 USB2 라인을 보드에서 두 갈래로 분기한다.

Slip Ring USB2 (D+ / D-)
          |
   Rotating Board
          |
   +------+------+
   |             |
USB3 Hub     U2D2
(Upstream)   (Motor Control)

USB2 Split Rules

U2D2는 절대 USB Hub 뒤에 연결하지 않음

USB2는 SuperSpeed에 비해 tolerant하여 split 가능

D+/D− 라우팅 길이 균등화 필수

GND Plane은 연속적으로 유지

## 4. U2D2 + Power Hub (Motor Control)

USB2 Data: Split된 D+/D− 입력

Power: Rotating Board → 12V

Output: RS485 or TTL → Dynamixel

Dynamixel Protocol 2.0 대응

## 5. Dynamixel Pan/Tilt Motors

RS485/TTL 제어

U2D2 Power Hub로 전원 공급

Pan/Tilt 각 축 제어

Sync Read/Write 고속 제어 지원

## Installation / Assembly Guide

Slip Ring → Rotating Board 배선 연결

Rotating Board → USB Hub / U2D2 / Power Hub 연결

Dynamixel → U2D2 Power Hub 연결

USB3 단일 Upstream 포트 → AGX Orin 연결

## Bill of Materials (BOM)

USB3 Slip Ring (12~22 wire)

Rotating Board (Custom PCB)

USB3 Hub (MH4UC-U3)

U2D2 + Power Hub

DC/DC Converter 24→12V

DC/DC Converter 24→5V

Dynamixel Pan/Tilt Motors
