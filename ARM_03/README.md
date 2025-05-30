### **SI (Metric) Birim Tablosu – Veri Depolama ve İşlem Kapasitesi**

| Birim Adı | Sembol | Değeri (10 tabanında) | Sayısal Gösterim |
| --- | --- | --- | --- |
| Kilo | k | 10^3   | 1.000 (Bin) |
| Mega | M | 10⁶   | 1 Milyon |
| Giga | G | 10⁹    | 1 Milyar |
| Tera | T | 10¹²   | 1 Trilyon |
| Peta | P | 10¹⁵   | 1 Katrilyon |
| Exa | E | 10¹⁸   | 1 Kentilyon |

---

### 🔧 Mikroişlemcideki "Engine" Birimleri Nelerdir?

"Engine" kelimesi burada genellikle şu tür donanım bloklarını ifade eder:

| Engine Türü | Açıklama |
| --- | --- |
| **DMA Engine** | Direct Memory Access – Bellekten belleğe veri taşır, CPU’yu meşgul etmez |
| **Crypto Engine** | AES, SHA gibi şifreleme algoritmalarını donanım hızında çalıştırır |
| **Math Engine / FPU** | Floating Point Unit – Ondalıklı işlemleri donanım seviyesinde yapar |
| **Graphics Engine** | LCD veya grafik işlemleri için hızlandırma sağlar |
| **Motor Control Engine (MCE)** | BLDC motorlar için PWM ve Hall sensör desteği sağlar |
| **USB Engine / Ethernet Engine** | USB/Ethernet protokollerini donanımsal olarak işler |

Bu "engine" birimleri doğrudan **donanım içinde gömülüdür** ve işlemci çekirdeğinden bağımsız olarak ya da onunla koordineli şekilde çalışır.

---

### 🏭 Bu Engine'leri Kim Yapar?

Burada iki katmanlı bir yapı vardır:

### 1. **ARM Ltd.**

ARM firması sadece **çekirdeği** (CPU core) tasarlar (örneğin: Cortex-M0, Cortex-M4, Cortex-A53).

Engine’leri genellikle ARM yapmaz, sadece bazılarını **opsiyonel olarak lisanslanabilir şekilde** sunar (örneğin: FPU, TrustZone, NVIC).

### 2. **STMicroelectronics** gibi üreticiler

STM32 gibi işlemcileri geliştiren firmalar (ST, NXP, Renesas, vs.), işlemciye ARM çekirdeğini entegre eder **ve kendi donanım engine’lerini** ekler. Örneğin:

- ST'nin Crypto Engine’i (CRYP, HASH, RNG modülleri)
- Motor Control Timer (Advanced Timer)
- DMA2D (Chrom-Art Graphic Engine – STM32F7/H7)

---

### ⚙️ Bu Engine’ler Nasıl Çalışır?

Bu engine birimleri:

- Genellikle **özel yazmaçlar (register)** aracılığıyla konfigüre edilir
- **CPU’dan bağımsız çalışabilirler** (örneğin DMA motoru, CPU uyurken veri taşır)
- CPU ile **kesme (interrupt)** veya **bayrak (flag)** sistemiyle senkronize çalışırlar
- Gerçek zamanlı işlemlerde ciddi performans avantajı sağlarlar

---

### 🔍 Örnek: DMA Engine

DMA motoru, şu şekilde çalışır:

1. Kaynak ve hedef adresleri yazmaçlarla belirlenir
2. Veri boyutu ayarlanır
3. DMA başlatılır, CPU işlem yapmaz
4. İşlem bitince kesmeyle CPU bilgilendirilir

Bu sayede işlemci ana görevine odaklanabilirken, veri aktarımı donanım tarafından halledilir.

---

### 🔍 ARM Nedir? Açılımı ve Anlamı

**ARM** kelimesi, aslında tarihsel olarak iki farklı anlama gelmiştir:

---

### 📜 Tarihsel Açılımı:

1. **Acorn RISC Machine**
    - İlk ARM işlemcileri 1983’te İngiltere merkezli **Acorn Computers Ltd.** tarafından geliştirildi.
    - Amaç: BBC Micro bilgisayarları için düşük güç tüketimli ve hızlı bir işlemci üretmekti.
    - Bu yüzden ilk açılım: **Acorn RISC Machine**
2. **Advanced RISC Machines**
    - 1990’da ARM Ltd. şirketi kurulduğunda açılım şu şekilde değişti:
        
        **Advanced RISC Machines**
        
    - Bu, artık yalnızca donanım geliştirme yerine **RISC mimarili işlemci çekirdeği tasarımı** yapan bir firma haline geldiklerini gösteriyordu.

---

### 🧠 Anlamı ve Temel Özelliği:

- **RISC:** "Reduced Instruction Set Computer"
    - Az sayıda ama hızlı ve optimize edilmiş komut kümesi kullanır
    - Düşük güç tüketimi, yüksek verimlilik sağlar
    - Bu yüzden mobil cihazlar, gömülü sistemler ve IoT cihazlarında ARM çok popülerdir

---

### 💡 ARM Ne Yapar?

- ARM bir **mikroişlemci çekirdeği tasarım şirketidir**.
- Kendisi çip üretmez, **çekirdek mimarilerini** (örneğin: Cortex-M, Cortex-A) **lisanslar**.
- Bu çekirdekleri **STMicroelectronics**, **Qualcomm**, **Apple**, **Samsung**, **NXP** gibi firmalar kullanarak kendi mikrodenetleyici ve SoC'lerini üretir.

---

Özetle:

> ARM = Advanced RISC Machines
> 
> 
> Modern işlemcilerde yaygın olan, düşük güçle yüksek performans sağlayan, lisanslanabilir bir mikroişlemci mimarisi standardıdır.
> 

### 🎯 **Application (Gerçek Zamanlı Uygulama) için Mikrodenetleyici Nedir?**

**Real-time microcontroller** (gerçek zamanlı mikrodenetleyici), belirli işlemlerin **kesin süre sınırları içinde yapılmasını garanti eden** sistemler için tasarlanmış mikrodenetleyicidir.

Bu tür mikrodenetleyiciler, **otomasyon**, **motor kontrol**, **sensor feedback**, **endüstriyel sistemler**, **tıbbi cihazlar**, **otomotiv ECU'ları**, **robotikler**, gibi alanlarda kullanılır.

---

### 🧠 Gerçek Zamanlı Mikrodenetleyici Ne Demektir?

- **Gerçek zamanlı (Real-time)** sistemler, belirli bir olayın belirli bir zaman içinde işlenmesini zorunlu kılar.
- Bu süre sınırının kaçırılması, sistemin **başarısız olması** demektir (örneğin: bir hava yastığının geç açılması).
- **Hard real-time:** Gecikmeye tolerans yok (örneğin: kalp pili)
- **Soft real-time:** Gecikme kabul edilebilir ama istenmez (örneğin: video oynatma)

---

### 🔧 Uygun Mikrodenetleyici Özellikleri

| Özellik | Açıklama |
| --- | --- |
| **Düşük gecikmeli kesme sistemi** | NVIC gibi donanımsal kesme kontrol birimi |
| **Gerçek zamanlı timer'lar** | PWM, Input Capture, Output Compare destekleri |
| **DMA / Donanım motorları** | CPU müdahalesi olmadan veri aktarımı |
| **RTOS Desteği** | FreeRTOS, embOS gibi sistemler için hazır yapı |
| **Deterministik davranış** | Aynı giriş, aynı süreyle aynı çıkışı verir |

---

### 🛠️ Gerçek Zamanlı Uygulamalarda Popüler Mikrodenetleyiciler

| Mikrodenetleyici | Firma | Özellikler |
| --- | --- | --- |
| **STM32F4 / STM32H7** | STMicroelectronics | Yüksek hız, çoklu timer, RTOS dostu |
| **TMS320F28x** | Texas Instruments | Motor sürücü ve kontrol uygulamalarına özel |
| **PIC32** | Microchip | RTOS destekli, gömülü sistemler için uygun |
| **NXP LPC / i.MX RT** | NXP Semiconductors | ARM Cortex tabanlı, RTOS ve periferi güçlü |
| **ESP32** | Espressif | Wi-Fi + RTOS destekli, gömülü IoT çözümler |

---

### ✅ Örnek Uygulamalar

- Motor kontrol (servo, stepper, BLDC)
- Gerçek zamanlı veri toplama ve işleme
- Gömülü ses işleme
- Endüstriyel kontrol sistemleri (PLC gibi)
- Otonom drone/robot denetimi

---

### 🧠 **ISA (Instruction Set Architecture) – Komut Kümesi Mimarisi Nedir?**

**ISA (Instruction Set Architecture)**, bir işlemcinin çalışabileceği **komutların tamamını ve bu komutların nasıl çalıştığını tanımlayan mimari yapıdır**.

📌 *"Bir işlemcinin anlayabildiği dil, ISA'dır."*

---

### 📚 ISA Ne İşe Yarar?

ISA, yazılım ve donanım arasındaki bir **sözleşme** gibidir.

| Katman | Açıklama |
| --- | --- |
| **Donanım (CPU)** | Komutları fiziksel olarak çalıştırır |
| **ISA** | Yazılımın kullanabileceği komutları ve formatlarını tanımlar |
| **Yazılım (derleyici, işletim sistemi, uygulama)** | ISA’yı kullanarak donanımla iletişim kurar |

---

### 📌 ISA Bileşenleri

Bir ISA şunları içerir:

1. **Komut Kümesi (Instruction Set)**
    - Toplama, çıkarma, yükleme, dallanma gibi temel işlemler
    - Örnek: `ADD R1, R2, R3`
2. **Adresleme Modları**
    - Verilere nasıl erişileceğini tanımlar (doğrudan, dolaylı, offset’li vs.)
3. **Veri Türleri**
    - İşlemcinin desteklediği veri türleri (byte, word, float, double vs.)
4. **Register Yapısı**
    - Kaç tane register var, türleri ne (örneğin: genel amaçlı, özel)
5. **Bellek Erişimi**
    - Bellek nasıl adreslenir, kaç bit adresleme vardır?
6. **Interrupt & Exception Mekanizmaları**
    - Donanım/yazılım kesmelerine karşı sistemin davranışı

---

### 💻 Yaygın ISA Türleri

| ISA | Mimari Tipi | Açıklama |
| --- | --- | --- |
| **x86** | CISC | Intel/AMD işlemcilerde kullanılan karmaşık yapı |
| **ARM** | RISC | Düşük güç, gömülü sistemlerde yaygın |
| **RISC-V** | RISC | Açık kaynak ISA, özelleştirilebilir |
| **MIPS** | RISC | Eğitim ve gömülü sistemlerde kullanılır |
| **SPARC** | RISC | Sun Microsystems tarafından geliştirildi |

---

### ✅ ISA = Ne Değildir?

- **ISA ≠ Mikro mimari (microarchitecture)**
    
    Mikro mimari, ISA'nın **nasıl** çalıştırıldığını belirler (örneğin: pipeline, cache, branch prediction).
    

> Örneğin hem Cortex-M4 hem Cortex-A72 işlemcileri ARMv7 ISA'sını kullanabilir ama iç yapıları (pipeline yapısı, hızları, önbellek sistemleri) tamamen farklıdır.
> 

---

### 🧠 ARM ISA Sürümleri (ARMv7 ve Diğerleri) + **Thumb** Kavramı

---

### 📌 ARM ISA (Instruction Set Architecture) Sürümleri

ARM mimarisi, yıllar içinde gelişerek **ARMv1**'den başlayıp günümüzde **ARMv9**'a kadar evrim geçirmiştir. Her "ARMvX" versiyonu, çekirdek özellikler, veri yolları, komut setleri ve çalışma modları açısından yeni yetenekler ekler.

### 🔢 ARM ISA Versiyonları Genel Karşılaştırma:

| Versiyon | Yıl | Temel Özellikler | Kullanım Alanı |
| --- | --- | --- | --- |
| ARMv4T | 1994 | Thumb desteği (16-bit komutlar) | Eski ARM7TDMI çekirdeği |
| ARMv5TE | 1999 | DSP komutları, Gelişmiş Thumb | ARM9/ARM10 çekirdekleri |
| ARMv6 | 2001 | SIMD, cache geliştirmeleri | ARM11, ilk iPhone işlemcisi |
| **ARMv7-A** | 2005 | Uygulama profili, MMU, Linux desteği | Cortex-A serisi (A8, A9, A15…) |
| **ARMv7-R** | 2005 | Gerçek zamanlı profil (RTOS dostu) | Cortex-R işlemciler (örn. R5) |
| **ARMv7-M** | 2006 | Mikrodenetleyici profili, NVIC, düşük güç | Cortex-M3, M4, M7 |
| ARMv8-A | 2011 | 64-bit destekli, AArch64 | Cortex-A53, A72, A76, Apple M1/M2 |
| ARMv9-A | 2021 | Güvenlik (Confidential Compute), AI hızlandırma | Snapdragon 8 Gen 1+, Apple M3, vs. |

---

### 🎯 ARMv7 Profilleri

ARMv7 üç ana profile ayrılır:

| Profil | Açıklama | Örnek İşlemciler |
| --- | --- | --- |
| **v7-A** | Uygulama işlemcileri, yüksek performans | Cortex-A8, A9, A15 |
| **v7-R** | Gerçek zamanlı kontrol, deterministik yapı | Cortex-R4, R5 |
| **v7-M** | Gömülü sistemler, düşük güç tüketimi | Cortex-M3, M4, M7 |

---

### 🔄 **Thumb Nedir?**

**Thumb**, ARM mimarisine ait **16-bit uzunlukta komutlar içeren** alternatif bir komut kümesidir. Amaç:

- Bellek kullanımını azaltmak (16-bit komutlar)
- Daha az bant genişliği kullanmak
- Daha küçük kod boyutu üretmek
- Düşük güç tüketimi sağlamak

### 📏 Thumb vs ARM Komutları:

| Özellik | ARM Mode | Thumb Mode |
| --- | --- | --- |
| Komut Uzunluğu | 32-bit | 16-bit (Thumb) |
| Kod Yoğunluğu | Düşük | Yüksek |
| Hız | Daha hızlı (daha fazla register erişimi) | Biraz daha yavaş |
| Kod Boyutu | Daha büyük | Daha küçük (~30% az) |
| Kullanım | Performans odaklı | Bellek verimliliği |

> Not: Thumb-2 (ARMv6T-M ve ARMv7-M sonrası) ile birlikte, Thumb artık hem 16-bit hem de 32-bit komutları destekler.
> 

---

### 🔁 Modlar Arası Geçiş

ARM çekirdeği, çalışma esnasında **ARM ↔ Thumb** modları arasında geçiş yapabilir. Bu, genellikle `BX` (Branch and Exchange) komutu ile yapılır:

```
asm
Copy code
BX R0   ; R0 register'ında LSB=1 ise Thumb, LSB=0 ise ARM moduna geçilir
```

---

### 💡 Uygulama Örneği

- **STM32F4** (ARM Cortex-M4): Yalnızca **Thumb-2 ISA** destekler. ARM mod yoktur.
- **Raspberry Pi 2** (ARM Cortex-A7): ARMv7-A destekler, hem ARM hem Thumb modlarını çalıştırabilir.

### 🔍 STM32F103, STM32F407 ve Raspberry Pi – ARM ISA & Thumb Karşılaştırması

---

### 📌 STM32F103 (Cortex-M3)

| Özellik | Değer |
| --- | --- |
| **Çekirdek** | ARM Cortex-M3 |
| **ISA** | ARMv7-M |
| **Thumb** | ✔️ Thumb-2 desteği vardır |
| **ARM (32-bit) komutlar** | ❌ Desteklemez |
| **FPU** | ❌ Yok |
| **Kullanım Amacı** | Temel gömülü sistemler, düşük güç |

> Bu işlemcide yalnızca Thumb-2 komut seti çalışır. ARM mod yoktur.
> 

### 📌 STM32F407 (Cortex-M4)

| Özellik | Değer |
| --- | --- |
| **Çekirdek** | ARM Cortex-M4 |
| **ISA** | ARMv7-M |
| **Thumb** | ✔️ Thumb-2 |
| **ARM (32-bit) komutlar** | ❌ Desteklemez |
| **FPU** | ✔️ (donanımsal float işlemler) |
| **Kullanım Amacı** | DSP, motor kontrol, yüksek hızlı gömülü projeler |

> Aynı şekilde bu işlemci de yalnızca Thumb-2 komutlarını çalıştırır. ARM mod yoktur.
> 

---

### 📌 Raspberry Pi (Model 2 / 3 / 4)

| Model | Çekirdek | ISA | ARM Mod | Thumb Mod | 64-bit Desteği |
| --- | --- | --- | --- | --- | --- |
| Pi 2 | Cortex-A7 (ARMv7-A) | ARMv7-A | ✔️ | ✔️ | ❌ |
| Pi 3 | Cortex-A53 (ARMv8-A) | ARMv8-A | ✔️ | ✔️ | ✔️ (AArch64) |
| Pi 4 | Cortex-A72 (ARMv8-A) | ARMv8-A | ✔️ | ✔️ | ✔️ (AArch64) |

> ARMv7-A ve ARMv8-A çekirdekleri hem ARM modunda 32-bit komutları, hem de Thumb (ve Thumb-2) komutlarını çalıştırabilir.
> 
> 
> Ayrıca ARMv8-A ile birlikte **64-bit mod (AArch64)** da kullanılabilir (örneğin Raspberry Pi OS 64-bit sürümlerinde).
> 

---

### 📌 Özet Kıyaslama Tablosu

| Özellik | STM32F103 | STM32F407 | Raspberry Pi 3/4 |
| --- | --- | --- | --- |
| Çekirdek | Cortex-M3 | Cortex-M4 | Cortex-A53 / A72 |
| ISA | ARMv7-M | ARMv7-M | ARMv8-A |
| ARM (32-bit) Mod | ❌ | ❌ | ✔️ |
| Thumb-2 Desteği | ✔️ | ✔️ | ✔️ |
| 64-bit Desteği | ❌ | ❌ | ✔️ (ARMv8 AArch64) |
| FPU | ❌ | ✔️ | ✔️ (gelişmiş SIMD dâhil) |

---

Raspberry Pi gibi cihazlar hem ARM hem Thumb modlarını destekler çünkü Raspberry Pi’de işletim sistemleri (Linux) çalıştığı için tam ISA desteği gerekirken, STM32 gibi mikrodenetleyiciler daha hafif ve özel komut setiyle çalışır.

---

### ✅ `const` Olarak Yazılan Değişkenler

- C/C++ dilinde `const` ile tanımlanan değişkenler **sabit değerli** olup, program çalışırken **değiştirilemez**.
- Derleyici bu değişkeni genellikle **Flash belleğe (ROM)** yerleştirir, çünkü içeriği sabittir.

```c
const int threshold = 100;
```

- Bu değişken:
    - **RAM'de yer kaplamaz** (çünkü değişmeyecek)
    - Programın Flash belleğine yazılır (örneğin: 512 KB Flash içinde yer alır)
    - Bellek tüketimi ve kararlılığı artırır

---

### 💾 512 KB Flash Bellek

- **Flash bellek**, mikrodenetleyicideki **kalıcı bellektir** (program kodu, sabit veriler burada tutulur).
- STM32 gibi sistemlerde:
    - `.text` (program kodları),
    - `.rodata` (sabit veriler),
    - `const` tanımlı değişkenler burada bulunur.
- 512 KB = **524.288 byte** alan demektir.
- Tipik dağılım:
    - Kodlar (main, ISR’lar, fonksiyonlar)
    - Sabit tablolar (lookup table)
    - Başlangıçta RAM’e kopyalanacak veriler

---

### 🧠 128 KB RAM (SRAM)

- Mikrodenetleyicinin çalışırken kullandığı **geçici bellek** alanıdır.
- İçinde şunlar yer alır:
    - **Stack**: Fonksiyon çağrıları, yerel değişkenler
    - **Heap**: Dinamik bellek (malloc/new)
    - **Global ve static değişkenler**
    - **DMA buffer**, I/O veri alanları
- 128 KB = 131.072 byte
- Kod değil, **veri çalıştırma** alanıdır

---

### ⚡ 125 DMIPS (Dhrystone MIPS)

- **DMIPS = Dhrystone Million Instructions Per Second**
- Mikrodenetleyicinin **işlem gücünü** ölçmek için kullanılan bir birimdir
- 125 DMIPS:
    - Yaklaşık 125 milyon Dhrystone komutu/saniye çalıştırabilir demektir
    - Genelde 168 MHz civarı Cortex-M4 çekirdeklerinde bu seviyeye ulaşılır

### Karşılaştırma:

- **STM32F103**: ~ 45–50 DMIPS (72 MHz Cortex-M3)
- **STM32F407**: ~ 125 DMIPS (168 MHz Cortex-M4 + FPU)

---

### 🔌 13 Communication Interfaces – Nedir?

Bu ifade, mikrodenetleyicinin **donanımsal iletişim birimlerinin sayısını** gösterir. "13 COMM interfaces" demek, toplamda 13 adet bağımsız seri iletişim birimi bulunduğu anlamına gelir.

### Genellikle bunlar şunları kapsar:

| Arayüz | Açıklama |
| --- | --- |
| **USART/UART** | Asenkron seri iletişim (örneğin RS232, Bluetooth) |
| **SPI** | Senkron, hızlı veri iletişimi (örneğin sensörler) |
| **I2C** | Çoklu cihazlı haberleşme protokolü |
| **USB** | USB OTG FS/HS, USB-CDC gibi |
| **CAN** | Otomotiv ve endüstriyel veri yolu |
| **SDIO** | SD kart haberleşme arayüzü |
| **I2S** | Ses verisi iletimi için kullanılan SPI türevi |
| **Ethernet** | Bazı serilerde gömülü MAC arayüzü |

STM32F407 örneğinde şu dağılım olabilir:

| Tip | Adet |
| --- | --- |
| USART | 6 |
| SPI | 3 |
| I2C | 3 |
| CAN | 2 |
| USB | 1 |
| SDIO | 1 |

*(Not: bazıları pin paylaşımı nedeniyle aynı anda kullanılamayabilir)*

---

### 🔧 Toplam Sistemin Özeti (örnek STM32F407 gibi düşünülürse):

- **const değişkenler:** Flash'ta tutulur, RAM tüketimini azaltır
- **512 KB Flash:** Kodlar + sabit veriler burada
- **128 KB RAM:** Çalışma zamanındaki tüm geçici veri burada tutulur
- **125 DMIPS:** Güçlü performans, motor kontrol ve sinyal işleme için yeterlidir
- **13 COMM interfaces:** Birçok cihazla aynı anda haberleşmeyi mümkün kılar

---

Bu yapı genelde **yüksek performanslı gömülü sistemler**, **gerçek zamanlı kontrol**, **çoklu haberleşme** gerektiren projeler için idealdir (ör: veri loglama, robot kontrol, endüstriyel protokol çevirici vb).

### ⏱️ **PLL (Phase-Locked Loop) Nasıl Çalışır? – Ders Formatında Anlatım**

---

### 🎯 Amaç:

Mikrodenetleyicide kullanılan PLL (Phase-Locked Loop), genellikle düşük frekanslı bir saat kaynağını (örneğin **HSE: 8 MHz kristal**) alıp, bunu istenen yüksek frekansa (**168 MHz gibi**) çevirerek **sistemin çalışma frekansını yükseltmek** için kullanılır.

---

## 1️⃣ PLL Nedir?

- PLL, bir **frekans çarpıcı ve kontrolörüdür**.
- Girişteki saat sinyalini alır, frekansı **artırır (multiply)** veya **bölerek (divide)** farklı saatler üretir.
- Sistem saatini (SYSCLK), çevresel saatleri (peripheral clock) ve bazı zamanlayıcıları üretmek için kullanılır.

---

## 2️⃣ PLL'nin Temel Bileşenleri

Bir STM32 gibi işlemcideki PLL yapısı tipik olarak şunlardan oluşur:

```
     Giriş Kaynağı
     (HSE / HSI)
         |
         ▼
    / PLLM bölücü
         |
         ▼
    × PLLN çarpan
         |
         ▼
    / PLLP sistem bölücü ─────> SYSCLK (ana sistem saati)
         |
         ▼
    / PLLQ çevre bölücü ─────> USB, SDIO, RNG gibi çevre saatleri
```

| Bileşen | Görev |
| --- | --- |
| **PLLM** | Giriş frekansını düşürür (pre-divider) |
| **PLLN** | Yükseltme katsayısı (multiplier) |
| **PLLP** | Sistem saatini bölme katsayısı |
| **PLLQ** | USB gibi çevre birimleri için saat üretir |

---

## 3️⃣ Adım Adım PLL Hesabı (Örnek STM32F407, HSE = 8 MHz)

### Amaç: SYSCLK = 168 MHz elde etmek

```
F_vco = (HSE / PLLM) × PLLN
SYSCLK = F_vco / PLLP
USBCLK = F_vco / PLLQ

```

### Parametreler:

- PLLM = 8
- PLLN = 336
- PLLP = 2
- PLLQ = 7

### Hesap:

- `F_vco = (8 MHz / 8) × 336 = 336 MHz`
- `SYSCLK = 336 MHz / 2 = 168 MHz`
- `USBCLK = 336 MHz / 7 = 48 MHz` → ✔ USB için uygundur

---

## 4️⃣ Giriş Kaynakları

PLL'nin beslenebileceği saat kaynakları:

| Kaynak | Açıklama | Tipik Değer |
| --- | --- | --- |
| HSI | Dahili RC saat | 16 MHz |
| HSE | Harici kristal | 8 MHz |
| MSI / LSI | Düşük frekanslı saat | <1 MHz |

Genellikle **HSE** daha kararlı ve hassas olduğu için tercih edilir.

---

## 5️⃣ PLL Neden Kullanılır?

- Kristal (HSE) frekansları genellikle düşüktür (8 MHz, 12 MHz)
- Mikrodenetleyici daha yüksek frekansta çalışmak ister (168 MHz)
- PLL ile bu düşük frekans **katlanır**, mikrodenetleyici **maksimum performans** sağlar
- **USB**, **ADC**, **Timer** gibi çevre birimlerinin de istediği özel frekanslar PLL’den türetilir

---

## 6️⃣ PLL Kullanırken Dikkat Edilmesi Gerekenler

- **PLL çıkış frekansı** işlemcinin limitlerini aşmamalı (ör. SYSCLK max 168 MHz – STM32F4)
- **USB frekansı mutlaka 48 MHz olmalı** (USB doğru çalışması için)
- **PLL sadece yapılandırma aşamasında ayarlanabilir**, çalışırken değiştirilemez
- **PLL çalışırken saat kaynağı değiştirilemez**, önce geçici başka saat kullanılmalı

---

Bu yapı, gerçek zamanlı sistemlerde işlemcinin en verimli çalışması ve tüm çevrelerin stabil saat alması için hayati önemdedir. PLL olmadan sabit 8 MHz gibi düşük hızda çalışmak zorunda kalınır ve bu da birçok uygulama için yeterli değildir.

### 🧠 İşlemci, Veri Yolu, Core ve Bellek (Memory) – Kavramlar ve Aralarındaki Bağlantı

---

### 1️⃣ **İşlemci (CPU / Mikrodenetleyici)**

- Bir sistemin **hesaplama yapan merkezidir**.
- Komutları yürütür, verileri işler, karar verir.
- Mikrodenetleyicide çekirdek (core), registerlar, ALU, kontrol birimi içerir.

---

### 2️⃣ **Core (Çekirdek)**

- İşlemcinin komut yürütme birimidir.
- Tek çekirdekli (single-core) ya da çok çekirdekli (multi-core) olabilir.
- Her çekirdek bağımsız olarak komut işleyebilir.

> Örnek: STM32F103 → 1 core (Cortex-M3)
> 
> 
> Raspberry Pi 4 → 4 core (Cortex-A72)
> 

---

### 3️⃣ **Memory (Bellek)**

- Verilerin ve programların saklandığı yerdir.
- Türleri:
    - **Flash ROM**: Kod ve sabit veriler için (kalıcı)
    - **RAM (SRAM/DRAM)**: Geçici veriler için (çalışma esnasında)
    - **EEPROM** (bazı sistemlerde): Program tarafından yazılabilir kalıcı alan

---

### 4️⃣ **Veri Yolu (Data Bus)**

- İşlemci ile bellek ve çevre birimleri arasında **veri taşır**.
- Genişliği (8-bit, 16-bit, 32-bit, 64-bit) sistemin aynı anda işleyebileceği veri miktarını belirler.

---

### 🧩 Aralarındaki Bağıntılar

| Kavram | Görev | Diğerleriyle İlişkisi |
| --- | --- | --- |
| **Core** | Komut yürütür | Veriye ulaşmak için veri yolu kullanır, belleğe erişir |
| **Memory** | Veri ve kodu saklar | Core, kodu buradan çeker, veri okur/yazar |
| **Data Bus** | Veriyi taşır | Core ↔ Memory ↔ I/O arasında veri aktarım hattıdır |
| **Processor** | Tüm donanım birimlerini içerir | İçinde core, veri yolu, önbellek, interrupt controller vs. bulunur |

---

### 🔄 İşlem Akışı (Sistem Perspektifiyle)

1. **Core**, program sayacındaki (PC) adrese göre bellekteki kodu okur
2. Bu işlem **veri yolu** üzerinden gerçekleşir
3. Gerekli veriler yine veri yoluyla RAM’den alınır
4. İşlemci veriyi işler, sonucu yazmak gerekirse yine veri yolunu kullanarak RAM’e yazar

---

### 🔧 Sistem Performansında Etkileri

- **Veri yolu dar** ise (8-bit), yüksek işlemcili sistem bile yavaş veri alışverişi yapar
- **Çekirdek hızlı** ama RAM yavaşsa, darboğaz oluşur
- **Core ↔ Bus ↔ Memory** uyumu sistem tasarımında kritik önem taşır

---

### 🎯 Örnek (STM32F407):

- **Core**: Cortex-M4 (125 DMIPS)
- **Data Bus**: 32-bit
- **Memory**: 512 KB Flash, 128 KB RAM
- CPU → veri yolu → RAM/Flash üzerinden çalışır

> Bu yapıdaki en küçük gecikme bile performansı etkiler, özellikle gerçek zamanlı uygulamalarda (motor sürme, sinyal işleme, iletişim).
> 

---

Bu üç yapı – core, bus ve memory – bir sistemin **bilgi işleme omurgasını oluşturur** ve her biri bir diğeriyle **zamanlama ve genişlik açısından uyumlu** olmalıdır.

### 🔀 **Veri Yolunun Tek Şeritli Olması ve Bus Matrix (Veri Yolu Matrisi) Kavramı**

---

### 1️⃣ **Klasik (Tek Şeritli) Veri Yolu Yapısı**

Tek şeritli veri yolu, sistemde **tüm işlem birimlerinin (CPU, DMA, çevre birimleri)** **aynı yol üzerinden iletişim kurması** anlamına gelir.

Bu durumda:

- Aynı anda sadece **bir işlemci veya çevre birimi** veri yolunu kullanabilir
- **Çakışma (bus contention)** oluşur
- Sistem performansı **darboğaza** girer

### Şema (Temsili):

```
CPU ─┬─ RAM
     ├─ FLASH
     └─ UART
```

> Tüm bileşenler aynı yol üzerinde yarışır, biri veri gönderirken diğeri bekler.
> 

---

### 📉 **Tek Veri Yolunun Sınırları**

| Sorun | Açıklama |
| --- | --- |
| Bus Contention | Aynı anda iki birim veri göndermek ister, çakışır |
| Performans Düşüşü | DMA veri taşırken CPU beklemek zorunda kalır |
| Öncelik Sorunları | Kim önce kullanacak, karmaşık kontrol gerekir |

---

### 2️⃣ **Bus Matrix (Veri Yolu Matrisi) Nedir?**

**Bus Matrix**, çoklu master ve çoklu hedef birimlerin **aynı anda paralel çalışabilmesini** sağlayan **çok portlu anahtarlama (switching)** yapısıdır.

> "Tek şeritli yol" yerine, çok şeritli otoban mantığı getirir.
> 

### Şema:

```
           +------------+
Master 1 ─▶│            │──▶ RAM
Master 2 ─▶│ Bus Matrix │──▶ FLASH
DMA      ─▶│            │──▶ PERIPHERALS
           +------------+
```

- Master'lar: CPU, DMA, USB, vb.
- Target'lar: RAM, Flash, çevre birimleri

---

### 🧠 Bus Matrix Özellikleri

| Özellik | Açıklama |
| --- | --- |
| Çoklu master erişimi | Aynı anda birden fazla birim (CPU, DMA) bellekle haberleşebilir |
| Paralel veri aktarımı | CPU işlem yaparken DMA başka bir veriyi taşıyabilir |
| Daha az bekleme | Çakışmalar en aza iner |
| Donanımsal yönlendirme | Hangi master hangi hedefe bağlıysa dinamik olarak yönlendirilir |

---

### 🎯 STM32 Örneği: AHB Bus Matrix

STM32F4 gibi işlemcilerde:

- **CPU, DMA, USB gibi birimler** AHB Master’dır
- **SRAM, Flash, çevre birimleri** Target’tır
- Aralarında **AHB bus matrix** bulunur
- DMA ile bellekten çevreye veri taşınırken CPU başka veri işleyebilir

---

### ⚖️ Bus Matrix Kullanmanın Avantajı

| Geleneksel Bus | Bus Matrix |
| --- | --- |
| Tek master–tek hedef | Çoklu master–çoklu hedef |
| Tek işlem eşzamanlı | Paralel işlemler |
| Basit ama yavaş | Karmaşık ama yüksek verimli |
| Çakışma çok olur | Çakışma minimum |

---

### 📌 Sonuç:

- **Tek veri yolu = dar boğaz + çakışma**
- **Bus Matrix = paralel erişim + yüksek performans**
- Modern mikrodenetleyicilerde (özellikle yüksek hızlı STM32 serileri), **bus matrix yapısı standart hâle gelmiştir**

Bu yapı, çoklu kaynaklı sistemlerde **gerçek zamanlılık, yüksek veri aktarımı ve düşük gecikme** için vazgeçilmezdir.

### ⚡ Dahili 3.3V → 1.2V Regülatör ve **Supply Supervision** Kavramları

(STM32 ve benzeri mikrodenetleyiciler özelinde)

---

## 🧩 1️⃣ **Dahili Voltaj Regülatörü (3.3V → 1.2V)**

Modern mikrodenetleyicilerde (özellikle STM32F4, F7, H7 gibi yüksek hızlı olanlarda), iki farklı voltaj düzeyi vardır:

| Bölüm | Voltaj Seviyesi | Açıklama |
| --- | --- | --- |
| **I/O (GPIO, UART)** | 3.3V | Harici dünyayla haberleşme için |
| **Core (CPU, Flash)** | 1.2V | İşlemci çekirdeği, daha düşük güçte çalışır |

### Dahili Regülatör Ne Yapar?

- Sisteme 3.3V uygulanır
- Dahili LDO (Low Dropout Regülatör), bu 3.3V’u **1.2V’a indirerek işlemci çekirdeğini besler**
- Böylece:
    - Harici devre 3.3V’la çalışabilir
    - İç çekirdek düşük güçte, yüksek hızda çalışır (ör: 168 MHz @ 1.2V)

### Regülatör Modları

- **Normal Mode**: Maksimum performans
- **Low Power Mode**: Düşük güç tüketimi için
- **Bypass Mode**: Harici 1.2V uygulanır, dahili regülatör devre dışı bırakılır (gelişmiş sistemlerde)

---

## 🧠 2️⃣ **Supply Supervision** – Güç İzleme ve Koruma

Mikrodenetleyicinin güvenli çalışması için **besleme geriliminin doğru seviyede** olması gerekir. Aksi hâlde:

- **Brown-out reset** olabilir (CPU kararsız çalışır)
- **EEPROM/Flash yazımı bozulabilir**

Bu yüzden STM32 gibi MCU’lar aşağıdaki **güç izleme devrelerini içerir**:

---

### 🔍 PVD – **Programmable Voltage Detector**

- Besleme gerilimi (VDD) belirli bir eşiğin **altına düştüğünde** **kesme üretir**
- Uygulama, düşük gerilim durumu hakkında **önceden bilgi alabilir**
- Gelişmiş enerji yönetimi sağlar (ör: güvenli shutdown)

| Özellik | Değer |
| --- | --- |
| Eşik Seviyesi | 2.0V – 2.9V arası (seçilebilir) |
| Tetikleme | Interrupt veya flag ile |
| Kullanım | Güvenli enerji tasarrufu |

---

### 🛑 BOR – **Brown-Out Reset**

- **VDD belirli bir seviyenin altına düştüğünde** **otomatik reset üretir**
- Bu reset, çekirdeğin kararsız çalışmasını **önlemek için zorunludur**
- Eşik genelde sabittir (ör: 2.0V – 2.4V arası)

| Özellik | Değer |
| --- | --- |
| Geri Dönüş | VDD tekrar yükselince |
| Zorunlu Mudur? | Evet, güvenlik için kritik |
| Flash koruma | Yanlış yazmayı engeller |

---

### 🔒 PDR – **Power-Down Reset**

- VDD **minimum çalışma geriliminin altına düştüğünde** reset üretir
- Tüm sistem **tamamen kapanmaya** zorlanır
- Özellikle **Flash belleğin güvenliği** için önemlidir

| Özellik | Değer |
| --- | --- |
| Tetikleme | < Minimum VDD |
| Amaç | Tam sistem güvenliği |
| Etki | Reset + bazı devre blokları kapanır |

---

### 📌 Fark Tablosu

| Mekanizma | Amaç | Etki | Ayarlanabilir mi? |
| --- | --- | --- | --- |
| **PVD** | Bilgilendirme (erken uyarı) | Kesme üretir | ✅ |
| **BOR** | Gerilim düştü → reset | Donanımsal reset | ❌ (genelde sabit) |
| **PDR** | Kritik düşüş → kapanma | Reset + güç blokları kesilir | ❌ |

---

### 🧬 Bu Yapılar Nasıl Bağlantılıdır?

- **3.3V** harici kaynaktan gelir
- Dahili regülatör ile **1.2V core voltage** üretilir
- **PVD**, VDD düşmeye başlayınca **erken uyarı** verir
- **BOR**, VDD sınırın altına düşünce **reset** atar
- **PDR**, kritik seviye altına inince **tam sistem koruması** sağlar

Bu yapı sayesinde sistem hem **verimli enerji kullanımı**, hem de **güvenli çalışma** sağlar. Özellikle gömülü sistemlerde **voltaj dalgalanmaları** yaşanabileceği için bu koruma mekanizmaları hayati öneme sahiptir.

### 🧭 **Clock Tree (Saat Ağacı)** ve **Tek vs Çoklu PLL Yapısı** – Kavramsal ve Mimari Öğreti

---

## 1️⃣ Clock Tree (Saat Ağacı) Nedir?

**Clock tree**, bir mikrodenetleyici ya da mikroişlemci sisteminde **farklı modüllerin (CPU, RAM, ADC, UART, USB...) ihtiyaç duyduğu saat sinyallerinin** merkezi birimden türetilip, **katmanlı şekilde dağıtılmasını sağlayan sistemsel yapıdır.**

Bir sistemde tek bir ana saat (oscillator) vardır ama farklı modüller farklı frekanslarda çalışmak ister.

> Tüm bu frekansların dağılımı ve yönetimi, clock tree yapısıyla organize edilir.
> 

---

### 📐 Clock Tree – Temel Bileşenler

1. **Saat Kaynağı (Clock Source)**
    - HSE (harici kristal – örn: 8 MHz)
    - HSI (dahili RC – örn: 16 MHz)
    - LSE/LSI (düşük frekanslı saatler)
2. **PLL (Phase-Locked Loop)**
    - Saat sinyalini çarpan/bölen yapılar
    - Ana saatten yüksek frekans türetir (örneğin 8 MHz → 168 MHz)
3. **Prescaler’lar / Divider’lar**
    - Türetilmiş yüksek saatten daha düşük frekanslar üretilir (ADC, UART, Timer için)
4. **Multiplexer’lar (Mux)**
    - Hangi kaynak hangi bloğa yönlendirilecek, onu seçer

---

### 🏗️ Clock Tree'nin Hiyerarşik Yapısı (STM32 Örneğiyle)

```
        [HSE]       [HSI]
           \         /
            \       /
            [PLL] <--- kaynak
               |
        -------------------
       |        |         |
   SYSCLK     USBCLK     ADC_CLK
     |           |           |
   CPU       USB modülü    ADC modülü

```

Her modül, ihtiyaç duyduğu saat kaynağını **PLL çıkışından** veya **doğrudan bir osilatörden** seçebilir. Bu ağaç yapısı programla **RCC (Reset and Clock Control)** birimiyle yönetilir.

---

## 2️⃣ Tek PLL vs Çoklu PLL Yapısı

---

### ✅ Tek PLL (Basit Sistemler İçin – Örn: STM32F1/F3/F4)

- Tek bir PLL vardır
- Ana saat kaynağından tüm sistem saatleri buradan türetilir
- Daha az esneklik ama daha düşük güç tüketimi ve kontrol kolaylığı

### Avantaj:

- Düşük karmaşıklık
- Konfigürasyon basit
- Kod ve donanım maliyeti az

### Dezavantaj:

- Tüm çevre birimleri aynı PLL’e bağlı → **çakışma/kompromi**
- Örneğin: hem 168 MHz SYSCLK hem de 48 MHz USB isteniyorsa PLL parametreleri bu iki hedefe göre "denge" bulmalı

---

### ✅ Çoklu PLL (Gelişmiş Sistemler İçin – Örn: STM32H7, STM32MP1, SoC’ler)

- PLL1, PLL2, PLL3 gibi birden fazla PLL olabilir
- Her PLL farklı çevreleri besleyebilir

### Örnek Yapı:

| PLL | Hangi modülleri besler? |
| --- | --- |
| **PLL1** | SYSCLK (CPU, RAM) |
| **PLL2** | ADC, DAC, FDCAN |
| **PLL3** | USB, SDMMC, RNG, SAI |

### Avantaj:

- Her çevre için **optimum frekans** sağlanabilir
- USB → 48 MHz, ADC → 36 MHz, CPU → 400 MHz gibi
- **Gürültü izolasyonu** ve **modül bazlı kontrol** kolaylığı

### Dezavantaj:

- Daha karmaşık RCC konfigürasyonu
- Donanım karmaşıklığı ve güç tüketimi biraz artabilir

---

### 📊 Karşılaştırmalı Tablo

| Özellik | Tek PLL | Çoklu PLL |
| --- | --- | --- |
| Mimarî Basitlik | ✔ Basit | ❌ Daha karmaşık |
| Esneklik | ❌ Az | ✔ Çok |
| Güç tüketimi | ✔ Daha az | ❌ Biraz daha fazla |
| Çakışma riski | ❌ Fazla (tek kaynaktan türetme) | ✔ Az (ayrı PLL'ler) |
| Uygulama Tipi | Küçük sistemler (F1-F4) | Gelişmiş sistemler (H7, MP1, Pi) |

---

## 🎯 Sonuç

- **Clock tree**, tüm sistem saatlerinin dallanıp dağıldığı merkezi yapıdır
- **PLL**, bu ağacın merkezinde frekans üretici olarak çalışır
- **Tek PLL**, sade sistemlerde yeterli
- **Çoklu PLL**, yüksek performanslı, çok çevreli sistemlerde kaçınılmaz

Bu mimari sayesinde her donanım birimi, ihtiyaç duyduğu hassas ve optimize edilmiş saat sinyaline ulaşabilir. Sistemin kararlılığı, gücü ve gerçek zamanlılığı doğrudan bu saat ağacının doğru tasarlanmasına bağlıdır.