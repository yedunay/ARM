### 🧠 Mikrodenetleyici Sistemlerinde: **Processor**, **Internal/External**, **Peripherals** Kavramları

Ve **Core**, **Internal**, **External**, **Vendor Peripherals** Ayrımları

---

## ⚙️ 1️⃣ **Processor (Çekirdek / CPU)**

- Mikrodenetleyicinin **merkezi işlem birimidir**
- Komutları çalıştırır, veri işler, karar verir
- ARM tabanlı sistemlerde bu bir **Cortex-M0/M3/M4/M7** gibi bir çekirdektir
- İçinde şunlar bulunur:
    - ALU (Arithmetic Logic Unit)
    - Register’lar
    - PC, SP, LR, PSR gibi kontrol register'ları
    - NVIC, SysTick (core peripherals kısmında anlatılacak)

---

## 🧩 2️⃣ **Peripheral (Çevre Birimi)**

- Processor dışında kalan, özel görevli donanım bloklarıdır
- CPU ile veri alışverişi yaparak sistemin dış dünya ile etkileşmesini sağlar
- Örn: GPIO, UART, SPI, ADC, TIM, USB, CAN…

---

## 🛠️ 3️⃣ **Internal vs External Kavramı**

| Kavram | Açıklama | Örnek |
| --- | --- | --- |
| **Internal** | Mikrodenetleyici içinde entegre | GPIO, USART, Timer, ADC |
| **External** | Mikrodenetleyici dışında, harici entegre (IC) | Harici RTC, LCD driver, Ethernet PHY, SPI EEPROM |

📌 Internal çevre birimleri genellikle **MMIO** ile erişilen **register blokları**dır

📌 External birimler ise **SPI, I2C, UART gibi protokollerle** bağlanır

---

## 🧠 4️⃣ **Core Peripheral Nedir?** (ARM tarafından tanımlanmış)

- İşlemcinin çekirdeğiyle doğrudan ilişkili donanım birimleridir
- ARM tarafından her Cortex çekirdeğiyle birlikte standart olarak sağlanır

| Core Peripheral | Görev |
| --- | --- |
| **NVIC** | Interrupt kontrolü |
| **SysTick** | Sistem zamanlayıcısı (1 ms tick) |
| **SCB (System Control Block)** | Mod kontrol, reset, hata yönetimi |
| **FPU** (varsa) | Donanım float işlem birimi |
| **MPU** (varsa) | Bellek koruma birimi |

📌 Bunlar **CMSIS-Core** içinde `core_cmX.h` ile gelir ve **ARM tarafından sağlanır.**

---

## 🏢 5️⃣ **Vendor Peripheral Nedir?** (ST, NXP, TI gibi üreticiden gelenler)

- İşlemci çekirdeğini üretici firmalar entegre eder
- Her üretici kendi **donanım çevre birimlerini (internal peripheral)** tasarlar
- Bu donanımlar ARM’ın çekirdeğine bağlanır ama ARM tarafından değil, **üretici tarafından sağlanır**

| Vendor Peripheral (STM32 örneği) | Görev |
| --- | --- |
| GPIOA, GPIOB... | Dijital giriş/çıkış portları |
| USART1, SPI1, I2C1... | Seri iletişim |
| TIM1, TIM2... | Timer / PWM |
| ADC1, DAC, RTC | Analog çevre birimleri |
| DMA1, DMA2 | Bellek içi veri aktarımı |

📌 Bu birimlerin tanımı **CMSIS-Device** içinde gelir (`stm32f4xx.h` gibi)

---

## 🧩 Özet Karşılaştırma Tablosu

| Kategori | Tanım | Kim Sağlar? | Örnek |
| --- | --- | --- | --- |
| **Processor (CPU)** | Komut işleyen çekirdek | ARM | Cortex-M4 |
| **Core Peripheral** | CPU’ya gömülü standart bloklar | ARM (CMSIS-Core) | NVIC, SysTick, SCB |
| **Vendor Peripheral** | Üreticiye özel çevre donanımları | ST, NXP, TI, vs. | GPIO, UART, ADC |
| **Internal Peripheral** | MCU içinde fiziksel olarak yer alan her şey | ARM + Vendor | Core + Vendor peripherals |
| **External Peripheral** | MCU dışındaki harici entegre devreler | Harici donanım üreticileri | I2C ekran, SPI EEPROM |

---

## ✅ Sonuç:

- **Processor** sadece CPU’dur – komutları çalıştırır
- **Core peripherals** ARM’ın çekirdekle beraber verdiği kontrol birimleridir
- **Vendor peripherals** üreticinin MCU’ya eklediği donanımlardır (GPIO, UART, ADC vs.)
- **Internal**: MCU içinde entegre her şey
- **External**: Harici olarak bağladığımız I2C/SPI/UART üzerinden erişilen IC’lerdir

Bu yapıların ayrımı, hem **donanımın nasıl çalıştığını**, hem de **yazılımda hangi kaynaklardan erişileceğini** anlamada kritiktir.

---

### 🧠 `const` Olarak Tanımlanmış Array’ler = **Table (Tablo)**

ve **ROM/Flash** bellekte yer alır.

---

## 📌 1️⃣ `const` Array Neden "Table" Olarak Anılır?

- Programda sabit tutulan, değiştirilmeyen dizi/array’ler genellikle **lookup** amacıyla kullanılır
- Bu tür diziler bir **tablo (table)** gibi işlev görür:
    
    → Girdiye göre **önceden hesaplanmış** değeri döner
    

📌 Bu sebeple isimlendirme genellikle:

```c
const uint8_t sine_table[256];      // Sinüs değerleri
const char font_table[96][8];       // Font bitmap’leri
const uint16_t crc16_table[256];    // CRC hesaplaması için

```

---

## 🧠 2️⃣ Neden `const` Olarak Tanımlanır?

- Değişmeyeceği garanti edilir → **RAM’e kopyalanmasına gerek yoktur**
- Derleyici bunu **ROM/Flash belleğe** (genellikle `.rodata` segmentine) yerleştirir
- Kod çalışırken **Flash’tan direkt okunur**, RAM kullanılmaz

---

## 💾 3️⃣ `const` Array’ler Flash Bellekte Nasıl Yer Alır?

| Bellek Bölgesi | Açıklama |
| --- | --- |
| `.text` | Kodlar (fonksiyonlar) |
| `.rodata` | `const` tanımlı sabit tablolar (lookup, font vs.) |
| `.data` | RAM'e taşınacak global veriler |
| `.bss` | RAM'de sıfırlanacak global değişkenler |

> const array doğrudan .rodata içinde Flash'a yerleştirilir → yalnızca okunabilir
> 
> 
> RAM harcaması olmaz → **daha verimli ve güvenlidir**
> 

---

## 🔎 4️⃣ Gerçek Hayat Kullanımları

### ✅ **Lookup Table**:

```c
const uint8_t sin_table[256] = { /* 0–255 için sinüs değerleri */ };

uint8_t fast_sin(uint8_t angle) {
    return sin_table[angle];
}

```

→ Trigonometrik işlem yapılmadan hızlı sonuç

---

### ✅ **Font Table**:

```c
const uint8_t font_5x8[96][5] = {
  {0x00,0x00,0x00,0x00,0x00}, // boşluk
  {0x00,0x00,0x5F,0x00,0x00}, // !
  // ...
};

```

→ LCD ekranlara karakter basmak için

---

## 🧠 5️⃣ Avantajlar

| Özellik | Açıklama |
| --- | --- |
| 🔒 Sabitlik | Yanlışlıkla değiştirilmesi engellenir |
| 💾 Flash’ta saklanır | RAM yerine ROM kullanılır |
| ⚡ Hızlı erişim | Direkt adreslenebilir |
| 📚 Kodun anlamı netleşir | Lookup işlemi olduğu anlaşılır |

---

## ✅ Sonuç

- `const` array’ler sabit tablolardır → **lookup table**, **font table**, **CRC table** gibi
- Kodda **hız, sabitlik ve bellek verimliliği** sağlamak için bu şekilde tanımlanır
- Bu yapı sayesinde işlemci karmaşık hesaplar yerine **önceden hesaplanmış değerleri doğrudan kullanır**
    
    → özellikle **gömülü sistemler ve gerçek zamanlı uygulamalar** için idealdir.
    

### 🧠 **Memory'de Alias (Bellek Yansıması / Takma Ad) Kavramı – Sebebi ve Kullanımı**

---

## 📌 1️⃣ Alias Nedir?

**Alias**, bellek haritasında **aynı fiziksel bellek bölgesine birden fazla sanal adresle erişilebilmesi** durumudur.

→ Yani **tek bir fiziksel veri** için **birden fazla adres** vardır.

> "Aynı RAM, farklı adreslerde yansıtılıyor" gibi düşünebilirsin.
> 

---

## 🧠 2️⃣ Neden Alias Vardır? (Amaçlar ve Sebepler)

### ✅ A. **Donanımsal Erişim Yöntemleri için**

Bazı bellek bölgeleri, farklı **erişim şekilleri** ile kullanılabilsin diye aliaslanır.

**Örnek: STM32 Cortex-M3/M4 sistemlerinde bit-band aliasing:**

- Bit-band memory: RAM veya perifer alanındaki **tek bir biti doğrudan adresleyebilmek**
- Her bit’e özel bir alias adres oluşturulur:
    
    ```
    Alias Adres = 0x22000000 + (byte_offset × 32) + (bit_number × 4)
    
    ```
    

### ✅ B. **Erişim Tipine Göre Ayrım İçin**

- Aynı RAM bölgesi farklı amaçlarla (DMA, CPU, debug) erişilecekse
- Alias kullanılarak her erişim farklı erişim yolu gibi ayrılabilir

### ✅ C. **Cache Yönetimi için**

- Bazı sistemlerde (Cortex-A) **aynı belleğin cacheli ve cache'siz alias adresleri** vardır
- Örn: DMA → cache’siz alias adresiyle çalışmalı

### ✅ D. **Farklı Bus'larla Erişim için**

- Aynı bellek bölgesi, hem AHB hem I-TCM hem D-TCM gibi farklı veri yollarından erişilebilsin diye farklı adreslerde yansıtılır

---

## 🧪 3️⃣ Örnek – STM32F4 Bit-Band RAM Alias

| Adres | Açıklama |
| --- | --- |
| `0x20000000` | Normal SRAM |
| `0x22000000` | Bit-band alias adres başlangıcı |

```c
#define BITBAND_SRAM_REF    0x20000000
#define BITBAND_SRAM_ALIAS  0x22000000

#define BITBAND(addr, bit) ((BITBAND_SRAM_ALIAS + ((addr - BITBAND_SRAM_REF) * 32) + (bit * 4)))

*(volatile uint32_t*)BITBAND(0x20000000, 3) = 1;  // RAM'deki bir bit set edilir

```

---

## 🧩 4️⃣ Alias Kullanımının Avantajları

| Amaç | Açıklama |
| --- | --- |
| 🧱 **Bit erişimi** | Bireysel bit yazma/okuma için kolaylık sağlar |
| 🔄 **Donanım-arayüz uyumu** | CPU vs DMA vs Cache erişimleri ayrılabilir |
| 💬 **Dökümantasyon uyumu** | Harita üzerinde sembolik tanımlar kolaylaşır |
| 🧩 **Donanımsal soyutlama** | Farklı işlevsel bloklar aynı belleği kullanabilir |

---

## ⚠️ 5️⃣ Dikkat Edilmesi Gerekenler

- Alias edilen bellek adresleri üzerinden yapılan değişiklikler **diğer adreslerde de etkili olur** (aynı fiziksel veri)
- Bu nedenle alias erişimi **tutarlılık** açısından dikkatli kullanılmalıdır
- Debug yaparken adres çakışmalarına karşı dikkatli olunmalı

---

## ✅ Sonuç

**Memory alias**, aynı fiziksel belleğe farklı mantıksal adresler atayarak:

- Donanım-software erişimini kolaylaştırır
- Bit seviyesinde kontrol sağlar
- Cache / DMA / CPU erişimlerini ayrıştırır
- Mimari soyutlamayı sadeleştirir

Gömülü sistemlerde özellikle **bit-band**, **cache yönetimi** ve **özel erişim yolları** için bu teknik çok yaygındır.

### 🧠 **System Memory**, **İkincil Flash Memory**, **Bootloader Memory**

(MCU belleğinde yer alan özel bölgeler – anlamları, farkları ve kullanım amaçları)

---

## 📌 1️⃣ **System Memory (Sistem Belleği)**

- MCU içinde **önceden üretici tarafından yerleştirilmiş özel bir ROM bölgesidir**
- İçinde **donanımsal bootloader**, fabrika test kodları ve özel startup rutinleri bulunur

### 📍 Özellikler:

| Özellik | Açıklama |
| --- | --- |
| Kalıcılık | ✔ ROM (değiştirilemez, üretici yazmıştır) |
| Erişim Tipi | Sadece okunabilir (`Read-Only`) |
| Adres Aralığı | Genelde `0x1FFF0000` civarı (STM32 örneği) |
| İçerik | **Donanım bootloader** (USART, USB, CAN, I2C ile kod yükleme) |

### 📌 Kullanım:

- **Boot pinleriyle veya yazılımla** bu bölgeye geçilip, **harici program yüklemesi yapılabilir**
- Kod Flash belleğe yazılmadan önce MCU ilk burada çalışır

---

## 🗂️ 2️⃣ **Secondary Flash Memory (İkincil Flash / Flash Bank 2)**

- Bazı MCU’larda, **birden fazla Flash bloğu (bank)** bulunur
- Bu bloklar aynı kapasitedeki farklı bölgelerdir
    
    → Örn: **Main Flash (bank 1)** ve **Secondary Flash (bank 2)**
    

### 📍 Özellikler:

| Özellik | Açıklama |
| --- | --- |
| Kalıcılık | ✔ Flash (programlanabilir, silinebilir) |
| Erişim Tipi | Okuma/Yazma/Silme (CPU veya bootloader ile) |
| Kullanım Amacı | **Dual-bank firmware update**, backup, A/B boot |
| Adres Aralığı | Genelde `0x08100000` (STM32F7/H7 örneği) |

### 📌 Kullanım:

- **Firmware güncellemeleri** sırasında aktif kod bankası çalışırken diğer bankaya yeni kod yazılır
- Sistem güvenli biçimde "yeni sürüme" geçebilir

---

## 🚀 3️⃣ **Bootloader Memory**

- **Bootloader**, MCU’nun açıldığında çalıştırdığı **ilk küçük program**tır
- Görevi:
    - Sistemi başlatmak
    - Flash’ta geçerli kod varsa oraya yönlendirmek
    - İstenirse USB, USART, vs. üzerinden yeni yazılım almak

### 👇 Bootloader Türleri:

| Tür | Açıklama |
| --- | --- |
| **Donanımsal** | System Memory içinde kalıcı olarak bulunur (`ROM`) |
| **Kullanıcı bootloader** | Ana Flash’a kullanıcı tarafından yazılır (`User Flash`) |

> Kullanıcı bootloader yazıldığında, cihaz açılışta önce oraya yönlendirilir;
> 
> 
> sonra uygulamaya geçer → **çift kademeli açılış** sağlanabilir.
> 

---

## 🧭 Bellek Haritası Örneği (STM32F4 için)

| Bellek Alanı | Adres Aralığı | Açıklama |
| --- | --- | --- |
| **System Memory** | `0x1FFF0000` | Donanım bootloader (ROM) |
| **Main Flash** | `0x08000000` | Uygulama kodu (kullanıcı) |
| **Secondary Flash** | `0x08100000` (varsa) | İkincil firmware veya A/B bank |
| **SRAM** | `0x20000000` | Geçici değişkenler (RAM) |
| **Option Bytes / OTP** | Ayrı alanlar | Boot seçenekleri, koruma |

---

## 🧠 Özet

| Kavram | Tip | İçerik / Amaç | Silinebilir mi? |
| --- | --- | --- | --- |
| **System Memory** | ROM | Donanım bootloader, fabrika kodları | ❌ Hayır |
| **Secondary Flash** | Flash | Alternatif firmware alanı | ✔ Evet |
| **Bootloader Memory** | ROM veya Flash | Açılış yöneticisi (donanım/kullanıcı) | ROM kısmı ❌, User kısmı ✔ |

---

Bu yapılar sayesinde MCU'lar:

- Güvenli firmware güncelleyebilir
- Harici programlayıcı olmadan kod alabilir
- Açılışta yedek sistemle boot edebilir
- Donanım üreticisi tarafından korunan bir kurtarma yolu sunar

Özellikle gömülü sistem güvenliği, güncellenebilirlik ve recovery açısından **bu bölmeler çok kritik rol oynar.**

---

## 🧠 STM32’de `BOOT0` Pininin Görevi

**→ Resetten çıkıldığında, işlemcinin hangi bellek bölgesinden başlatılacağını belirler.**

---

## 🔧 Nasıl Çalışır?

STM32 işlemcileri açıldığında veya reset atıldığında, **PC (Program Counter)**'ın nereye yönlendirileceği, yani **ilk kodun nereden yükleneceği**, `BOOT0` pininin **seviyesine** göre belirlenir.

Bu seçim **donanım mantığı + boot configuration register (örneğin: `BOOT1` bitleri)** ile yapılır.

---

## 📦 Seçenekler (STM32F1/F4 için klasik senaryo):

| BOOT0 | Hedef Bellek (Boot memory) | Açıklama |
| --- | --- | --- |
| 0 | **Main Flash Memory** | Normal uygulama çalıştırılır (`0x08000000`) |
| 1 | **System Memory (ROM)** | Donanım bootloader başlatılır (`0x1FFFxxxx`) |

> 💡 Bazı serilerde BOOT1 pini de vardır ve bu seçenek sayısı 3’e çıkar (Flash, System, SRAM).
> 

---

## 🎯 BOOT0 Ne İçin Kullanılır?

### ✅ 1. **Kurtarma Modu (Firmware Update)**

- Eğer Flash bozulmuşsa veya boşsa, **BOOT0 = 1** yapılır
- Sistem açıldığında **System Memory içindeki donanımsal bootloader** devreye girer
- Bu bootloader, USART, USB, CAN, I2C üzerinden **harici program yüklemeye izin verir**

### ✅ 2. **Programlayıcı Olmadan Kod Yüklemek**

- BOOT0 = 1 yap → USB/USART bootloader aktif
- Kod yükle → Reset → BOOT0 = 0 → normal uygulama çalışır

---

## ⚙️ Teknik Akış

```
[ Reset veya Power-On ]
        ↓
[ BOOT0 seviyesi kontrol edilir ]
        ↓
BOOT0 = 1 → PC = System Memory (ROM Bootloader)
BOOT0 = 0 → PC = Flash Memory (Uygulama başı)

```

---

## ⚠️ Önemli Detay – **Alias Mekanizması**

- STM32 işlemcilerde `0x00000000` adresine **alias (yansıtma)** yapılır
- Bu alias **BOOT0 seçimine göre** farklı belleklere yönlenir:

| BOOT0 | `0x00000000` adresinde ne var? |
| --- | --- |
| 0 | Flash (main application) |
| 1 | System memory (ROM bootloader) |

> Bu sayede CPU reset sonrası doğrudan doğru başlangıç adresinden kodu alabilir.
> 

---

## 🛠️ Donanımda BOOT0 Nasıl Ayarlanır?

| Yöntem | Açıklama |
| --- | --- |
| BOOT0 pini doğrudan 3.3V veya GND'ye çekilir | Fiziksel sabitleme |
| BOOT0 pini pull-down, reset anında butonla VCC’ye çekilir | Geçici boot seçimi |
| Opsiyon byte üzerinden BOOT pin etkisiz hale getirilebilir | Bazı STM32 serilerinde |

---

## ✅ Özet

| Özellik | BOOT0 = 0 | BOOT0 = 1 |
| --- | --- | --- |
| Bellek Hedefi | Flash (`0x08000000`) | System Memory (`0x1FFFxxxx`) |
| Kullanım | Normal uygulama çalıştırma | Firmware yükleme, kurtarma modu |
| PC yönlendirme | Flash başlangıcına (alias) | ROM’daki donanım bootloader'a |

---

### 🎯 Amaç:

- Sistemin **açılışta hangi kaynaktan boot edeceğine karar vermek**
- Gerekirse **USB/USART üzerinden program yükleme arayüzüne ulaşmak**
- **Donanım güvenliği ve kurtarma kolaylığı** sağlamak

Bu yapı sayesinde, bir STM32 çipi neredeyse **her zaman yeniden programlanabilir**, yani "brick" olmaz. Gömülü sistemde bu büyük bir avantajdır.

---

## 🧠 **System Memory'den Başlatmak İstememizin Sebebi Nedir?**

**System memory**, STM32 gibi ARM tabanlı mikrodenetleyicilerde **üretici (örneğin STMicroelectronics) tarafından yerleştirilmiş, değiştirilemeyen, salt okunur (ROM)** bir bölgedir.

Ve içinde **hazır, donanımsal bir bootloader** bulunur.

---

## 🎯 Bu Bölgeden Başlatmak Neden Gerekebilir?

### ✅ 1. **İlk Kez Program Yüklemek**

- Mikrodenetleyici fabrikadan geldiğinde **Flash boştur**
- Uygulama kodu henüz yoktur → Flash’tan başlatılamaz
- System memory içindeki **hazır bootloader devreye girer**
- USB, USART, CAN veya I2C üzerinden **ilk kez kod yüklenir**

---

### ✅ 2. **Flash Silinirse veya Bozulursa**

- Flash içerdiği uygulama bozulduysa veya yanlışlıkla silindiyse
- Normal boot (BOOT0 = 0) işe yaramaz
- BOOT0 = 1 yapılır → system memory devreye girer
- Harici bağlantı üzerinden **kurtarma ve yeniden yükleme yapılabilir**

> Bu, cihazın "brick" olmamasını sağlar — her zaman bir kurtarma kapısı açık kalır.
> 

---

### ✅ 3. **Programlayıcı Olmadan Kod Yüklemek (UART/USB Boot)**

- STLink gibi programlayıcı olmadan, sadece **USB kablosu veya UART üzerinden** kod yüklemek istiyorsan:
    1. BOOT0 = 1 yap
    2. Reset at
    3. PC’de STM32CubeProgrammer ile bağlantı kur
    4. Kod yükle → Reset → BOOT0 = 0 yap

---

### ✅ 4. **Üretim / Seri Programlama Kolaylığı**

- Üretim hattında binlerce cihaz programlanacaksa:
    - Programlayıcı bağlamadan, **UART veya USB üzerinden otomatik yazılım yükleme** yapılabilir
    - System memory bootloader bu süreci yönetir

---

## 💡 Ekstra: System Bootloader Ne Yapar?

| Özellik | Açıklama |
| --- | --- |
| Protokol desteği | USART, USB DFU, CAN, I2C (cihaza göre) |
| Komut seti | Flash sil, Flash yaz, veri oku, CRC kontrol |
| Kullanım aracı | STM32CubeProgrammer, Flash Loader Demo |

---

## ✅ Özet

**System memory'den başlatma**, bir mikrodenetleyici sisteminde:

- Kod yüklemek için
- Flash silinmiş/bozulmuşsa kurtarmak için
- Programlayıcı olmadan yazılım güncellemek için
- Seri üretimde otomasyon kolaylığı sağlamak için

**donanımsal ve yazılımsal olarak kritik bir güvenlik ve erişim yoludur.**

Bu nedenle üretici bu ROM'u hiçbir zaman silinemez yapar → sistemin her koşulda yeniden programlanabilir kalmasını sağlar.

> Flash bellekte **silme işlemi tüm bitleri `1` yapar** (yani `0xFF`, `0xFFFF`, `0xFFFFFFFF` değerleriyle dolu olur).
> 

---

## 🧠 Peki **Neden Flash Silme = Tüm Bitleri 1 Yapmak?**

### 📌 Sebep: **Flash hücrelerinin fiziksel yapısı**

Flash bellek, temel olarak **Floating Gate MOSFET** hücrelerinden oluşur.

| Bit Durumu | Hücre Durumu | Gerilim (threshold) |
| --- | --- | --- |
| `0` | Elektronlar floating gate'e enjekte edilmiş | Yüksek (iletken değil) |
| `1` | Elektron yok, floating gate boş | Düşük (iletken) |

🔄 Yani:

- **Elektron varsa → 0**
- **Elektron yoksa → 1**

---

## 🔄 Yazma ve Silme Nasıl Yapılır?

| İşlem | Ne Yapılır? | Bit Değeri Sonucu |
| --- | --- | --- |
| **Yazma** | Elektron enjekte edilir (bit `1 → 0`) | `1` → `0` yapılabilir |
| **Silme** | Elektronlar boşaltılır (tüm bitler `1` olur) | Sadece `0` → `1` yapılabilir |
| **Kısıtlama** | `0 → 1` doğrudan **yazılamaz**, sadece **silerek** yapılabilir | ❌ Direkt mümkün değil |

---

## 🔧 Peki Yazılım `0 → 1` Yapamıyorsa Nasıl Oluyor?

### ✅ 1. **Önce SİLME** yapılır → tüm bitler `1` olur

### ✅ Sonra **YAZMA** yapılır → istenen bitler `1 → 0` düşürülür

### Örnek:

```c
// Flash'ı programlamak için tipik adımlar
HAL_FLASH_Unlock();                   // Flash yazma izni açılır
FLASH_Erase_Sector(...);             // Silme işlemi yapılır → tüm bitler 1
HAL_FLASH_Program(TYPE, addr, data); // İstenilen veri yazılır → 1 → 0
HAL_FLASH_Lock();                    // Flash tekrar kilitlenir

```

---

## 🧪 Teknik Detay: Silme Nasıl Yapılıyor?

Flash içinde **yüksek voltaj (örneğin 10–20V civarı)** uygulayan bir **charge pump devresi** vardır.

- **Silme işlemi sırasında** tüm hücrelere bu voltaj uygulanır
- Bu işlem birkaç milisaniye sürebilir (örneğin 25ms – 100ms)
- Silme **sayfa (page)** ya da **sektör (sector)** düzeyinde yapılır

📌 **Flash’tan tek bir bit silinemez.** En küçük silme birimi → **bütün blok/sektör**.

---

## ⚠️ Neden `0 → 1` Doğrudan Yazılamaz?

Çünkü bu işlem:

- **Yalnızca elektron çıkışı gerektirir** (yüksek enerji gerektirir)
- Her hücrede **yüksek voltaj devresi** bulunması donanımda **çok maliyetli olurdu**
- Flash tasarımı bu yüzden sadece:
    - **1 → 0 yazma**
    - **Tüm bitleri 1 yaparak silme** şeklinde yapılır

---

## ✅ Özet

| Soru | Cevap |
| --- | --- |
| **Neden tüm bitler 1 olur?** | Elektronlar boşaltılır, floating gate temizlenir |
| **0 → 1 doğrudan yazılabilir mi?** | ❌ Hayır, önce tüm bölge silinmeli |
| **Silme işlemi nasıl yapılır?** | Yüksek voltajla tüm hücrelerdeki elektronlar boşaltılır |
| **Yazma nasıl çalışır?** | 1 olan bitler seçilerek 0’a indirilir |
| **Yazılım bunu nasıl yapar?** | Silme + programlama kombinasyonu ile |

Bu donanımsal sınırlamalar nedeniyle Flash ile çalışırken yazılım daima şu sıralamayı takip eder:

**Sil → Yaz (yalnızca 1→0 olacak şekilde).** Bu durum, firmware update, EEPROM emülasyonu gibi işlemleri tasarlarken çok dikkat edilmesi gereken bir kısıttır.

### 🧠 **Stack Pointer (SP)** ve SP'ye Yakın Görevdeki **Link Register (LR)**

**Başlangıç değerleri ve işlevsel farklarıyla birlikte**

---

## 🟢 1️⃣ **Stack Pointer (SP)**

### 📌 Tanım:

- Stack Pointer, **yığın (stack)** belleğinin **en üst adresini gösteren** register’dır.
- Cortex-M mimarilerinde **`R13`** register'ı ile tanımlıdır.
- SP, **otomatik fonksiyon çağrıları, yerel değişkenler ve kesmelerde** kullanılır.

---

### 🔧 Başlangıç Değeri:

- SP’nin başlangıç değeri, `vector table` (0x00000000 veya Flash başlangıcı) adresindeki **ilk 4 byte** ile belirlenir:

```
@ Örnek Vector Table başı (linker script tarafından üretilir):
0x08000000: 0x20020000   ← SP başlangıç değeri (RAM üst ucu)
0x08000004: 0x08000125   ← Reset Handler (PC başlangıcı)

```

### 📍 Neden RAM’in üstü?

- Stack **aşağıya doğru (yüksek adresten → düşük adrese)** büyür
- Bu yüzden stack pointer genellikle **SRAM’in sonuna** (üst adresine) ayarlanır

---

## 🟡 2️⃣ **Link Register (LR)**

### 📌 Tanım:

- Link Register, **fonksiyon dönüş adresini** tutar
- Fonksiyon çağrıldığında (BL), PC yeni adrese giderken **LR'ye dönüş adresi yazılır**
- Cortex-M'de **`R14`** register’ıdır

---

### 🔁 Kullanımı:

```
BL my_function    ; LR = return address
BX LR             ; Dönüş

```

### 💡 Kesmelerde de kullanılır:

- Cortex-M çekirdeği kesme sırasında `LR`'yi özel bir değerle (`EXC_RETURN`) doldurur
- Kesmeye geri dönüşte bu değer analiz edilerek doğru moda dönüş sağlanır

---

## 🧩 3️⃣ SP – LR Karşılaştırmalı Tablo

| Özellik | Stack Pointer (SP) | Link Register (LR) |
| --- | --- | --- |
| Register adı | R13 | R14 |
| Görev | Stack’in en üst adresini tutar | Fonksiyon dönüş adresini tutar |
| Kesmelerde rolü | Stack yapısını taşır | EXC_RETURN değeri alır |
| Otomatik mi? | ✔️ (Push/Pop işlemleriyle) | ✔️ (BL komutu ile) |
| Başlangıç değeri | RAM’in üst adresinden gelir (`vector table[0]`) | Kod çalışınca BL ile atanır |

---

## 📚 Özet

| Register | Adı | Görev | Başlangıç Nereden Gelir? |
| --- | --- | --- | --- |
| R13 | Stack Pointer (SP) | Stack'in en üstünü gösterir | Vector table’ın ilk girdisi |
| R14 | Link Register (LR) | Fonksiyon dönüş adresini tutar | `BL` komutuyla otomatik atanır |

Bu iki register, özellikle **fonksiyon çağrıları**, **kesmeler** ve **gömülü sistem başlangıç sekansı** için çok kritik rol oynar.

SP’nin düzgün ayarlanmaması → **çökme (stack overflow, undefined behavior)**

LR’nin yanlış kullanımı → **yanlış yere dönüş, sonsuz döngü, çakılma** gibi sorunlara neden olabilir.

---

## 🧠 1️⃣ ARM State vs Thumb State – Nedir?

| Durum | Açıklama |
| --- | --- |
| **ARM state** | 32-bit uzunluğunda tam ARM komutları yürütülür |
| **Thumb state** | 16-bit (veya Thumb-2'de 16/32-bit karışık) komutlar |

### ➕ Neden iki ayrı durum var?

- **ARM state**: daha güçlü, daha fazla register erişimi, performans odaklı
- **Thumb state**: daha küçük komutlar → daha az bellek kullanımı, düşük bant genişliği

---

## 🧭 2️⃣ Geçiş Nasıl Yapılır?

Program counter (PC) yani adres değeri üzerinden belirlenir:

| PC'nin LSB (Bit 0) | Anlam |
| --- | --- |
| `0` | **ARM state** |
| `1` | **Thumb state** |

Yani:

`BX R0` gibi bir komutta → `R0` = `0x08000001` → `Thumb state`

`R0` = `0x08000000` → `ARM state`

📌 Ancak bu, **aslında PC’nin LSB'sinin doğrudan bir adres biti olarak kullanılmaması** anlamına gelir.

Bu bit sadece **state selector** görevi görür, çünkü kod adresleri daima **aligned** (4 veya 2 byte hizalı) olmalıdır.

---

## 📦 3️⃣ Peki Bu Bit’i Neden Biz Değiştiremiyoruz?

Çünkü:

1. Bu bit **doğrudan CPU tarafından kontrol edilir**, kullanıcı doğrudan bu biti maskeyle set/clear yapamaz.
2. Sen `BL`, `BX`, `LDR PC, [...]` gibi komutlarla bir adrese atlama yaptığında, **adresin en düşük biti** otomatik kontrol edilir.
3. Derleyici ve linker bu adresleri zaten Thumb/ARM kod bloklarına göre düzenler (örneğin `.thumb` ya da `.arm` sekmeleri).

### Yani:

Bu bir **varsayım değil**, **bilinçli donanım mimarisi**:

→ Thumb state’e gitmek istiyorsan **hedef adresin LSB’si 1 olmalı**

→ ARM state’e gitmek istiyorsan **LSB = 0** olacak

---

## 🔧 4️⃣ Kullanımda Örnek

```
LDR R0, =0x08000201   ; Thumb kod bloğuna atla (LSB = 1)
BX R0                 ; Thumb state'e geç

```

```
LDR R0, =0x08000000   ; ARM kod bloğu
BX R0                 ; ARM state'e geç

```

---

## 🔐 5️⃣ Thumb’ın Zorunlu Hale Getirilmesi

Bazı sistemlerde (örneğin **Cortex-M serileri**):

- **Sadece Thumb state desteklenir**
- ARM state **kaldırılmıştır**
- Bu durumda: tüm atlamalar, PC değerleri, exception vektörleri, **daima LSB = 1 olmalıdır**
- CPU, ARM state’e geçmeye çalışılırsa **HardFault** verir

> 📌 Cortex-M0/M3/M4/M7/M33 gibi çekirdekler: sadece Thumb-2 çalıştırır, ARM yoktur.
> 

---

## ✅ Özet

| Özellik | Açıklama |
| --- | --- |
| ARM vs Thumb state | Komut uzunluğuna göre farklı işlem modları |
| PC'nin LSB'si | State seçicidir: 0 = ARM, 1 = Thumb |
| Değiştirilememe sebebi | LSB sadece **donanım tarafından yorumlanır** |
| Thumb zorunlu mu? | Cortex-M serilerinde **evet, sadece Thumb** |
| Derleyici rolü | Kod adreslerini doğru hizalar (LSB ayarı yapar) |

---

## 🧠 **Reset Vector**, `SP`'nin İnit Değeri ve Kesme Vektör Tablosu

(Tamamı genellikle **ilk 1 KB'lik bellek alanında** tutulur ve sistemin **en hayati noktasıdır**)

---

## 📌 1️⃣ Vector Table (Kesme Vektör Tablosu)

- ARM Cortex-M mimarilerinde, **adres 0x0000_0000'dan başlayan** bu tablo,
    
    işlemcinin **açılışta ve her kesmede hangi adrese atlayacağını** belirler.
    

### 📍 Tipik Başlangıç (ilk 8 byte):

| Adres | İçerik | Anlamı |
| --- | --- | --- |
| `0x00000000` | İlk Stack Pointer değeri | `SP` başlangıcı (R13) |
| `0x00000004` | Reset Handler adresi | `PC` başlangıcı |

Bu ilk iki kayıt, işlemci açıldığında:

```
SP = *(0x00000000)
PC = *(0x00000004)

```

şeklinde **otomatik olarak yüklenir**.

---

## 📍 Devamında Gelenler – **Exception ve Interrupt Vektörleri**:

| Adres | Hangi Handler? |
| --- | --- |
| `0x00000008` | NMI Handler |
| `0x0000000C` | HardFault Handler |
| `...` | ... |
| `0x0000003C` | SysTick Handler |
| `0x00000040` → | Vendor-defined IRQ0 |
| `...` | Diğer perif. IRQ adresleri |

Bu alanlar, **işlemcinin iç kesmeleri (core exceptions)** ve ardından gelen **vendor tanımlı dış kesmeleri (external interrupts)** için handler adreslerini içerir.

---

## 🧠 2️⃣ Stack Pointer’ın İlk Değeri – Neden İlk Satırda?

- Cortex-M işlemciler **stack tabanlı mimaridir**
- İlk SP değeri genellikle **RAM’in en üst adresidir**
- Resetten sonra:
    - `SP ← *(0x00000000)`
    - `PC ← *(0x00000004)`
- SP olmadan fonksiyon çağrısı, exception handling, local değişken tutulamaz → sistem başlatılamaz

---

## 🔐 3️⃣ Bu Alan Neden Kritik?

| Sebep | Açıklama |
| --- | --- |
| 🧭 Başlangıç yönlendirmesi | Sistem açıldığında **buradan yönlenir** |
| 🛡️ Güvenlik | Hatalı bir `SP` veya `PC` → **HardFault** |
| 🔁 Kesme yönetimi | Tüm ISR’ler bu tablodan adreslenir |
| 🧠 Önceden tanımlı, sabit yapı | Cortex-M mimarisi bunu **zorunlu kılar** |

---

## 💾 4️⃣ Boyut ve Yerleşim

- Tipik Vector Table:
    - 16 core exception + N tane external interrupt
    - Her biri 4 byte → örneğin 100 IRQ için ~464 byte
- Bu yüzden genellikle:
    - **Toplam 1 KB’lik alan** linker script’te `VECTOR_TABLE` olarak ayrılır
    - `.isr_vector` veya `.vectors` olarak adlandırılır

---

## 📦 5️⃣ STM32 Örneği – İlk Vektörler

```c
__attribute__((section(".isr_vector")))
const uint32_t vector_table[] = {
    0x20020000,            // Initial SP value
    (uint32_t) Reset_Handler,
    (uint32_t) NMI_Handler,
    (uint32_t) HardFault_Handler,
    ...
    (uint32_t) USART1_IRQHandler,
    (uint32_t) TIM2_IRQHandler,
    ...
};

```

---

## ✅ Özet

| Bileşen | Görevi | Konumu |
| --- | --- | --- |
| `SP init` | Stack başlatma, sistemin çalışabilmesi | `0x00000000` |
| `Reset Handler` | Kodun başlangıç noktası | `0x00000004` |
| `Vector Table` | Tüm kesmelerin yönlendirme tablosu | İlk 1 KB (genelde) |
| `Vendor IRQ` | Üreticiye özel çevre birimi kesmeleri | 0x00000040 ve sonrası |

Bu alanların **tek bir hata ile sistemin tamamen kilitlenmesine** neden olabileceği için,

gömülü sistemlerde bu **1 KB’lik başlangıç alanı**, kelimenin tam anlamıyla **hayati çekirdektir**.