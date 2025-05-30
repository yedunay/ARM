# Gömülü Sistemler Dersi - Hafta 2: Mimariler, Komutlar ve Donanım Etkileşimi

Bu ders, mikroişlemler ve mikrodenetleyicilerin temel mimari yapılarından başlayarak, komut sistemleri, donanım-haberleşme ara birimleri ve güç besleme hatlarındaki stabiliteyi sağlayan uygulamalı elektronik prensipleri içerir.

## 🔨 RISC ve CISC Mimarileri

### RISC (Reduced Instruction Set Computing)

* Sabit uzunlukta, basit komutlar
* Hızlı, tek saat döngüsünde çalışan talimatlar
* Yükle-çalıştır modelini benimser
* Daha çok register kullanımı → Daha az bellek erişimi
* Örnek: ARM, MIPS, RISC-V

### CISC (Complex Instruction Set Computing)

* Değişken uzunlukta, karmaşık komutlar
* Bir komut birden fazla işlem yapabilir
* Daha az register, daha fazla bellek erişimi
* Örnek: Intel x86, Z80, VAX

| Özellik              | RISC  | CISC         |
| -------------------- | ----- | ------------ |
| Komut Uzunluğu       | Sabit | Değişken     |
| Saat Döngüsü         | 1     | Birden fazla |
| Donanım Karmaşıklığı | Basit | Karmaşık     |
| Bellek Erişimi       | Az    | Fazla        |

## 🎓 MIPS ve VLSI

### MIPS

* **Microprocessor without Interlocked Pipeline Stages**: RISC mimarili işlemler
* **Million Instructions Per Second**: Hız metrik birimi
* Kullanım: Router, oyun konsolu, bazı gömülü sistemler

### VLSI (Very Large Scale Integration)

* 100.000+ transistörü tek yongaya entegre eden çip tasarım tekniği
* Modern mikroişlemler, RAM, FPGA vs. bu teknolojiyle üretilir

## 📝 Komut (Instruction) Yapısı

### Parçaları:

* **Opcode**: Hangi işlem?
* **Operands**: Kime/neyi uygulayacak?
* **Addressing Mode**: Operanda nasıl erişecek?

### Komut Örnekleri

| Komut          | Anlamı                          |
| -------------- | ------------------------------- |
| MOV R1, R2     | R2 → R1                         |
| ADD R1, R2, R3 | R1 = R2 + R3                    |
| LDR R1, \[R2]  | R2 adresinden veri al, R1'e koy |
| STR R1, \[R2]  | R1'deki veriyi R2 adresine yaz  |

### Komut Türleri

* Aritmetik: ADD, SUB, MUL, DIV
* Mantıksal: AND, OR, XOR
* Transfer: MOV, LDR, STR
* Kontrol: B, CMP, BEQ
* I/O: IN, OUT

## 💡 Kondansatör Kullanımı

### Neden + ve - arasına konur?

1. **Gürültü ve Ripple Azaltma**: Voltaj dalgalanmalarını filtreler
2. **Decoupling (Bypass)**: Ani akım çekimlerinde voltaj stabilitesi sağlar
3. **AC Filtreleme**: DC devreye karışan istenmeyen AC bileşenleri bastırır
4. **Enerji Depolama**: Geçici güç ihtiyacını karşılar

### Pratikte

* 100nF seramik kondansatör mikrodenetleyiciye yakın konur
* 10-100µF elektrolitik kondansatör besleme kaynağına yakın yerleştirilir

## 💻 USB-UART Kullanımı

### Neden Kullanılır?

1. Bilgisayarda UART port yok, USB ile UART haberleşmesi sağlanır
2. Mikrodenetleyici ile veri gönderme/alma
3. Seri monitör ve debug için
4. Bootloader ile yazılım yükleme
5. Endüstriyel sensörlerden veri alma

## ⚖️ Kondansatör ve Besleme Stabilitesi

1. Uzun iz ve kablolar → dirençli yol → voltaj düşüşü → kondansatör telafi eder
2. Ani akım değişimleri → endüktif parazit → kondansatör filtreler
3. Seramik ve elektrolitik kondansatör kombinasyonu kullanılır
