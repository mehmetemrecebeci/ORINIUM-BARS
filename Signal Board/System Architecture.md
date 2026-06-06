# Genel Amaç

ORINIUM BARS Signal Board, aracın merkezi kontrol ve haberleşme kartıdır.

Bu kartın görevi:

- Power Board'u yönetmek
- Motor sürücüyü kontrol etmek
- Sensör verilerini toplamak
- Jetson ile haberleşmek
- Güvenlik sistemlerini yönetmek
- Telemetri verilerini oluşturmak
- Otonom sistem altyapısını desteklemek

Signal Board doğrudan yüksek güç taşımaz.

Tüm güç elektroniği işlemleri Power Board üzerinde gerçekleştirilir.

Signal Board sistemin "beyni" olarak çalışır.

---

# Basit Sistem Mimarisi

```
                    SIGNAL BOARD

                       STM32F407
                            |
       +--------------------+--------------------+
       |                    |                    |
       |                    |                    |
       v                    v                    v

     Jetson             Power Board          Sensors

                            |
                            |
                            v

                    Motor Driver Control
```

---

# Detaylı Sistem Mimarisi

```
================================================================================
                           ORINIUM BARS SIGNAL BOARD
================================================================================

                               +----------------+
                               | STM32F407VET6 |
                               +--------+-------+
                                        |
                                        |
     +----------------+-----------------+----------------+----------------+
     |                |                 |                |                |
     |                |                 |                |                |
     v                v                 v                v                v

 Jetson         Power Board         CAN Bus          Sensors         Telemetry

================================================================================
                             JETSON INTERFACE
================================================================================

Jetson Orin Nano

UART
CAN
Heartbeat
Status
Commands

Görevler:

- Görüntü işleme
- Yapay zeka
- Navigasyon
- Yol planlama
- Haritalama

================================================================================
                          POWER BOARD INTERFACE
================================================================================

Signal Board → Power Board

CONTACTOR_ENABLE

PRECHARGE_ENABLE

MOTOR_ENABLE

PWM_UH
PWM_UL

PWM_VH
PWM_VL

PWM_WH
PWM_WL

--------------------------------------------------------------------------------

Power Board → Signal Board

Battery Voltage

DC Bus Voltage

DC Bus Current

Phase Current

Temperature

Driver Fault

Contactor Feedback

Precharge Complete

================================================================================
                               CAN NETWORK
================================================================================

CAN Bus

Bağlanabilecek Birimler:

- Motor Driver
- Jetson
- Ek kontrol kartları
- Gelecekteki modüller

Araştırılacaklar:

- CAN topology
- CAN termination
- Isolated CAN
- CANopen
- Custom CAN protocol

================================================================================
                                SENSOR BUS
================================================================================

GPS

ZED-F9P

-------------------------

IMU

BNO055

-------------------------

Telemetry

RFD900x

-------------------------

Encoder Inputs

Encoder #1

Encoder #2

-------------------------

Limit Switch Inputs

Front
Rear
Left
Right

-------------------------

Emergency Stop Status

================================================================================
                           MOTOR CONTROL SECTION
================================================================================

STM32

      |
      |
      v

FOC Controller

      |
      |
      v

SVPWM Generator

      |
      |
      v

PWM Outputs

PWM_UH
PWM_UL

PWM_VH
PWM_VL

PWM_WH
PWM_WL

      |
      |
      v

Power Board Gate Driver

================================================================================
                            SAFETY MANAGEMENT
================================================================================

İzlenecekler:

- E-stop
- Over Current
- Over Voltage
- Under Voltage
- Over Temperature
- Driver Fault
- Communication Fault

Aksiyonlar:

- Motor Stop
- Contactor Open
- Fault Reporting
- Telemetry Warning

================================================================================
                              DEBUG SECTION
================================================================================

SWD

SWDIO
SWCLK
NRST
3V3
GND

USB-UART

Debug Console

CAN Analyzer Header

================================================================================
                               POWER INPUT
================================================================================

Power Board'dan gelir

24V

5V

3.3V

Koruma:

TVS

ESD

Polyfuse

Ferrite Bead

================================================================================
                              FUTURE EXPANSION
================================================================================

Ethernet

RS485

Additional CAN

Additional UART

Additional Sensors

Vision Modules

Redundant Safety System
```

---

# Signal Board Tasarım Hedefleri

Ana MCU

```
STM32F407VET6
```

---

Haberleşme

```
CAN

UART

I2C

SPI
```

---

Sensörler

```
GPS

IMU

Encoder

Limit Switch

E-Stop
```

---

Power Board Kontrolü

```
Contactor

Precharge

Motor Enable

Fault Management
```

---

Motor Kontrolü

```
FOC

SVPWM

Current Control

Speed Control
```

---

# Signal Board İçin Açılacak Sayfalar

## 01 - STM32 Architecture

- Clock yapısı
- Timer kullanımı
- DMA
- ADC
- Interrupt yapısı
- Pin planlama

---

## 02 - Jetson Interface

- UART protokolü
- CAN protokolü
- Veri paketleri
- Heartbeat sistemi

---

## 03 - Motor Control

- FOC
- SVPWM
- Current Loop
- Speed Loop
- Position Loop

---

## 04 - Sensor Interfaces

- GPS
- IMU
- Encoder
- Limit Switch
- E-stop

---

## 05 - Communication Protocols

- CAN
- UART
- I2C
- SPI

---

## 06 - Safety Management

- Fault State Machine
- Emergency Stop Logic
- Driver Fault Handling
- Watchdog

---

## 07 - Telemetry System

- RFD900x
- Veri paketleri
- Yer istasyonu protokolü

---

## 08 - PCB Design

- Stackup
- Connector placement
- Grounding
- ESD
- Signal integrity

---

## 09 - Firmware Architecture

- Task yapısı
- Driver katmanı
- Middleware
- Application layer

---

## 10 - Future Improvements

- Ethernet
- ROS2 Interface
- Sensor Fusion
- AI Interface
- Redundant MCU
- Functional Safety

```

```
