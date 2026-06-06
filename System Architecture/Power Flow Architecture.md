Güç akışı 72V LiFePO4 batarya ile başlar. Batarya çıkışı giriş koruma, sigorta, kontaktör ve precharge devresinden geçerek 72V DC bus hattını oluşturur. Bu DC bus hattı hem motor sürücü katını hem de yardımcı güç dönüştürücüleri besler.
'''
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
'''
