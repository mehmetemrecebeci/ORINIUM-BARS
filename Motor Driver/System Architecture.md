## Genel Amaç

ORINIUM BARS Motor Driver, 72V batarya sisteminden beslenen BLDC/PMSM motorların yüksek verimli şekilde sürülmesi amacıyla geliştirilen FOC tabanlı motor kontrol platformudur.

Sistem;

- 72V DC Bus
- STM32G4 Motor Control MCU
- FOC Algoritması
- SVPWM Modülasyonu
- 3 Faz İnverter
- Encoder / Hall Feedback
- Akım Ölçümü
- Koruma Sistemleri

içerecektir.

---

# Basit Mimari

```
72V DC Bus
      |
      |
      v

3 Phase Inverter
      |
      |
      v

BLDC / PMSM Motor

      ^
      |
      |

 STM32G4
 FOC + SVPWM
```

---

# Detaylı Sistem Mimarisi

```
================================================================================
                          ORINIUM BARS MOTOR DRIVER
================================================================================

                        72V DC BUS
                             |
                             |
                             v

                 +-----------------------+
                 | DC-Link Capacitors    |
                 |                       |
                 | Bulk Capacitors       |
                 | Film Capacitors       |
                 +-----------+-----------+
                             |
                             |
                             v

================================================================================
                           POWER STAGE
================================================================================

                 +--------------------------------+
                 |                                |
                 |      3 Phase Inverter          |
                 |                                |
                 |  Q1      Q3      Q5            |
                 |   |       |       |            |
                 |   +---U---+---V---+---W        |
                 |   |       |       |            |
                 |  Q2      Q4      Q6            |
                 |                                |
                 +---------------+----------------+
                                 |
                                 |
                                 v

                           BLDC / PMSM
                               Motor

================================================================================
                           GATE DRIVER
================================================================================

STM32 PWM Outputs

PWM_UH
PWM_UL

PWM_VH
PWM_VL

PWM_WH
PWM_WL

          |
          |
          v

     Gate Driver IC

          |
          |
          v

      MOSFET Gates

================================================================================
                        CONTROL MCU
================================================================================

                 +------------------------+
                 | STM32G4 Series MCU     |
                 |                        |
                 | FOC                    |
                 | SVPWM                  |
                 | Current Control        |
                 | Speed Control          |
                 | Protection Logic       |
                 +------------+-----------+
                              |
                              |
                              v

                         CAN Interface

================================================================================
                         CURRENT FEEDBACK
================================================================================

Possible Methods:

1)
Shunt + INA240

2)
Hall Current Sensor

------------------------------------------------

Measurements:

Phase U Current

Phase V Current

Phase W Current

DC Bus Current

================================================================================
                         POSITION FEEDBACK
================================================================================

Encoder

A
B
Z

or

Hall Sensors

Hall A
Hall B
Hall C

================================================================================
                        VOLTAGE FEEDBACK
================================================================================

Measured Signals

DC Bus Voltage

Motor Phase Voltage

Driver Supply Voltage

================================================================================
                      TEMPERATURE FEEDBACK
================================================================================

Measurements

MOSFET Temperature

PCB Temperature

Motor Temperature

================================================================================
                           PROTECTION
================================================================================

Over Current

Short Circuit

Shoot Through

Over Voltage

Under Voltage

Over Temperature

Gate Driver Fault

Encoder Fault

Communication Fault

================================================================================
                       COMMUNICATION
================================================================================

CAN Bus

Motor Driver Status

Motor Speed

Current

Voltage

Temperature

Fault Status

Commands

Speed Command

Torque Command

Enable / Disable
```

---

# Hedef Teknik Özellikler

```
Input Voltage:
60V - 84V

Nominal Voltage:
72V

Motor Type:
BLDC / PMSM

Power:
2000W

Control:
FOC

PWM:
SVPWM

Communication:
CAN

Feedback:
Encoder / Hall

Current Sense:
Shunt + Amplifier

Protection:
Full
```

---

# Motor Driver İçin Açılacak Sayfalar

## 01 - Power Stage

- 3 faz inverter
- Half bridge yapısı
- MOSFET seçimi
- DC-Link tasarımı
- Snubber yapıları

---

## 02 - STM32G4 Architecture

- G4 serisi araştırması
- ADC kullanımı
- Timer kullanımı
- DMA
- Comparator
- Motor Control SDK

---

## 03 - Gate Driver

- Driver seçimi
- Bootstrap tasarımı
- Deadtime
- UVLO
- Fault pinleri

---

## 04 - Current Measurement

- Shunt seçimi
- INA240
- Hall sensörler
- ADC ölçekleme

---

## 05 - Encoder Interface

- Quadrature Encoder
- Hall Sensor
- Speed Calculation
- Position Calculation

---

## 06 - FOC Control

- Clarke
- Park
- Inverse Park
- Current Loop
- Speed Loop

---

## 07 - SVPWM

- Space Vector mantığı
- Sector hesapları
- PWM üretimi

---

## 08 - Protection System

- OCP
- SCP
- OTP
- UVP
- OVP
- Fault State Machine

---

## 09 - CAN Communication

- CAN Protocol
- Message IDs
- Motor Commands
- Telemetry

---

## 10 - PCB Design

- 6 Layer Stackup
- Current Paths
- Gate Driver Layout
- Current Sense Layout
- Thermal Management

---

## 11 - Simulation

- LTspice
- PSIM
- Motor Model
- FOC Simulation

---

## 12 - Future Improvements

- Dual Motor Driver
- 4000W Version
- SiC MOSFET
- Sensorless FOC
- Regenerative Braking
- Field Weakening

```

Başkan şu haliyle artık mimari şöyle oturdu:

```text
Power Board
├── Input Protection
├── LLC Converter
├── Buck Converter
└── Power Distribution

Motor Driver
├── STM32G4
├── FOC
├── SVPWM
├── Gate Driver
├── Current Sense
├── CAN
└── 3 Phase Inverter

Signal Board
├── STM32F407
├── Jetson Interface
├── GPS
├── IMU
├── Telemetry
└── Vehicle Management
```

Bu ayrım ileride şematik çizerken inanılmaz rahatlatacak çünkü artık her blok kendi başına yönetilebilir bir proje haline geldi. 🔥
