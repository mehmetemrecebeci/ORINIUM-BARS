# ORINIUM BARS

# ORINIUM BARS — SYSTEM ARCHITECTURE

## Genel Amaç

ORINIUM BARS, 72V LiFePO4 batarya sistemi ile çalışan, uzaktan kontrol ve otonom görev yeteneklerine sahip paletli/tekerlekli insansız kara aracı platformudur.

Sistemin elektronik mimarisi üç ana bölümden oluşmaktadır:

- Power Board
- Motor Driver
- Signal Board

Amaç; güç elektroniği, motor kontrolü ve araç yönetimini modüler şekilde ayırarak bakım kolaylığı, genişletilebilirlik ve güvenilirlik sağlamaktır.


# Sistemin Genel Yapısı


                    ORINIUM BARS

                 +----------------+
                 |  Jetson Orin   |
                 +--------+-------+
                          |
                          |
                          v

                 +----------------+
                 | Signal Board   |
                 | STM32F407      |
                 +--------+-------+
                          |
                          | CAN
                          |
                          v

                 +----------------+
                 | Motor Driver   |
                 | STM32G4        |
                 +--------+-------+
                          |
                          |
                          v

                     BLDC Motor

72V Battery
      |
      |
      v

+--------------------+
| Power Board        |
+--------------------+

      |
      +---- 24V
      |
      +---- 5V
      |
      +---- 3.3V

      |
      +--------------------------+
                                 |
                                 v

                         Signal Board



# Güç Akış Diyagramı

72V LiFePO4 Battery
        |
        v

Input Protection

        |
        v

200A Fuse

        |
        v

Contactor

        |
        v

Precharge

        |
        v

72V DC BUS

        |
        +------------------------+
        |                        |
        |                        |
        v                        v

72V→24V LLC              Motor Driver

        |
        v

24V BUS

        |
        v

24V→5V Buck

        |
        v

5V BUS

        |
        v

5V→3.3V

        |
        v

Logic Systems
```

---

# Veri Akış Diyagramı

```
GPS
IMU
Encoder
Telemetry
Limit Sensors

      |
      |
      v

Signal Board
(STM32F407)

      |
      | CAN / UART
      |
      v

Jetson Orin Nano

      |
      |
      v

Navigation
AI
Path Planning

      |
      |
      v

Signal Board

      |
      | CAN
      |
      v

Motor Driver

      |
      |
      v

Motor
```

---

# Kartların Görevleri

## Power Board

Görevleri:

- 72V giriş koruması
- Sigorta yönetimi
- Kontaktör yönetimi
- Precharge yönetimi
- LLC dönüştürücü
- Buck dönüştürücü
- Güç dağıtımı
- Gerilim ölçümü
- Akım ölçümü
- Koruma sistemleri

Power Board yüksek güç elektroniği katıdır.

---

## Motor Driver

Görevleri:

- BLDC/PMSM sürme
- FOC algoritması
- SVPWM üretimi
- Akım ölçümü
- Encoder geri beslemesi
- CAN haberleşmesi
- Motor koruma sistemleri

Motor Driver güç dönüşüm ve motor kontrol katıdır.

---

## Signal Board

Görevleri:

- Araç yönetimi
- Sensör yönetimi
- Jetson haberleşmesi
- Telemetri
- Güvenlik yönetimi
- Görev yönetimi
- Durum makineleri
- Hata yönetimi

Signal Board sistemin merkezi kontrol kartıdır.

---

# Haberleşme Mimarisi


Jetson
   |
 UART / CAN
   |
STM32F407
   |
   | CAN
   |
STM32G4


Ana haberleşme omurgası:

- CAN Bus

Yardımcı haberleşmeler:

- UART
- I2C
- SPI

# Güvenlik Mimarisi

İzlenecek Hatalar:

- Over Voltage
- Under Voltage
- Over Current
- Over Temperature
- Motor Driver Fault
- Communication Fault
- Encoder Fault
- Emergency Stop

Hata Durumunda:

- Motor Disable
- PWM Disable
- Contactor Open
- Fault Reporting
- Telemetry Warning

---

# Sensör Mimarisi

Araçta Kullanılacak Sensörler

- ZED-F9P GPS
- BNO055 IMU
- Encoder #1
- Encoder #2
- Limit Sensörleri
- Motor Sıcaklık Sensörü
- DC Bus Ölçümleri
- Akım Ölçümleri

---

# Nihai Mimari Vizyonu

ORINIUM BARS elektronik mimarisi, güç elektroniği, motor kontrolü ve araç yönetimi fonksiyonlarını birbirinden ayıran modüler bir yapıda tasarlanacaktır.

Bu mimari sayesinde ilerleyen revizyonlarda:

- İkinci motor sürücü
- Daha yüksek güçlü inverter
- SiC tabanlı sürücüler
- Otonom sürüş modülleri
- Ethernet haberleşmesi
- Gelişmiş güvenlik sistemleri

mevcut yapıyı değiştirmeden sisteme eklenebilecektir.

ORINIUM BARS

├── System Architecture
│
├── Power Board
│   ├── Input Protection
│   ├── LLC Converter
│   ├── Buck Converter
│   ├── Measurements
│   ├── Protection
│   └── PCB Design
│
├── Motor Driver
│   ├── STM32G4
│   ├── Power Stage
│   ├── Gate Driver
│   ├── Current Sense
│   ├── Encoder
│   ├── FOC
│   ├── SVPWM
│   ├── CAN
│   ├── Protection
│   └── PCB Design
│
├── Signal Board
│   ├── STM32F407
│   ├── Jetson Interface
│   ├── Sensors
│   ├── Communication
│   ├── Telemetry
│   ├── Safety
│   └── Firmware
│
├── Component Research
│   ├── LLC Controller
│   ├── Buck Controller
│   ├── MOSFET
│   ├── Gate Driver
│   ├── Current Sensor
│   ├── Voltage Sensor
│   ├── CAN Transceiver
│   ├── Connectors
│   ├── Capacitors
│   ├── Magnetics
│   └── MCU Selection
│
└── Future Improvements
├── Dual Motor Driver
├── 4000W Version
├── Regenerative Braking
├── SiC MOSFET
├── GaN Converter
├── DAB Converter
├── Ethernet
├── Sensor Fusion
├── Redundant Safety
└── Autonomous Expansion


