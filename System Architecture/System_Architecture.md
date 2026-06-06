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
