# ORINIUM BARS — System Architecture

## 1. Project Overview

ORINIUM BARS, 72V LiFePO4 batarya sistemi ile çalışan modüler bir insansız kara aracı elektronik platformudur. Mevcut sistem mimarisinde Jetson Orin Nano, STM32F407, Kelly KLS7218S motor sürücüleri, 72V BLDC motorlar, GPS, IMU, lidar, kamera, telemetri ve güvenlik bileşenleri ayrı modüller halinde kullanılmaktadır.

Bu projenin hedefi, mevcut modüler sistemi daha profesyonel ve özgün bir elektronik altyapıya dönüştürmektir. Bu amaçla sistem iki ana özel PCB yapısı üzerine kurulacaktır:

- Power Board: PDU + güç dönüştürücüler + motor sürücü güç elektroniği
- Signal Board: STM32F407 tabanlı araç kontrol ve haberleşme kartı

Power Board, 72V batarya hattından gelen enerjiyi yönetir, yardımcı beslemeleri üretir ve motor sürücü katını barındırır. Signal Board ise sensörleri, Jetson haberleşmesini, güvenlik sinyallerini ve araç kontrol mantığını yönetir.

---

## 2. System Requirements

Sistemin temel elektriksel gereksinimleri 72V batarya mimarisi üzerine kuruludur. Ana sürüş sistemi iki adet 72V / 2000W BLDC motor üzerinden sağlanacaktır. Yardımcı elektronikler için 24V, 5V ve 3.3V hatları üretilecektir.

Temel gereksinimler:

- Ana batarya: 72V LiFePO4
- Ana motorlar: 2 × 72V / 2000W BLDC
- Yardımcı güç hattı: 24V
- Logic ve sensör beslemeleri: 5V ve 3.3V
- Ana kontrolcü: STM32F407
- Motor kontrolcü: STM32G4
- Üst seviye işlemci: Jetson Orin Nano
- Haberleşme: CAN, UART, I2C, SPI
- Güvenlik: E-stop, kontaktör, precharge, OVP, UVP, OCP, OTP

---

## 3. Overall System Architecture

ORINIUM BARS elektronik mimarisi üç ana bloktan oluşmaktadır:

```text
                    ORINIUM BARS

        +--------------------------------+
        |        Jetson Orin Nano        |
        |   AI / Vision / Navigation     |
        +---------------+----------------+
                        |
                        | UART / CAN
                        |
        +---------------v----------------+
        |          Signal Board          |
        |          STM32F407 VCU         |
        +---------------+----------------+
                        |
                        | CAN / Control Signals
                        |
        +---------------v----------------+
        |          Power Board           |
        |   PDU + LLC + Buck + Driver    |
        +---------------+----------------+
                        |
                        |
                 +------v------+
                 | BLDC Motor  |
                 +-------------+
````

Mevcut sistemde Kelly motor sürücüler ve Mean Well DC-DC modül gibi hazır çözümler yer almaktadır. Hedef mimaride ise bu yapılar kademeli olarak özgün Power Board ve Motor Driver tasarımıyla değiştirilecektir.

---

## 4. Power Flow Architecture

Güç akışı 72V LiFePO4 batarya ile başlar. Batarya çıkışı giriş koruma, sigorta, kontaktör ve precharge devresinden geçerek 72V DC bus hattını oluşturur. Bu DC bus hattı hem motor sürücü katını hem de yardımcı güç dönüştürücüleri besler.

```text
72V LiFePO4 Battery
        |
        v
Input Protection
        |
        v
200A Fuse
        |
        v
Contactor + Precharge
        |
        v
==================
    72V DC BUS
==================
        |
        +----------------------+
        |                      |
        v                      v
72V → 24V LLC             Motor Driver
24V / 10A                 72V / 2000W
        |
        v
24V BUS
        |
        v
24V → 5V Buck
5V / 15A
        |
        v
5V BUS
        |
        v
5V → 3.3V
3.3V / 3A
```

Bu yapı sayesinde ana motor gücü ile düşük gerilim yardımcı sistemleri ayrılır. 24V hattı far, fan, röle, yardımcı aktüatörler ve 5V buck dönüştürücü için kullanılır. 5V ve 3.3V hatları ise Jetson, sensörler, haberleşme modülleri ve logic devreleri besler.

---

## 5. Data Flow Architecture

Veri akışı Signal Board merkezlidir. Sensörlerden gelen veriler STM32F407 tarafından toplanır, gerekli durumlarda Jetson Orin Nano’ya aktarılır. Jetson görüntü işleme, otonom karar verme ve navigasyon görevlerini yürütür. Motor kontrol komutları ise Signal Board üzerinden Motor Driver tarafına iletilir.

```text
Sensors
GPS / IMU / Encoder / Limit Switch / Telemetry
        |
        v
Signal Board
STM32F407
        |
        | UART / CAN
        v
Jetson Orin Nano
AI / Vision / Navigation
        |
        v
Signal Board
Vehicle Control Logic
        |
        | CAN
        v
Motor Driver
STM32G4 + FOC + SVPWM
        |
        v
BLDC Motor
```

Bu yaklaşımda Jetson üst seviye karar verici olarak çalışırken, STM32F407 gerçek zamanlı araç kontrolü ve güvenlik yönetiminden sorumludur. STM32G4 ise motor sürücü üzerinde FOC ve SVPWM kontrolünü yürütür.

---

## 6. Vehicle Electronics Architecture

Araç elektronik mimarisi iki fiziksel PCB ve üç fonksiyonel blok üzerine kurulacaktır.

Fiziksel kartlar:

* Power Board
* Signal Board

Fonksiyonel bloklar:

* Power Board / PDU
* Motor Driver
* Signal Board / VCU

Power Board üzerinde 72V giriş koruması, kontaktör, precharge, LLC dönüştürücü, buck dönüştürücü, ölçüm devreleri ve motor sürücü güç katı bulunacaktır. Motor Driver kısmı aynı fiziksel Power Board üzerinde yer alacak, ancak Notion ve GitHub dokümantasyonunda ayrı bir mühendislik başlığı olarak ele alınacaktır.

Signal Board ise STM32F407 tabanlı merkezi araç kontrol kartıdır. Jetson, GPS, IMU, encoder, telemetri, limit switch, e-stop ve Power Board arayüzleri bu kart üzerinden yönetilecektir.

---

## 7. Communication Architecture

ORINIUM BARS haberleşme mimarisi CAN omurgası üzerine kurulacaktır. CAN hattı, Signal Board ile Motor Driver arasındaki ana haberleşme yöntemi olarak kullanılacaktır. Jetson ile STM32F407 arasında UART veya CAN kullanılabilir. GPS ve telemetri gibi modüller UART üzerinden bağlanacaktır.

Planlanan haberleşme yapısı:

```text
Jetson Orin Nano
        |
        | UART / CAN
        |
STM32F407 - Signal Board
        |
        | CAN
        |
STM32G4 - Motor Driver
```

Kullanılacak haberleşme protokolleri:

* CAN: Motor Driver ve araç içi haberleşme
* UART: Jetson, GPS, RFD900x telemetri
* I2C: IMU ve düşük hızlı sensörler
* SPI: Gerekirse yüksek hızlı çevresel birimler
* GPIO: E-stop, fault, enable ve status sinyalleri

RS485 ve RS232 bu revizyonda temel gereksinim olarak görülmemektedir. Ancak ileride endüstriyel sensör veya uzun mesafe haberleşme ihtiyacı doğarsa opsiyonel olarak değerlendirilebilir.

---

## 8. Safety Architecture

Güvenlik mimarisi hem donanımsal hem yazılımsal olarak ele alınacaktır. E-stop hattı yalnızca yazılım tarafından değil, doğrudan kontaktör ve motor enable hattını etkileyen donanımsal bir güvenlik zinciri olarak tasarlanacaktır.

İzlenecek hata durumları:

* Over Voltage
* Under Voltage
* Over Current
* Short Circuit
* Over Temperature
* Motor Driver Fault
* Communication Fault
* Encoder Fault
* Emergency Stop

Hata durumunda uygulanacak aksiyonlar:

* PWM disable
* Motor disable
* Kontaktör açma
* Precharge iptali
* Fault bilgisini Signal Board’a aktarma
* Telemetri üzerinden hata bildirimi
* Jetson’a sistem durumu gönderimi

Güvenlik sisteminin temel amacı, motor sürücü ve güç elektroniği tarafında oluşabilecek bir arızanın aracı veya elektronik sistemi tehlikeye sokmadan kontrollü şekilde durdurulmasını sağlamaktır.

---

## 9. Sensor Architecture

Araç üzerinde hem otonom sürüş hem de güvenlik için farklı sensörler kullanılacaktır. Görüntü işleme ve derinlik algılama tarafı Jetson tarafından yürütülürken, gerçek zamanlı araç kontrolü için gerekli sensörler STM32F407 üzerinden okunacaktır.

Sensör ve modüller:

* ZED-F9P / u-blox GPS
* BNO055 IMU
* Omron encoderler
* Limit sensörleri
* Lidar
* Kamera modülleri
* DC bus voltage sense
* Battery voltage sense
* Phase current sense
* Motor sıcaklık sensörü
* MOSFET sıcaklık sensörü
* E-stop status

Sensör mimarisi, gerçek zamanlı kontrol ve üst seviye otonom karar verme süreçlerini birbirinden ayıracak şekilde tasarlanacaktır. Böylece Jetson yüksek işlem gücü gerektiren görevleri, STM32F407 ise güvenlik ve deterministik kontrol görevlerini yürütecektir.

---

## 10. Future Expansion Architecture

ORINIUM BARS mimarisi gelecekte geliştirilebilir ve genişletilebilir olacak şekilde planlanmaktadır. İlk aşamada tek motor sürücü tasarımı hedeflense de ilerleyen revizyonlarda çift motor sürücü yapısına geçilebilir.

Gelecekte değerlendirilebilecek geliştirmeler:

* İkinci motor sürücü
* 4000W toplam sürüş gücü
* Regenerative braking
* SiC MOSFET tabanlı inverter
* GaN tabanlı DC-DC dönüştürücü
* DAB converter
* Ethernet haberleşmesi
* ROS2 entegrasyonu
* Sensor fusion
* Redundant safety architecture
* Gelişmiş telemetri ve veri kayıt sistemi

Bu mimari sayesinde sistem yalnızca mevcut ORINIUM BARS platformuna değil, ileride geliştirilecek farklı UGV veya elektrikli araç platformlarına da uyarlanabilecek modüler bir elektronik altyapı haline getirilebilir.

```
```
