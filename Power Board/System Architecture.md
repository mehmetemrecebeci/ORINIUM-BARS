ORINIUM BARS Power Board, aracın yüksek güçlü elektronik altyapısını tek kart üzerinde toplamak amacıyla geliştirilen özgün bir güç elektroniği platformudur.

Kart üzerinde;

- 72V giriş koruma sistemi
- Kontaktör ve precharge devresi
- 72V → 24V 10A LLC dönüştürücü
- 24V → 5V 15A senkron buck dönüştürücü
- 5V → 3.3V yardımcı besleme
- 72V 2000W BLDC/PMSM motor sürücü
- Gerilim, akım ve sıcaklık ölçüm sistemleri

bulunacaktır.

Power Board, Signal Board tarafından kontrol edilen güç elektroniği katmanı olarak görev yapacaktır.

---

# Sistemin Basit Mimarisi

```
72V Battery
     |
Input Protection
     |
Contactor
     |
Precharge
     |
72V DC Bus
     |
     +--------------------+
     |                    |
     |                    |
     v                    v

72V→24V LLC         Motor Driver
24V→5V Buck         72V / 2kW
5V→3.3V             FOC + SVPWM
     |
     |
Power Outputs

24V
5V
3.3V
```

---

# Detaylı Güç Mimarisi

```
================================================================================
                           ORINIUM BARS POWER BOARD
================================================================================

72V LiFePO4 Battery
        |
        |
        v

+---------------------------+
| Input Protection Stage    |
|                           |
| - TVS Protection          |
| - Reverse Polarity        |
| - Over Voltage Protection |
| - Surge Protection        |
+-------------+-------------+
              |
              |
              v

+---------------------------+
| 200A Main Fuse            |
+-------------+-------------+
              |
              |
              v

+---------------------------+
| SW200 Contactor           |
+-------------+-------------+
              |
              |
              v

+---------------------------+
| Precharge Circuit         |
|                           |
| Relay + Precharge R       |
+-------------+-------------+
              |
              |
              v

==================================================================
                         72V MAIN DC BUS
==================================================================

              |
      +-------+---------------------------+
      |                                   |
      |                                   |
      v                                   v

+---------------------+        +---------------------------+
| LLC Converter       |        | Motor Driver Power Stage  |
|                     |        |                           |
| 72V -> 24V          |        | 72V BLDC/PMSM            |
| 24V / 10A           |        | 2000W Target             |
+----------+----------+        +-------------+------------+
           |                                  |
           |                                  |
           v                                  v

       24V BUS                        3-Phase Inverter
           |                                  |
           |                                  |
           +------------+                     |
                        |                     |
                        v                     v

             +-------------------+     U Phase
             | 24V -> 5V Buck    |     V Phase
             | 5V / 15A          |     W Phase
             +---------+---------+          |
                       |                    |
                       v                    |
                   5V BUS                   |
                       |                    |
                       v                    |
             +-------------------+          |
             | 5V -> 3.3V        |          |
             | 3.3V / 3A         |          |
             +---------+---------+          |
                       |                    |
                       v                    |
                   3.3V BUS                 |
                                            |
                                            v
                                   BLDC / PMSM Motor

==================================================================
                   MEASUREMENT & PROTECTION
==================================================================

Voltage Sense
-------------
- Battery Voltage
- DC Bus Voltage
- 24V Voltage
- 5V Voltage
- 3.3V Voltage

Current Sense
-------------
- DC Bus Current
- Motor Phase Current
- LLC Output Current
- 5V Output Current

Temperature Sense
-----------------
- MOSFET Temperature
- Transformer Temperature
- Board Temperature

Protection
----------
- Reverse Polarity
- Over Voltage
- Under Voltage
- Over Current
- Over Temperature
- Short Circuit
- Driver Fault
- Emergency Stop
```

---

# LLC Converter Tasarım Hedefleri

```
Input Voltage:
60V - 84V

Nominal Input:
72V

Output Voltage:
24V

Output Current:
10A

Output Power:
240W

Topology:
Half Bridge LLC

Target Efficiency:
>94%

Isolation:
Yes

Cooling:
Passive + Airflow
```

---

# 24V → 5V Buck Tasarım Hedefleri

```
Input:
24V

Output:
5V

Current:
15A

Power:
75W

Topology:
Synchronous Buck

Target Efficiency:
>92%
```

---

# 5V → 3.3V Besleme

```
Input:
5V

Output:
3.3V

Current:
3A

Usage:
STM32
CAN
Logic
Sensors
```

---

# Motor Driver Tasarım Hedefleri

```
Motor Type:
BLDC / PMSM

Bus Voltage:
72V

Power:
2000W

Control:
FOC

PWM Method:
SVPWM

Feedback:
Encoder / Hall

Communication:
CAN

Protection:
OCP
OVP
UVP
OTP
Shoot-through Protection
```

---

# Geliştirme Sırası

1. LLC Converter Tasarımı (72V → 24V 10A)
2. 24V → 5V Buck Tasarımı
3. 5V → 3.3V Besleme
4. Ölçüm ve Koruma Katı
5. Motor Driver Güç Katı
6. FOC + SVPWM Yazılımı
7. Power Board PCB Tasarımı
8. Signal Board Entegrasyonu

---

# Nihai Vizyon

ORINIUM BARS Power Board, hazır DC-DC dönüştürücüler ve hazır motor sürücüler yerine tamamen özgün olarak geliştirilen, yüksek verimli LLC güç kaynağı ve FOC tabanlı motor sürücü içeren modüler bir araç güç elektroniği platformu olacaktır.

Notion'da açılacak sayfalar:

01 System Architecture

02 LLC Converter

03 Buck Converter

04 Motor Driver Hardware

05 Motor Driver Control (FOC)

06 Measurements & Protection

07 Power Board PCB Design

08 Signal Board Design

09 Component Research

10 Future Improvements

# ORINIUM BARS PROJECT STRUCTURE

## 01 - System Architecture

Bu sayfanın amacı sistemin genel mimarisini belirlemektir.

Araştırılacak / Karar Verilecek Konular:

- Power Board görevleri
- Signal Board görevleri
- Kartlar arası haberleşme
- Güç akış diyagramı
- Veri akış diyagramı
- Sensör mimarisi
- Motor sürücü mimarisi
- Jetson entegrasyonu
- STM32 görevleri
- CAN mimarisi
- Güvenlik mimarisi
- E-stop mimarisi

Çıktı:

- Nihai blok diyagram
- Güç akış şeması
- Veri akış şeması

---

## 02 - LLC Converter

Amaç:

72V → 24V / 10A dönüştürücü geliştirmek.

Araştırılacak Konular:

- LLC çalışma prensibi
- Half Bridge LLC seçimi
- LLC Controller seçimi
- MOSFET seçimi
- Trafo tasarımı
- Lm hesabı
- Lr hesabı
- Cr hesabı
- Resonant frekans
- Switching frekansı
- Feedback yapısı
- Current limit
- OVP
- UVP
- Soft-start
- EMI filtre
- Soğutma

Power Designer çıktıları

Simülasyon sonuçları

Şematik tasarımı

PCB yerleşimi

Çıktı:

- Çalışan LLC tasarımı

---

## 03 - Buck Converter

Amaç:

24V → 5V / 15A dönüştürücü geliştirmek.

Araştırılacak Konular:

- Senkron buck topolojisi
- Controller seçimi
- MOSFET seçimi
- Endüktör hesabı
- Çıkış kapasitörleri
- Ripple hesabı
- Current limit
- Efficiency analizi
- EMI filtre

Çıktı:

- Çalışan 5V güç kaynağı

---

## 04 - Motor Driver Hardware

Amaç:

72V 2000W BLDC/PMSM motor sürücü geliştirmek.

Araştırılacak Konular:

Power Stage

- 3 faz inverter yapısı
- High side
- Low side
- DC-Link yapısı

MOSFET

- MOSFET seçimi
- VDS hesabı
- Akım hesabı
- Thermal hesaplar

Gate Driver

- Driver seçimi
- Bootstrap yapısı
- Deadtime

Current Sense

- Shunt
- Hall Sensor
- INA240
- İzole ölçüm seçenekleri

Voltage Sense

- DC Bus
- Motor gerilimi

Temperature Sense

- MOSFET
- PCB
- Soğutucu

Koruma

- Shoot-through
- OCP
- OVP
- UVP
- OTP

Çıktı:

- Tam motor sürücü güç katı

---

## 05 - Motor Driver Control (FOC)

Amaç:

Motor kontrol algoritmasını geliştirmek.

Araştırılacak Konular:

- Clarke Transform
- Park Transform
- Inverse Park
- SVPWM
- Space Vector mantığı
- Akım kontrol döngüsü
- Hız kontrol döngüsü
- Pozisyon kontrol döngüsü
- Encoder okuma
- Hall sensör okuma
- Rotor açı hesabı
- FOC matematiği
- Startup algoritması
- Fault yönetimi

Çıktı:

- FOC + SVPWM çalışan yazılım

---

## 06 - Measurements & Protection

Amaç:

Tüm ölçüm ve koruma sistemlerini toplamak.

Gerilim Ölçümü

- Battery Voltage
- DC Bus Voltage
- LLC Output
- 24V Bus
- 5V Bus
- 3.3V Bus

Akım Ölçümü

- Battery Current
- DC Bus Current
- Phase Current
- LLC Output Current

Sıcaklık Ölçümü

- MOSFET
- LLC Transformer
- PCB

Koruma

- Reverse Polarity
- Surge Protection
- OVP
- UVP
- OCP
- SCP
- OTP
- Driver Fault

NOT:

LT/LTC serisi çözümler araştırılacak.

Kesin entegre seçimi daha sonra yapılacak.

Çıktı:

- Nihai koruma mimarisi

---

## 07 - Power Board PCB Design

Amaç:

Power Board fiziksel tasarımı.

Araştırılacak Konular:

- 6 Layer Stackup
- 2 oz Copper
- Creepage
- Clearance
- HV-LV ayrımı
- Thermal yönetim
- LLC yerleşimi
- Motor Driver yerleşimi
- DC-Link yerleşimi
- Current Sense yerleşimi
- Soğutucu entegrasyonu

Çıktı:

- Üretilebilir PCB

---

## 08 - Signal Board Design

Amaç:

STM32 tabanlı VCU kartı geliştirmek.

Araştırılacak Konular:

- STM32F407
- CAN
- UART
- I2C
- SPI
- GPS
- IMU
- RFD900x
- Encoder girişleri
- E-stop girişleri
- Jetson arayüzü
- Power Board arayüzü
- SWD

Çıktı:

- Tam VCU kartı

---

## 09 - Component Research

Bu sayfa sadece parça araştırması için kullanılacaktır.

Başlıklar:

- LLC Controller
- Buck Controller
- MOSFET
- Gate Driver
- Current Sensor
- Voltage Sensor
- CAN Transceiver
- Encoder Interface
- Connector
- Capacitor
- Transformer Core
- MCU

NOT:

Bu sayfa sürekli güncellenecektir.

---

## 10 - Future Improvements

Rev2 ve Rev3 fikirleri.

Örnek:

- Dual Motor Driver
- 4000W Power Stage
- SiC MOSFET
- GaN Converter
- DAB Converter
- Battery Charger
- V2G
- Isolated CAN
- Ethernet
- Sensor Fusion Board
- AI Coprocessor
- Redundant Safety System
