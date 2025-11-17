# pan_tilt_cam

                         [ AGX ORIN ]
                        USB 3.0 PORT
                               |
                               v
                     +------------------+
                     |  SLIP RING (USB3)|
                     |  - SSTX+/SSTX-   |
                     |  - SSRX+/SSRX-   |
                     |  - D+, D-        |
                     |  - GND/SHIELD    |
                     |  - 24V POWER     |
                     +---------+--------+
                               |
                               v
                 +-----------------------------------+
                 |         ROTATING BOARD            |
                 |-----------------------------------|
                 |  (1) POWER                       |
                 |      24V -> 12V (U2D2 Power Hub) |
                 |      24V -> 5V  (USB3 Hub Power) |
                 |                                   |
                 |  (2) USB3 SUPER-SPEED             |
                 |      Slipring SS -> USB3 HUB      |
                 |                                   |
                 |  (3) USB2 SPLIT (D+ / D-)         |
                 |      -> USB HUB Upstream          |
                 |      -> U2D2 (Motor Control)      |
                 +-------------------+----------------+
                                     |
                        +------------+-------------+
                        |                          |
                        v                          v
                +------------------+      +------------------------+
                |   USB3.0 HUB    |      |   U2D2 + POWER HUB     |
                | (Upstream Line) |      |-------------------------|
                +--------+--------+      | USB2.0 Data (D+/D-)     |
                         |               | 12V Power Input          |
              +----------+---------+     | TTL/RS485 -> Dynamixel   |
              |                    |     +------------+-------------+
              v                    v                  |
       +-------------+     +-------------+            v
       | CAMERA 1    |     | CAMERA 2    |      +-----------+
       |   USB 3.0   |     |  USB 2.0    |      | DYNAMIXEL |
       +-------------+     +-------------+      |  PAN/TILT |
                                                 +-----------+


