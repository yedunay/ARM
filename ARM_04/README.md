### 🧠 `TEMP<6:4>` ve `TEMP<7:0>` – Bit Seviye Alt Küme Kavramı (Bitfield)

---

### 📌 Genel Mantık: `REGISTER<BİT:ALT_BIT>` Gösterimi

Bu gösterim, bir **veri kaydı (register)** ya da değişken içindeki **belirli bit aralığını** ifade eder.

Donanım ve mikrodenetleyici belgelerinde çok yaygın bir gösterimdir.

---

## 1️⃣ `TEMP<7:0>` Nedir?

- `TEMP` adında bir **8-bit’lik veri alanı** olduğunu gösterir.
- `<7:0>` demek: **Bit 7’den Bit 0’a kadar olan tüm bitleri kapsar** (toplam 8 bit)

Örnek:

```
TEMP = 0b10110101
        ↑       ↑
       Bit7    Bit0
```

- Kullanım: `TEMP<7:0>` = tüm veri byte’ı
- Genellikle bir **tam register**ı ifade eder (ör: bir sensör ölçüm değeri, kontrol bayrağı)

---

## 2️⃣ `TEMP<6:4>` Nedir?

- `TEMP` değişkeninin **sadece 6. ile 4. bitleri arasındaki alt kümesidir**
- Yani toplamda 3 bit: `TEMP[6]`, `TEMP[5]`, `TEMP[4]`
- Bu 3 bit genellikle bir **alt alan (bitfield)** olarak **özel anlam taşır**
    - Örneğin bir kontrol register’ında:
        - `TEMP<6:4>` = mod seçimi
        - `TEMP<3:2>` = hız ayarı
        - `TEMP<1:0>` = enable / disable

---

### 🎯 Örnek: Bitfield Kullanımı

```c
uint8_t TEMP = 0b10111001;

// TEMP<6:4> nasıl alınır?
uint8_t field = (TEMP >> 4) & 0b00000111;  // ⇒ 0b101 (decimal 5)

// TEMP<7:0> zaten direkt TEMP değişkeninin kendisidir

```

---

### ⚙️ Donanım Tarafında Ne İçin Kullanılır?

| Bit Aralığı | Kullanım |
| --- | --- |
| `TEMP<7:0>` | Tam register verisi |
| `TEMP<6:4>` | Mod, tip, seviye gibi kontrol alanı |
| `TEMP<3:0>` | Sayıcı değeri veya küçük kontrol bitleri |
| Tek bit (`TEMP<2>`) | Bayrak (flag) veya enable/disable işlevi |

---

### 📦 Neden Bit Alt Kümesi Kullanılır?

- **Bellekten tasarruf:** 8-bit içinde birden fazla parametre saklanır
- **Hızlı erişim:** Mask & shift ile hızlı kontrol
- **Donanımda standardizasyon:** Her register alanı görevine göre parçalara ayrılır

---

Özetle, `TEMP<7:0>` tüm byte'ı, `TEMP<6:4>` ise onun içinden 3-bit’lik alt bir alanı temsil eder. Bu yapı donanım register’larında işlevleri küçük parçalara bölmek için kullanılır.

### 🧠 Register Nedir? Sadece CPU Register mı? Flash, Program Memory ve SRAM ile Farkları

---

## 1️⃣ **Register – Genel Tanım**

**Register**, işlemcinin içinde bulunan **çok küçük, çok hızlı bellek hücreleridir.**

Sadece CPU içindedir ve doğrudan ALU (Arithmetic Logic Unit) ile çalışır.

| Özellik | Açıklama |
| --- | --- |
| Fiziksel Konum | Yalnızca CPU içinde |
| Erişim Süresi | En hızlı erişim (1 clock cycle) |
| Kapasite | Çok küçük (genellikle 8–32 adet, 8–32 bit arası) |
| Kullanım | Hesaplama, adresleme, geçici veri taşıma |
| Programcı Erişimi | Assembly düzeyinde doğrudan erişilir |

---

### ✅ Örnek CPU Register'ları (ARM Cortex-M):

| Register | Açıklama |
| --- | --- |
| R0–R12 | Genel amaçlı veri register'ları |
| SP | Stack Pointer (yığın işaretçisi) |
| LR | Link Register (dönüş adresi) |
| PC | Program Counter (komut adresi) |
| APSR | Flag register (Z, N, C, V bitleri) |

---

## 2️⃣ ❌ **Flash / SRAM Register Değildir**

Bunlar register değil, **bellek bölgeleridir.**

---

### 🔸 Flash (Program Memory)

| Özellik | Açıklama |
| --- | --- |
| Kalıcı mı? | ✔ Evet |
| Hız | CPU register’a göre çok yavaş |
| Kullanım | Program kodu ve `const` veri saklanır |
| Erişim Tipi | Genelde yalnızca okunur |
- Kodlar burada tutulur (`main()`, fonksiyonlar, ISR vs.)
- `const int x = 5;` → Flash’ta saklanır

---

### 🔸 SRAM (Static RAM)

| Özellik | Açıklama |
| --- | --- |
| Kalıcı mı? | ❌ Hayır (güç kesilince silinir) |
| Hız | Register’a göre yavaş, Flash’a göre hızlı |
| Kullanım | Geçici veri (değişkenler, yığın, heap) |
| Erişim Tipi | Okuma ve yazma yapılabilir |
- `int x = 3;` gibi değişkenler SRAM’dedir
- Stack ve Heap bölgeleri de buradadır

---

## 3️⃣ 📦 Bellek Haritasında Konumlar

| Bellek Tipi | Tipik Adres Aralığı (STM32F4 örneği) |
| --- | --- |
| Flash (ROM) | 0x0800_0000 – 0x080F_FFFF |
| SRAM | 0x2000_0000 – 0x2001_FFFF |
| Peripherals | 0x4000_0000 – 0x5000_0000 |
| CPU Register’ları | CPU içinde, özel erişim |

---

## 4️⃣ 🧮 Özet: Register vs. Bellek Karşılaştırması

| Özellik | CPU Register | SRAM (RAM) | Flash (Program Memory) |
| --- | --- | --- | --- |
| Fiziksel Yer | CPU içinde | CPU dışı | CPU dışı |
| Hız | En hızlı | Orta | Yavaş |
| Kalıcılık | Geçici | Geçici | Kalıcı |
| Boyut | Küçük (baytlar) | Orta (KB) | Büyük (100KB–MB) |
| Kullanım | Anlık işlem | Değişkenler | Kod/sabit veri |

---

**Sonuç:**

Register, sadece CPU içinde bulunan en hızlı geçici depolama birimidir.

Flash ve SRAM ise CPU dışında kalan, daha büyük ama daha yavaş **bellek alanlarıdır.**

Hepsi birlikte işlemcinin **hafıza mimarisini** oluşturur, ancak sadece **CPU register’ları gerçek "register"dır.** Kesinlikle doğru bir yaklaşım. Bu düşünce, **taşınabilir, okunabilir ve bakımı kolay yazılım** geliştirmenin temel ilkelerinden biridir.

---

### 🧠 **Sabit Adres Yerine Sembolik İsim Kullanmak Neden Önemlidir?**

### 🎯 1. **Taşınabilirlik (Portability)**

- Doğrudan adres kullanırsan:
    
    ```c
    *((uint32_t*)0x40021018) = 0x01;  // GPIOA_MODER
    
    ```
    
    Bu sadece **tek bir mikrodenetleyici için geçerlidir** (örneğin STM32F1).
    
- Sembolik isim kullanırsan:
    
    ```c
    GPIOA->MODER = 0x01;
    
    ```
    
    Aynı kod farklı STM32 serileriyle uyumlu hâle gelir. Çünkü `GPIOA` tanımı **başka başlık dosyaları aracılığıyla güncellenebilir**.
    

---

### 👓 2. **Okunabilirlik**

- `0x40021018` gibi adresler anlam taşımaz
- `GPIOA->MODER` ise kodun **ne yaptığını doğrudan anlatır**

---

### 🧰 3. **Bakım ve Güncellenebilirlik**

- Donanım tasarımı veya mikrodenetleyici değişirse, sadece sembol tanımını (örneğin bir `#define` ya da `typedef`) güncellemek yeterlidir.
- Adres sabit kullanılmış olsaydı, **bütün kodu satır satır düzeltmek gerekirdi**.

---

### 🛠️ Uygulamada Nasıl Yapılır?

### 📄 Başlık Dosyası (CMSIS veya Üretici Kütüphaneleri)

```c
#define GPIOA_BASE (0x40020000UL)
#define GPIOA ((GPIO_TypeDef *) GPIOA_BASE)

typedef struct {
    volatile uint32_t MODER;
    volatile uint32_t OTYPER;
    volatile uint32_t OSPEEDR;
    // ...
} GPIO_TypeDef;

```

Kullanım:

```c
GPIOA->MODER |= (1 << 10);

```

---

### 📌 Sonuç

| Yaklaşım | Taşınabilirlik | Okunabilirlik | Güvenlik | Bakım |
| --- | --- | --- | --- | --- |
| **Sabit adres** | ❌ Düşük | ❌ Düşük | ❌ Hatalara açık | ❌ Zor |
| **Sembolik isim** | ✔️ Yüksek | ✔️ Yüksek | ✔️ Güvenli | ✔️ Kolay |

---

Donanım seviyesinde çalışırken bile, **yazılım mühendisliği ilkelerine sadık kalmak**, projeni hem bugüne hem geleceğe taşır. Bu nedenle sabit adresler yerine **sembolik tanımlamaların kullanılması bir "iyi uygulama standardı"dır.**

### 📚 CMSIS – ARM’ın Donanım Soyutlama ve Standartlaşma Yapısı

**(Cortex Microcontroller Software Interface Standard)**

---

## 🧠 1️⃣ CMSIS Nedir?

**CMSIS**, ARM tarafından geliştirilen ve tüm ARM Cortex tabanlı mikrodenetleyicilerde **ortak bir yazılım arayüzü ve isimlendirme standardı sağlayan** bir yazılım çerçevesidir.

### 📌 Amaçları:

- Kod taşınabilirliğini sağlamak
- Donanım soyutlaması yapmak
- Derleyici/araç bağımsız bir çekirdek tanımı oluşturmak
- Geliştiricilere düşük seviye erişimde **standartlaştırılmış semboller** sunmak

---

## 🧩 2️⃣ CMSIS'in Yapısı ve Bileşenleri

| Bileşen Adı | Açıklama |
| --- | --- |
| **CMSIS-Core** | CPU çekirdeği ve sistem register’ları tanımı (`SCB`, `NVIC`, `SysTick`) |
| **CMSIS-Device** | Mikrodenetleyiciye özel çevre birimlerinin adresleri ve yapılandırmaları |
| **CMSIS-DSP** | Sabit ve kayan nokta DSP kütüphanesi |
| **CMSIS-RTOS** | Gerçek zamanlı işletim sistemi arayüz standardı |
| **CMSIS-Pack** | IDE'ler için paket yönetim sistemi |

---

## 📁 3️⃣ CMSIS-Device ile Donanım Tanımlamaları

**CMSIS-Device**, üretici firma tarafından sağlanan ve CMSIS standardına uygun olan `*.h` başlık dosyalarını içerir.

### Örnek: STM32F4 için `stm32f4xx.h`, `core_cm4.h`

```c
#define GPIOA_BASE     (AHB1PERIPH_BASE + 0x0000UL)
#define GPIOA          ((GPIO_TypeDef *) GPIOA_BASE)

typedef struct {
  __IO uint32_t MODER;   /*!< GPIO port mode register */
  __IO uint32_t OTYPER;
  __IO uint32_t OSPEEDR;
  // ...
} GPIO_TypeDef;

```

Bu tanımlar CMSIS'e uygun şekilde yapılmıştır ve tüm ARM çekirdekli sistemlerde benzer yapılar korunur.

---

## 🏗️ 4️⃣ ARM’ın İşlemci Gruplamaları ve CMSIS Bağlantısı

| ARM Grubu | Açıklama | CMSIS'deki Bağlantı |
| --- | --- | --- |
| **Cortex-M** | Mikrodenetleyici sınıfı (low power) | CMSIS-CoreM (`core_cm3.h`, `core_cm4.h`) |
| **Cortex-R** | Gerçek zamanlı sistemler için | CMSIS-CoreR |
| **Cortex-A** | Uygulama sınıfı (işletim sistemli) | Genelde CMSIS kullanılmaz, başka OS/driver setleri vardır |

> CMSIS özellikle Cortex-M serisi için tasarlanmıştır.
> 
> 
> `core_cmX.h` dosyaları çekirdek register’larını ve sistem kontrol yapılarını tanımlar.
> 

---

## 🧠 5️⃣ Neden CMSIS Önemli?

| Faydası | Açıklama |
| --- | --- |
| **Sembolik Tanımlar** | `GPIOA->MODER` gibi okunabilir ve taşınabilir isimler sağlar |
| **Donanım Soyutlama** | Kodu adres yerine isimlerle yazarak donanım bağımlılığı azaltılır |
| **Araç Uyumluluğu** | Keil, IAR, GCC gibi tüm IDE’lerde ortak yapı kullanılır |
| **Güncellenebilirlik** | Üretici bir chip’e destek eklediğinde sadece `*.h` dosyası değişir |

---

## 📌 Özet

- CMSIS = ARM’ın mikrodenetleyici dünyası için geliştirdiği standart soyutlama katmanıdır
- `GPIOA->MODER` gibi tanımların **sembolik ve taşınabilir** olmasını sağlar
- ARM, çekirdekleri tasarlar → CMSIS standardını sağlar → **ST gibi firmalar** kendi çipleri için bu standarda uygun tanım dosyaları yazar
- Bu yapı sayesinde **farklı üreticilerin farklı çipleriyle çalışmak**, minimum kod değişikliğiyle mümkündür

CMSIS, gömülü sistemlerde **temiz, taşınabilir ve sürdürülebilir** düşük seviye yazılım yazmanın temel yapı taşıdır.

### 📍 **Address Offset (Adres Ofseti) Mantığı – Gömülü Sistemlerde Temel Kavram**

---

### 🔧 1️⃣ Tanım: **Offset Nedir?**

**Offset**, bir bellek bloğu içinde belirli bir elemanın **başlangıç adresine olan uzaklığıdır**.

Yani **bir temel (base) adrese göre fark** anlamına gelir.

> 📌 Formül:
> 
> 
> `Gerçek Adres = Base Adres + Offset`
> 

---

### 🧠 2️⃣ Neden Kullanılır?

- **Bellek blokları içinde parça-parça erişim** sağlar
- Donanım register’larının veya yapıların içindeki alanlara erişimi kolaylaştırır
- Kodun **taşınabilir** ve **bakımı kolay** olmasını sağlar

---

### 📦 3️⃣ Donanımda Offset Kullanımı (Perifer Register’ları)

**Örnek: STM32 – GPIOA Register Bloğu**

| Register | Offset | Açık Adres (base = 0x40020000) |
| --- | --- | --- |
| MODER | 0x00 | 0x40020000 |
| OTYPER | 0x04 | 0x40020004 |
| OSPEEDR | 0x08 | 0x40020008 |
| PUPDR | 0x0C | 0x4002000C |

Bu yapıda:

```c
#define GPIOA_BASE (0x40020000UL)
#define GPIOA ((GPIO_TypeDef *) GPIOA_BASE)

typedef struct {
    __IO uint32_t MODER;     // offset 0x00
    __IO uint32_t OTYPER;    // offset 0x04
    __IO uint32_t OSPEEDR;   // offset 0x08
    __IO uint32_t PUPDR;     // offset 0x0C
} GPIO_TypeDef;

```

Kullanım:

```c
GPIOA->PUPDR = 0x00000001; // 0x4002000C adresine yazılır

```

> PUPDR, GPIOA_BASE adresinden +0x0C (12 byte) ötede → bu offset’tir.
> 

---

### 🧰 4️⃣ Offset ile Yapı (struct) Üzerinden Erişim

Bellek üstüne kurulu her yapı, **offset mantığıyla erişilebilir**:

```c
typedef struct {
    uint8_t header;    // offset 0
    uint16_t length;   // offset 1 (çakışma varsa padding olabilir!)
    uint8_t data[4];   // offset 3
} Packet_t;

```

```c
Packet_t *p = (Packet_t *) 0x20000000;
p->data[2];  // Gerçek adres = 0x20000000 + offset 5

```

---

### ⚙️ 5️⃣ Avantajları

| Özellik | Açıklama |
| --- | --- |
| **Modülerlik** | Her bloğun offset’i bellidir, adres değişirse sorun olmaz |
| **Soyutlama** | Base adres bir kere tanımlanır, her yer yeniden yazılmaz |
| **Perifer Erişimi** | Donanım register’larının tümü offset mantığıyla adreslenir |

---

### 🎯 Gerçek Hayat Analojisi

Bir bina düşün:

- Kat 1’in adresi = temel adres (örneğin zemin)
- Kat 3 daire 2 = temel + 3 kat yukarı + daire ofseti
- Her dairenin yeri sabittir ama binanın yeri değişirse, tüm offset yapısı korunur

---

### 🧩 Özet

- **Offset**, base adrese göre bir elemanın hafızadaki yerini belirler
- Gömülü sistemlerde tüm **perifer register yapıları** bu mantıkla kurulur
- Kodda `#define` veya `struct` ile offset tanımlamak, sistemi **okunabilir, taşınabilir ve hatasız** hâle getirir

Bu mantık, gömülü programlamada donanım düzeyine güvenli ve kontrollü erişimin temel taşıdır.

### 🧠 **FPU Nedir?** (Floating Point Unit – Ondalıklı Sayı İşlemcisi)

Ve **neden gerekir, nasıl çalışır, nasıl kullanılır?**

---

## 🔧 1️⃣ FPU Nedir?

**FPU (Floating Point Unit)**, işlemcinin içinde yer alan, **reel sayılar (ondalıklı – float, double)** ile işlemleri **donanım düzeyinde** ve **çok daha hızlı** yapabilen özel bir birimdir.

---

## ❓ 2️⃣ Yazılımda Reel Sayılar FPU Olmadan Kullanılamaz mı?

> ✅ Kullanılabilir.
> 
> 
> Ama: **Yavaş, büyük ve enerji tüketimi yüksek** olur.
> 

**FPU olmayan bir işlemci**, float işlemleri yaptığında:

- C derleyicisi bu işlemleri **tamsayı işlem birimlerine** dönüştürerek **yazılım tabanlı** olarak simüle eder
- Yani `3.14 * 2.0` gibi bir işlem onlarca makine komutuna çevrilir

📌 **Sonuç:** float çalışır ama **yüzlerce cycle sürer**, **kodu şişirir**, **verimsizdir**

---

## ⚙️ 3️⃣ FPU ile Ne Değişir?

- FPU donanımı, **IEEE-754 standardına göre** float/double işlemleri donanımda yürütür
- `ADD`, `MUL`, `DIV`, `SQRT` gibi işlemleri **tek bir komutla** gerçekleştirir
- Kod küçülür, hız katlanır (örn: 10 kat)

---

## 🔍 4️⃣ FPU Nasıl Çalışır? Donanımsal Seviyesi

### Donanımsal bileşenleri:

| Bileşen | Görev |
| --- | --- |
| **Exponent Unit** | Üssü normalize eder, kaydırmaları yapar |
| **Mantissa Unit** | Ondalık kısmın asıl işlemlerini yapar |
| **Rounding Logic** | IEEE-754’e uygun yuvarlama yapar |
| **Exception Logic** | Taşma, belirsizlik, sıfıra bölme kontrolü |
| **Control FSM** | Tüm akışı yöneten kontrol devresi |

### FPU işlemi örneği:

`float x = a * b + c;`

Aşağıdaki adımları donanımda yapar:

1. **Normalize** eder (exponent'ler eşitlenir)
2. **Çarpma işlemi** mantissa üzerinde yapılır
3. **Toplama işlemi** yapılır (carry + shift)
4. **Sonuç normalize edilir ve yuvarlanır**
5. **IEEE-754 formatında output** verir

Bu adımlar klasik ALU'da yapılamaz çünkü ALU sadece tamsayılarla çalışır.

---

## 🧪 5️⃣ STM32 Örneği (FPU’lu ve FPU’suz)

| İşlemci | FPU | `float` İşlem Hızı |
| --- | --- | --- |
| STM32F103 (M3) | ❌ Yok | Yavaş (yazılım emülasyonu) |
| STM32F407 (M4) | ✔️ Var | 10× daha hızlı |

> -mfpu=fpv4-sp-d16 -mfloat-abi=hard ile GCC derleyiciye “donanım FPU kullan” denir
> 

---

## 🔧 6️⃣ Nasıl Kullanılır?

Programcı olarak sen **float, double** değişkenleri normal kullanırsın:

```c
float a = 3.14;
float b = 2.71;
float result = a * b + 1.0;

```

Eğer derleyiciye doğru **FPU tanımı yapılmışsa**, bu işlemler FPU komutlarıyla derlenir (örneğin: `VMUL.F32`, `VADD.F32`)

Derleyici ayarları:

| Ayar | Açıklama |
| --- | --- |
| `-mfloat-abi=soft` | Yazılım tabanlı float işlemi |
| `-mfloat-abi=hard` | Donanım FPU kullanılır |
| `-mfpu=fpv4-sp-d16` | Cortex-M4 için uygun FPU tipi |

---

## 🔋 7️⃣ Neden Önemlidir?

| Konu | FPU Yoksa | FPU Varsa |
| --- | --- | --- |
| Hız | Çok yavaş | Çok hızlı |
| Kod boyutu | Büyük (yazılımla yapılır) | Küçük (tek komut) |
| Enerji | Fazla harcar | Daha az harcar |
| Karmaşık işlem | Uygun değil | Mümkün (filter, FFT vs.) |

---

## 🧠 Sonuç

- FPU, float işlemlerini donanımda yürüten özel bir birimdir
- Yazılımda float kullanılabilir ama **performans ciddi düşer**
- FPU sayesinde **sayısal filtreleme, kontrol algoritmaları, sensör işlemleri, DSP görevleri** mümkün hale gelir
- Özellikle **gerçek zamanlı sistemlerde** FPU **hayati önem taşır**
- Kullanmak için sadece doğru işlemci ve derleyici ayarları gerekir, yazılımsal olarak kod fark etmez

FPU, yazılımda basit bir `float x = a / b;` ifadesinin arkasındaki **donanımsal sihri** sağlayan çekirdektir.

---

## 🔋 1️⃣ Tarihsel Başlangıç: TTL Lojik ve 5V Standardı

### 📌 Neden 5V?

- İlk dijital devreler (özellikle **TTL – Transistor-Transistor Logic**) 1960’larda yaygınlaştı.
- Bu lojik ailesi **kararlı çalışmak** için **yaklaşık 5V** gerilime ihtiyaç duyuyordu.
- Elektronik bileşenlerin üretim toleransları genişti, bu yüzden **5V toleranslı ve basit** sistemler tercih edildi.
- Bilgisayar endüstrisi 1970'lerden itibaren bu standardı benimsedi (örneğin: Intel 8080, Z80, vs.)

### 🔋 Güç Kaynakları:

- **3 adet AA pil** (1.5V × 3 = 4.5V) → yeterince yakın
- **Regüleli adaptörle 5V üretmek kolaydı**

---

## ⚡ 2️⃣ 3.3V’un Ortaya Çıkışı: Düşük Güç, Yüksek Entegrasyon

1980’ler sonu – 1990’larda **güç tüketimi** kritik hale gelmeye başladı:

- Daha fazla transistör = daha çok ısı ve enerji
- Yüksek hızda çalışan sistemler için **daha düşük voltajda çalışmak şart oldu**

> Çözüm: Transistör boyutlarını küçült → daha düşük gerilimle çalıştır
> 

### 📌 Neden 3.3V?

- 5V’a göre **daha az güç tüketir** (P = V²/R → voltajın karesiyle artar)
- O zamanın üretim teknolojisiyle **mantıklı alt sınır buydu**
- 3.3V, hâlâ **eski 5V sistemlerle uyumlu** olarak çalıştırılabiliyordu (level shift gerekse bile)

---

## 🧠 3️⃣ Pil Gerilimleriyle İlişki

Senin gözlemin burada çok isabetli:

- **1 adet AA pil** ≈ 1.5V (alkalin) veya ≈1.65V (tam dolu NiMH)
- 2 × 1.65V = **3.3V**
- 3 × 1.65V = **4.95V** ≈ **5V**

Yani:

📌 **2 ya da 3 pille kolayca 3.3V veya 5V elde edilebilir**

→ Bu da taşınabilir sistemler için büyük avantaj sağlar.

Ayrıca:

- **USB** standardı da 5V'tur → cihazlar doğrudan USB’den çalışabilir
- 3.3V regülatörlerle USB’den 3.3V üretmek **çok kolaydır**

---

## 🔧 4️⃣ Elektronik Devrelerde 3.3V ve 5V’un Yaygınlığı

| Gerilim | Kullanım Alanı | Neden Popüler? |
| --- | --- | --- |
| 5V | Eski mikrodenetleyiciler, USB, sensörler | Tarihi TTL uyumluluğu, güç kaynağı kolaylığı |
| 3.3V | Modern mikrodenetleyiciler, RF modüller | Düşük güç, uyumlu teknolojiler |
| 1.8V | Yeni nesil CPU, DRAM | Gelişmiş, yüksek hızda düşük güç tüketimi |

---

## 🧮 Sonuç

- **5V**, TTL teknolojisinden gelen tarihi bir standarttır
- **3.3V**, daha düşük güç tüketimi ve pil uyumu nedeniyle modernleştirilmiş bir çözümdür
- **1.65V'luk piller**, bu değerlerin **serisel kombinasyonlarıyla kolayca elde edilebilir** olduğundan, bu voltajlar **pratik uygulamalarla da desteklenmiştir**
- Bu sayede bu değerler hem **endüstri standardı**, hem **tarihsel miras**, hem de **pratik mühendislik seçimi** olarak yaygınlaşmıştır

"2×1.65=3.3" ve "3×1.65=4.95" gözlemi bu yüzden **çok yerinde ve anlamlıdır** – bu voltaj seviyeleri teknik olduğu kadar **lojistik olarak da seçilmiş** değerlerdir.

### 🧠 "Flash 0 Wait State Exec" Ne Demektir?

Ve neden **SRAM işlemciyle eşit hızda**, ama **Flash daha yavaş çalışır?**

---

## 1️⃣ 📦 Flash Bellek Nedir?

- **Kalıcı bellektir** – kodun ve sabit verinin tutulduğu yerdir
- Mikrodenetleyici açıldığında **program komutları Flash’tan okunur ve yürütülür**
- Ancak: **Flash belleğin erişim süresi (read latency)**, işlemciye göre daha yavaştır

---

## 2️⃣ ⏱️ Wait State (Bekleme Durumu) Nedir?

**Wait state**, işlemcinin bir bellek erişimini beklerken **durdurulmasıdır**.

Çünkü:

⛔ CPU bir saat darbesinde işlem yapabilir ama

🐢 Flash o kadar hızlı cevap veremez → CPU beklemek zorunda kalır

📌 **1 wait state = işlemcinin 1 saat çevrimi boyunca beklemesi**

---

## 3️⃣ "0 Wait State Execution" Ne Anlama Gelir?

Eğer Flash:

- **İşlemcinin hızına yetişebiliyorsa**, yani aynı clock hızında erişim verebiliyorsa,
- CPU **beklemeden**, her cycle'da komut çekebilir

👉 Bu duruma **"0 wait state execution"** denir.

> Örneğin:
> 
> - CPU = 24 MHz → Flash erişimi de 24 MHz ise → ✅ 0 wait state
> - CPU = 72 MHz → Flash sadece 24 MHz hızında cevap verebiliyor → ❌ Flash gecikir → bekleme gerekir (wait state eklenir)

---

## 4️⃣ ⚡ SRAM ile Flash Arasındaki Fark

| Özellik | SRAM | Flash |
| --- | --- | --- |
| Türü | Geçici (RAM) | Kalıcı (ROM) |
| Erişim Süresi | Çok hızlı (1 cycle) | Yavaş (genelde 2–5 cycle) |
| CPU Uyumlu | Hızlı clock’ta sorun çıkarmaz | Yüksek hızda wait state gerekir |
| Kullanım | Değişkenler, stack, heap | Kod ve sabit veriler |

### 📌 SRAM = CPU ile aynı hızda çalışabilir

Çünkü: SRAM donanım olarak daha hızlı ve CPU ile doğrudan bağlıdır.

→ **0 wait state garantilidir**

---

## 5️⃣ STM32 Örneği: Flash Wait State Ayarı

STM32F4 gibi işlemcilerde `FLASH->ACR` register'ı ile wait state sayısı ayarlanır:

```c
FLASH->ACR = FLASH_ACR_LATENCY_5WS; // 5 wait state (168 MHz için)

```

| CPU Hızı (MHz) | Flash Wait State |
| --- | --- |
| 0–30 | 0 WS |
| 30–60 | 1 WS |
| 60–90 | 2 WS |
| ... | ... |
| 168 MHz | 5 WS |

---

## 6️⃣ Optimize Kod Çalışması İçin

CPU Flash’tan kod okurken yavaşlarsa:

- **Kod** önce **SRAM’e kopyalanıp oradan yürütülebilir**
- Böylece 0 wait state elde edilir

```c
__attribute__((section(".ramfunc")))
void fast_function() {
    // hızlı erişilmesi gereken kritik kod
}

```

---

## ✅ Özet

| Kavram | Anlamı |
| --- | --- |
| **Wait State** | CPU’nun bellek erişimini beklemek zorunda kalması |
| **0 Wait State** | CPU’nun hiçbir cycle kaybı olmadan doğrudan erişmesi |
| **SRAM** | CPU ile eş hızda çalışabilir, 0 WS garantilidir |
| **Flash** | Daha yavaş, yüksek frekansta wait state gerekir |
| **"0 Wait State Exec"** | Flash belleğin CPU hızına tam yetiştiği özel senaryodur |

Flash belleğin yavaşlığı, yüksek CPU frekanslarında wait state’lerle telafi edilir. Kritik kodlar SRAM’e taşınarak bu bekleme ortadan kaldırılabilir.

ARM tabanlı işlemciler (özellikle **Cortex-M** serisi) için güç yönetimi, sistemin enerji verimliliğini artırmak amacıyla donanımsal ve yazılımsal olarak kontrol edilen çeşitli **güç modları (power modes)** içerir. Aşağıda bu modları **en yüksek güç tüketiminden en düşük olanına** doğru sıralı ve sistematik şekilde açıklıyorum:

---

## 🔋 ARM Cortex-M İşlemcilerde Tüm Güç Modları

### 🟢 1. **Run Mode (Çalışma Modu)**

| Özellik | Açıklama |
| --- | --- |
| CPU çalışıyor | Kod yürütülüyor |
| Tüm çevre birimleri aktif | Tam performans |
| Güç tüketimi | En yüksek |

Kullanım: Normal kod çalışması sırasında.

---

### 🌙 2. **Sleep Mode (Uyku Modu)**

| Özellik | Açıklama |
| --- | --- |
| CPU durur | Saat durur, pipeline durur |
| Çevre birimleri çalışır | Timer, USART gibi çevreler devam edebilir |
| Wake-up kaynağı | Herhangi bir kesme (interrupt) |
| Güç tüketimi | Orta düzeyde tasarruf |

Kullanım: İşlemci boşta, ama çevre birimleri çalışmaya devam ediyor.

**Aktivasyon:** `__WFI()` veya `__WFE()` assembler komutları

`SCB->SCR &= ~SCB_SCR_SLEEPDEEP_Msk;`

---

### 🌑 3. **Deep Sleep Mode (Derin Uyku / SleepDeep)**

### Varyantlar:

- **Stop Mode**
- **Standby Mode**
- (Bazı çiplerde ayrıca: Shutdown / Power-down / Low-power run)

---

### 🟡 3A. **Stop Mode**

| Özellik | Açıklama |
| --- | --- |
| CPU + bazı çevreler durur | Saatler kapanabilir ama bazı çevreler kalabilir |
| RAM korunur | Veri kaybı olmaz |
| Wake-up | RTC, EXTI, GPIO, Timer |
| Güç tüketimi | Düşük |

**Kullanım:** Uyandırılması gereken ama düşük güç tüketimi isteyen durumlarda.

---

### 🔴 3B. **Standby Mode**

| Özellik | Açıklama |
| --- | --- |
| CPU + RAM durur | Sadece RTC/backup domain çalışır |
| RAM içeriği silinir | Yeniden başlatma gibi |
| Wake-up | RTC alarm, EXTI pin, WKUP pin |
| Güç tüketimi | Çok düşük |

**Kullanım:** En düşük güç modudur, sistem neredeyse tamamen kapanır.

---

## 💡 Ekstra Modlar (Bazı ARM Tabanlı Sistemlerde):

| Mod Adı | Açıklama |
| --- | --- |
| **Low Power Run** | Düşük frekans + düşük voltajda çalışır |
| **Shutdown** | Tamamen güç kesilir, yalnızca power-on reset ile uyanır |
| **VBAT Domain** | RTC ve backup register’lar için ayrı besleme ile çalışabilir |

---

## 📊 Güç Modları Karşılaştırma Tablosu

| Mod | CPU | RAM | Çevreler | Wake-up Süresi | Güç Tüketimi |
| --- | --- | --- | --- | --- | --- |
| **Run** | ✔️ | ✔️ | ✔️ | - | 🔴 Yüksek |
| **Sleep** | ❌ | ✔️ | ✔️ | Hızlı | 🟠 Orta |
| **Stop** | ❌ | ✔️ | ⚠️ Bazıları | Orta | 🟢 Düşük |
| **Standby** | ❌ | ❌ | ❌ | Yavaş | 🔵 Çok Düşük |
| **Shutdown** | ❌ | ❌ | ❌ | Çok Yavaş | ⚫ En düşük |

---

## ⚙️ Nasıl Seçilir?

- **Yüksek tepki süresi + işlem gerekirse** → Run / Sleep
- **Ara verilecek ama veri korunacaksa** → Stop
- **Tam uyuma geçilecekse** → Standby
- **Pilin son %1'i** gibi senaryolarda → Shutdown

---

ARM mimarisinde bu güç modları, hem **kullanıcı yazılımıyla kontrol edilebilir**, hem de **interrupt yapısı ve wake-up kaynaklarıyla dinamik olarak yönetilebilir**. Her güç modunun donanım-tabanlı tetikleyicileri ve geri dönüş yolları vardır. Bunlar üreticiye göre (ST, NXP, vs.) farklı detaylara sahip olsa da temel mimari bu şekildedir.

### ⚙️ **DMA (Direct Memory Access) – Kısaca**

**DMA**, CPU'yu meşgul etmeden **veriyi bellekler veya çevre birimleri arasında doğrudan taşıyan** donanım birimidir.

---

### 📌 Ne İşe Yarar?

- **CPU olmadan veri aktarımı**
- RAM ↔ Perifer (ADC, USART, SPI vs.)
- RAM ↔ RAM
- Flash ↔ RAM (bazı sistemlerde)

---

### 🚀 Avantajları

| Özellik | Yarar |
| --- | --- |
| CPU boşta kalır | CPU başka işler yapabilir |
| Hızlıdır | Donanım tabanlı, kesintisiz çalışır |
| Enerji tasarrufu | İşlemci uyurken bile veri taşınabilir |

---

### 🔁 Nasıl Çalışır?

1. **Kaynak adres** ve **hedef adres** belirlenir
2. **Veri boyutu** tanımlanır
3. **Transfer tipi** seçilir (örneğin: çevre → bellek)
4. DMA başlatılır → veri aktarılır
5. İş bitince **interrupt** ile CPU bilgilendirilir

---

### 🧠 Kullanım Örnekleri

- ADC sonuçlarını CPU olmadan RAM'e yazmak
- UART ile gelen veriyi otomatik almak
- Ses, video, sensör verisini yüksek hızla işlemek

---

**Sonuç:**

DMA, **hızlı, verimli ve CPU dostu veri aktarımı** sağlar. Özellikle gerçek zamanlı sistemlerde **vazgeçilmezdir**.

### 🔌 MCU’larda Kullanılan Tüm Temel Haberleşme Protokolleri

(**Ana başlıklar + önem sırası + kısa açıklamalar**)

---

## 🧭 1️⃣ **Senkronsuz Seri Protokoller (Asynchronous Serial)**

### 📌 **UART / USART (Universal Asynchronous Receiver Transmitter)**

- **2 hat**: TX (gönder), RX (al)
- Saat sinyali taşımaz, **veri + baudrate** ile zamanlama
- Basit, yaygın, düşük veri hızları (~115200 bps tipik)

**Kullanım:** Bluetooth, GPS, PC seri port, debug

---

## 🔗 2️⃣ **Senkron Seri Protokoller (Synchronous Serial)**

### 📌 **SPI (Serial Peripheral Interface)**

- **4 hat**: MISO, MOSI, SCK, SS
- Master-Slave yapısı
- Yüksek hızlı (~10+ Mbps) tam-dupleks iletişim
- Basit ama çok kablo → 1 master : N slave için ayrı SS gerekir

**Kullanım:** SD kart, LCD, sensörler (IMU, flash)

---

### 📌 **I2C (Inter-Integrated Circuit)**

- **2 hat**: SDA (veri), SCL (saat)
- Adres tabanlı, çoklu cihaz (multi-slave) destekler
- Düşük – orta hız (~400 kHz – 1 MHz)
- Donanım adresleme, hat paylaşımı avantajı

**Kullanım:** EEPROM, RTC, sıcaklık sensörleri, ekranlar

---

### 📌 **I2S (Inter-IC Sound)**

- SPI tabanlı, ses verisi için optimize edilmiş protokol
- 3 hat (WS, SCK, SD)
- PCM / dijital ses aktarımı

**Kullanım:** Audio codec, dijital mikrofon, hoparlör

---

## 🌐 3️⃣ **Ağ Tabanlı / Kompleks Protokoller**

### 📌 **CAN (Controller Area Network)**

- Otomotiv ve endüstride standart
- Yüksek güvenlik + hata kontrolü
- **Multi-master**, **mesaj tabanlı**
- Hız: ~1 Mbps

**Kullanım:** Otomobil, robot, endüstriyel modüller

---

### 📌 **USB (Universal Serial Bus)**

- Bilgisayara bağlantı (device / host)
- Yüksek hız (USB FS: 12 Mbps, HS: 480 Mbps)
- Karmaşık protokol, yazılım yığını gerekir

**Kullanım:** Klavye, fare, bellek, debug, CDC (sanalsal UART)

---

### 📌 **Ethernet (RMII/MII)**

- Fiziksel bağlantı: RJ45
- Yüksek bant genişliği + ağ protokol desteği (IP/TCP)
- Donanım + yazılım yığını gerektirir

**Kullanım:** Web tabanlı kontrol, IoT, veri loglama

---

## 📡 4️⃣ **Kablosuz / Gömülü RF Protokolleri**

### 📌 **BLE (Bluetooth Low Energy)**

- Düşük güç tüketimli kısa mesafe iletişim
- UART veya USB üzerinden bağlanabilir modüller

### 📌 **Wi-Fi (ESP, NRF, STM32WL)**

- İnternete bağlanabilen sistemler için
- TCP/IP stack gerekir

---

## 🧠 En Önemlileri (Uygulama Bağımlı Ama Genel Sıralama):

| Protokol | Neden Önemli? |
| --- | --- |
| **UART** | Her MCU’da var, debug için kritik |
| **SPI** | Yüksek hız, SD kart, ekran, sensör |
| **I2C** | Çok cihazlı, düşük pinli iletişim |
| **CAN** | Otomotiv ve endüstri standardı |
| **USB** | PC bağlantısı, firmware güncelleme |
| **Ethernet** | IoT, uzak erişim |

---

## 📦 Özet Tablosu

| Protokol | Tip | Hat Sayısı | Hız (tipik) | Kullanım Alanı |
| --- | --- | --- | --- | --- |
| UART | Async | 2 | ~115 kbps | Debug, modül iletişimi |
| SPI | Sync | 4 | ~10 Mbps | SD kart, ekran, sensör |
| I2C | Sync | 2 | ~400 kHz | EEPROM, RTC, sensör |
| I2S | Sync | 3 | ~1 Mbps | Ses |
| CAN | Frame | 2 (differential) | ~1 Mbps | Araç, endüstri, robotlar |
| USB | Host/Dev | 2 + data | 12–480 Mbps | PC bağlantısı |
| Ethernet | Packet | 2 (RMII) | 10–100 Mbps | IoT, ağ tabanlı sistemler |

---

### 🧠 STM32 ve diğer ARM Cortex-M tabanlı mikrodenetleyicilerde:

## 🔍 1️⃣ **CRC Calculation Unit (CRC Hesaplama Birimi)**

**CRC (Cyclic Redundancy Check)**, verinin bütünlüğünü kontrol etmek için kullanılan bir algoritmadır.

MCU içinde donanım olarak bulunan **CRC unit**, bu işlemi **CPU’dan bağımsız, hızlı ve doğru** şekilde yapar.

### ✅ Kullanım Amaçları:

- **Flash bellek doğrulama** (bootloader, firmware update)
- **Haberleşmede hata kontrolü**
- **Bellek alanı kontrolü**

### 🔧 Özellikler:

- Genellikle **32-bit CRC** hesaplar (`CRC32`)
- Giriş verisi `CRC->DR` register'ına yazılır
- Sonuç `CRC->DR`'den okunur
- STM32’de: `RCC->AHB1ENR |= RCC_AHB1ENR_CRCEN;` ile aktif edilir

---

## 🧬 2️⃣ **Unique ID (Benzersiz Kimlik Numarası)**

Her STM32 mikrodenetleyicide donanımsal olarak yer alan, **sadece o çipe ait olan sabit bir ID**'dir. Silinemez, değiştirilemez.

### 📌 Özellikleri:

- Genelde **96-bit uzunluğundadır**
- Adres: `0x1FFF F7E8` (STM32F4 örneği)
- Üç ayrı 32-bit register’da tutulur
- Her üretimde benzersizdir

### 🎯 Kullanım Alanları:

- Lisanslama / şifreleme
- Donanım kimlikleme (IoT cihaz ID'si)
- Ürün seri numarası

```c
uint32_t uid0 = *(uint32_t*)0x1FFF7A10;
uint32_t uid1 = *(uint32_t*)0x1FFF7A14;
uint32_t uid2 = *(uint32_t*)0x1FFF7A18;

```

---

## 📦 3️⃣ **All Packages (Tüm Paketler)**

**"Packages"**, aynı mikrodenetleyici yongasının fiziksel olarak farklı **entegre yapılarla (kılıf, pin sayısı, boyut vs.)** sunulmasıdır.

### 📦 Yaygın Paket Tipleri:

| Paket Adı | Açıklama | Kullanım |
| --- | --- | --- |
| **LQFP** | Quad flat package, 32/64/100/144 pin | Geliştirme kartları |
| **BGA** | Ball grid array (alt küre lehimli) | Kompakt, yüksek pinli |
| **QFN** | Quad flat no-lead | Küçük, SMD dostu |
| **WLCSP** | Wafer level chip scale | Ultra küçük cihazlar |
| **TSSOP** | Thin shrink small-outline | Düşük pin sayılı modeller |

### 📌 Örnek:

`STM32F103C8T6`

- `C8`: 64 KB Flash
- `T`: LQFP package
- `6`: Sıcaklık ve özellik sınıfı

---

## 🧩 Özet

| Bileşen | Görevi |
| --- | --- |
| **CRC Unit** | Hızlı, donanımsal veri bütünlüğü kontrolü |
| **Unique ID** | Her MCU’ya özel donanımsal kimlik |
| **Package Types** | Fiziksel entegre yapısı, pin dizilimi vs. |

Bu donanımsal yapılar, STM32 gibi MCU’ların **güçlü, özelleştirilebilir ve güvenli** sistemlerde kullanılmasını sağlar.

### ⏱️ **Elapsed Time Counter vs Clock/Calendar**

**(Zaman Sayacı vs Takvim-Saati Modülü)**

İkisi de zamanı izlemek için kullanılır ama **amaçları, yapıları ve kullanım senaryoları farklıdır.** Aşağıda teknik olarak detaylı ve karşılaştırmalı şekilde açıklıyorum:

---

## 🧮 1️⃣ Elapsed Time Counter (Geçen Süre Sayacı)

### 📌 Tanım:

**Zamanı sürekli artan bir sayaçla** takip eder. Başlangıç noktası **genellikle 0’dır**, zaman geçtikçe artar.

🟢 Genellikle bir **timer**, **RTC prescaler**, veya **systick** ile oluşturulur.

### 🔧 Özellikler:

| Özellik | Açıklama |
| --- | --- |
| Başlangıç | Sayaç sıfırdan başlar |
| Görev | Belirli bir olaydan sonra ne kadar zaman geçtiğini hesaplamak |
| Format | Salt zaman değeri (milisaniye, saniye, tick vs.) |
| Donanım | SysTick, Timer, RTC’nin CNT kısmı |

### ✅ Avantajları:

- Basit ve düşük kaynak tüketimi
- Delay/timeout ölçmek için çok uygundur
- Mikrodenetleyici bağımsız olarak yapılandırılabilir

### ❌ Dezavantajları:

- **Tarih veya saat bilgisi içermez**
- Güç kesilirse sayaç sıfırlanır
- Gerçek dünya saatiyle senkron değildir

---

## 🕒 2️⃣ Clock/Calendar (RTC – Saat/Takvim Modu)

### 📌 Tanım:

**Gerçek zamanlı saat (RTC)** ile çalışan, **gün, ay, yıl, saat, dakika, saniye** gibi tarihsel bilgileri saklayan yapıdır.

### 🔧 Özellikler:

| Özellik | Açıklama |
| --- | --- |
| Başlangıç | Genellikle bir tarih/saat ile başlatılır (ör: 01.01.2024 00:00:00) |
| Görev | Gerçek zamanlı tarih/saat takibi |
| Format | Yıl, Ay, Gün, Saat, Dakika, Saniye |
| Donanım | RTC (Real Time Clock) + Takvim Modülü |

### ✅ Avantajları:

- Gerçek dünya saatine göre çalışır
- Güç kesintisinden etkilenmez (Vbat ile çalışabilir)
- Alarm, periyodik uyarı, zaman damgası gibi görevler için idealdir

### ❌ Dezavantajları:

- Konfigürasyonu karmaşıktır
- Daha fazla donanım kaynağı ve enerji tüketebilir
- Yavaş saatle çalışır (tipik 32.768 kHz) → zaman çözünürlüğü düşüktür

---

## 🧩 Karşılaştırma Tablosu

| Özellik | **Elapsed Time Counter** | **Clock/Calendar (RTC)** |
| --- | --- | --- |
| Zaman Tipi | Geçen süre (duration) | Gerçek tarih & saat |
| Tipik Format | ms, s, ticks | Yıl, ay, gün, saat, dakika, saniye |
| Kullanım Amacı | Gecikme, timeout, sayaç | Tarih/saat gösterimi, alarm, timestamp |
| Donanım Kaynağı | Timer, SysTick, RTC CNT | RTC Takvim modülü |
| Güç Kesilme Duyarlılığı | Evet (sayaç sıfırlanır) | Hayır (Vbat destekli çalışır) |
| Karmaşıklık | Basit | Karmaşık yapılandırma |
| Doğruluk | Yüksek çözünürlük | Düşük frekanslı, daha az hassas |

---

## 🧠 Uygulama Örnekleri

| Senaryo | Hangi Yapı? |
| --- | --- |
| 500 ms sonra LED kapansın | ✅ Elapsed Time Counter |
| RTC tabanlı alarm kurulsun | ✅ Clock/Calendar |
| Cihaz açıldıktan sonra ne kadar çalıştı | ✅ Elapsed Time |
| Veriye zaman damgası eklensin | ✅ RTC Calendar |
| Buton basıldıktan 2 saniye sonra işlem | ✅ Elapsed Time |
| Saat ve tarih LCD'de gösterilsin | ✅ Clock/Calendar |

---

## 🔚 Sonuç:

- **Elapsed Time Counter**: "Bu olaydan sonra kaç ms geçti?" sorusunu cevaplar
- **Clock/Calendar**: "Şu an tarih ve saat kaç?" sorusunu cevaplar
- Sistemler genellikle **ikisini birlikte** kullanır: biri kısa süreli işlemler için, diğeri kalıcı zaman yönetimi için.

### 🧠 **Unified Harvard Mimarisi** Nedir?

(ve klasik **Harvard**, **Von Neumann** mimarileriyle ilişkisi)

---

## 🏛️ 1️⃣ Temel Kavramlar

Önce klasik mimarileri hatırlayalım:

| Mimarî | Komut Belleği | Veri Belleği | Ortak Veri Yolu | Açıklama |
| --- | --- | --- | --- | --- |
| **Von Neumann** | Aynı | Aynı | ✔️ | Komut ve veri aynı bellek ve yolda |
| **Harvard** | Ayrı | Ayrı | ❌ | Komut ve veri için ayrı bellek ve yol |

---

## 🔁 2️⃣ **Unified Harvard Architecture** – Tanım

**Unified Harvard Architecture**, klasik Harvard mimarisini temel alır ancak **komut ve veriyi zaman zaman aynı bellekten erişilebilir kılar.**

Bu mimaride:

- Fiziksel olarak **komut ve veri yolları ayrı** olabilir (Harvard)
- Ancak **adres alanı birleşiktir** – yani **tek bir bellek haritası** üzerinden erişim yapılabilir
- Sistem, komut belleğine de veri olarak erişebilir (örneğin `const` değişkenler Flash'ta tutulur)

---

## 📦 3️⃣ Neden “Unified”?

- Klasik Harvard’da **komut belleğine veri gibi erişilemezdi**
- Unified yapıda **işlemci veri belleğinden veri okur gibi, Flash'tan da sabit veri okuyabilir**
- Bu, donanımın Harvard, **yazılımın Von Neumann gibi davranması** anlamına gelir

---

## ⚙️ 4️⃣ Teknik Özellikler

| Özellik | Unified Harvard |
| --- | --- |
| Bellek yolları (bus) | Ayrı (komut & veri) |
| Adres haritası | Birleşik (tek memory map) |
| Komut → veri gibi erişim | ✔️ Mümkün |
| RAM ↔ Flash veri erişimi | ✔️ Evet |
| Pipeline verimliliği | ✔️ Daha iyi |

---

## 🧠 5️⃣ STM32 Örneği ile Bağlantı

- **Cortex-M çekirdeği**, fiziksel olarak Harvard’dır
    - Instruction Bus: Flash'tan komut çeker
    - Data Bus: SRAM veya Flash’tan veri çeker
- Ancak adresleme olarak tümü **tek bellek haritası içinde tanımlıdır** (`0x08000000` Flash, `0x20000000` SRAM, vs.)
- Bu nedenle, **`const` veri** gibi sabit bilgiler, komut belleğinde tutulabilir ve veri olarak erişilebilir

---

## 🎯 6️⃣ Avantajlar ve Kullanım Sebepleri

| Avantajlar | Açıklama |
| --- | --- |
| Pipeline ve erişim paralelliği | Komut ve veri aynı anda çekilebilir |
| Bellek esnekliği | `const`, lookup table gibi veri Flash'ta tutulabilir |
| Tek adres haritası | Yazılımı sadeleştirir, adres çakışmasını önler |
| Uyumluluk | Derleyiciler için yönetimi kolaylaştırır |

---

## 🔚 Sonuç:

**Unified Harvard Architecture**, donanım olarak Harvard (ayrı yollar), mantık olarak Von Neumann (birleşik adresleme) yapısına sahip **hibrit** bir mimaridir.

📌 STM32 gibi ARM Cortex-M mikrodenetleyiciler, bu yapıyı kullanır:

- Ayrı yollar → paralel erişim & performans
- Ortak adresleme → kolaylık & esneklik

Yani:

> Harvard performansı + Von Neumann yazılım kolaylığı = ✅ Unified Harvard
> 

### 💾 Flash, ROM, PROM, EPROM, EEPROM, Flash vs Flush – Kapsamlı Karşılaştırma

---

## 🧠 1️⃣ Öncelikle: Ortak Özellik – **Non-Volatile Memory (NVM)**

Aşağıdaki bellek türlerinin hepsi **kalıcıdır**:

Elektrik kesilse bile veri kaybolmaz.

---

## 📦 2️⃣ Bellek Türlerinin Karşılaştırması

| Özellik | **ROM** | **PROM** | **EPROM** | **EEPROM** | **Flash** |
| --- | --- | --- | --- | --- | --- |
| Açılım | Read-Only Memory | Programmable ROM | Erasable PROM | Electrically EPROM | Flash EEPROM |
| Yazılabilirlik | ❌ Hayır (fabrikada) | ✔️ 1 kez (sonradan) | ✔️ Silinip yazılır | ✔️ Silinip yazılır | ✔️ Silinip yazılır |
| Silme yöntemi | Yok | Yok | **UV ışığıyla** | **Elektriksel olarak** | **Elektriksel – blok** |
| Silme türü | – | – | Tüm chip | Byte-byte | **Blok** (sektör-grup) |
| Hız | Çok yüksek | Yüksek | Düşük | Orta | **Yüksek** |
| Dayanıklılık | Çok yüksek | Yüksek | Orta | Orta – az silme ömrü | **Yüksek (10k–100k erase)** |
| Tipik Kullanım | BIOS, sabit firmware | Sabit konfig verisi | Tarihsel, eğitim amaçlı | RTC verisi, küçük ayar | Mikrodenetleyici kodu, USB bellek, SSD |

---

## 🔁 3️⃣ **Flash Belleğin EEPROM’dan Farkı Nedir?**

| Kriter | EEPROM | Flash |
| --- | --- | --- |
| Silme birimi | Byte/word | **Blok/sektör** |
| Yazma şekli | Küçük alanlar | Büyük veri blokları |
| Kullanım yeri | Ayar/konfig, küçük veri | Program kodu, firmware, dosya |
| Hız | Daha yavaş | **Daha hızlı** |

📌 **Flash belleğin EEPROM'dan en büyük farkı**, **silmenin yalnızca büyük bloklarla yapılabilmesi**dir. EEPROM ise byte bazında çalışabilir, ama bu da daha yavaş ve daha az dayanıklıdır.

---

## 🔄 4️⃣ **Flush Nedir?**

🟥 **Flush**, bir bellek tipi değildir.

Bilgisayar bilimlerinde “flush”:

> Bellekte (cache veya buffer) tutulan verinin kalıcı belleğe yazılması anlamına gelir.
> 

### Örnekler:

- `fflush(stdout);` → buffer'daki yazı doğrudan ekrana gönderilir
- Dosya sisteminde: `fsync()` → dosya tamponundaki veri diske kalıcı olarak yazılır
- Flash bellekte: `flash_flush()` → yazılan veri RAM yerine gerçekten Flash’a işlenir

### Flush ≠ Flash

- **Flash = kalıcı bellek teknolojisi**
- **Flush = yazılım işlemi, geçici veriyi kalıcı hale getirir**

---

## ✅ Özet Karşılaştırma Tablosu

| Ad | Silme | Yazma | Kalıcılık | Silme Yöntemi | Güncel Kullanımı |
| --- | --- | --- | --- | --- | --- |
| ROM | ❌ | ❌ | ✔️ | – | Sabit sistem kodu (eski) |
| PROM | ❌ | 1 kez | ✔️ | – | Tarihsel/kısıtlı sistemler |
| EPROM | ✔️ | ✔️ | ✔️ | UV ışık | Tarihsel, laboratuvar |
| EEPROM | ✔️ | ✔️ | ✔️ | Elektrik (byte) | RTC, config, sensör kalibrasyonu |
| **Flash** | ✔️ | ✔️ | ✔️ | Elektrik (blok) | MCU kodu, SSD, USB bellek |
|  |  |  |  |  |  |

---

### 📌 Kısacası:

- **Flash** = modern, hızlı, blok silinebilen EEPROM türü
- **Flush** = veriyi bellekte tutmak yerine hemen kalıcılaştırmak (yazılım düzeyinde)
- EEPROM küçük veriler, Flash ise büyük kod alanları için uygundur
- ROM/PROM/EPROM daha çok tarihsel kökenli, günümüzde yerini Flash’a bırakmıştır.

### 🧠 RAM'in Tarihsel Gelişimi ve Temel Bellek Türleri

(Tape → Disk → SRAM → DRAM'e evrimsel ve yapısal bakış)

---

## ⏳ 1️⃣ **RAM Nedir?**

**RAM (Random Access Memory)**, veriye **rastgele erişim** sağlayan, yani belleğin herhangi bir adresine **doğrudan ve aynı sürede** erişilebilen geçici bir bellek türüdür.

> Modern anlamda RAM, sistemin çalışma belleğidir (işlem sırasında aktif kullanılan veri).
> 

---

## 📜 2️⃣ **Tarihsel Gelişim Süreci**

### 🧾 A. **Tape Memory (Manyetik Teyp – Sekansiyel Erişim)**

| Özellik | Açıklama |
| --- | --- |
| Erişim Türü | **Sıralı (sequential)** → veri sırayla aranır |
| Hız | Çok yavaş |
| Kullanım | 1940–50’lerde, ilk dijital sistemlerde |
| Örnek | Delikli kart, manyetik teyp |

### 🔁 Dezavantajı:

- Bellek erişimi için sıralı tarama gerekir → random erişim **yok**

---

### 💽 B. **Disk Memory (Manyetik Diskler – Yarı Sekansiyel)**

| Özellik         | Sıralıdan daha iyi, ama hâlâ random erişim yavaş |
| Dönem           | 1950’lerden itibaren

| Yapı            | Plak (platter) + okuma/yazma kafası

| Örnek           | Hard disk (HDD)

> Random erişim teorik olarak mümkündür ama mekanik gecikme (seek time) nedeniyle oldukça yavaştır.
> 

---

### ⚡ C. **RAM (Gerçek Random Erişim)**

Elektronik tabanlı RAM’lerin ortaya çıkışıyla **rastgele erişim gerçekten mümkün hale geldi.**

---

## 💾 3️⃣ RAM Türleri: **SRAM vs DRAM**

### 📘 A. **SRAM (Static RAM)**

| Özellik | Açıklama |
| --- | --- |
| Hücre yapısı | 6 transistör (flip-flop yapısı) |
| Veri tutma | Elektrik varken **sürekli saklar** |
| Yenileme ihtiyacı | ❌ Yok |
| Hız | ✅ Çok hızlı |
| Yoğunluk | ❌ Düşük (büyük alan kaplar) |
| Kullanım | **CPU cache**, register benzeri hızlı alanlar |

> SRAM, transistörle kurulu olduğu için çok hızlıdır ama pahalı ve büyüktür.
> 

---

### 📗 B. **DRAM (Dynamic RAM)**

| Özellik | Açıklama |
| --- | --- |
| Hücre yapısı | 1 transistör + 1 kondansatör |
| Veri tutma | Kondansatörde **şarj olarak saklanır** |
| Yenileme ihtiyacı | ✅ Evet (her ~ms’de yenilenmeli) |
| Hız | Daha yavaş |
| Yoğunluk | ✅ Yüksek (küçük boyut) |
| Kullanım | **Sistem RAM**, DDR bellek, grafik belleği |

> DRAM küçük ve ucuzdur, bu yüzden gigabaytlarca sistem belleği olarak kullanılır.
> 

---

## 🧠 4️⃣ SRAM – DRAM Karşılaştırması

| Özellik | **SRAM** | **DRAM** |
| --- | --- | --- |
| Hücre Yapısı | 6 transistör | 1 transistör + 1 kondansatör |
| Yenileme | Gerekmez | Gereklidir (dynamic) |
| Hız | ✅ Çok hızlı | Orta |
| Maliyet | Yüksek | Düşük |
| Yoğunluk | Düşük | ✅ Yüksek |
| Kullanım Yeri | Cache, mikrodenetleyici RAM | PC RAM, GPU bellek |

---

## 🔂 5️⃣ Modern RAM Yapıları

| Tür | Açıklama |
| --- | --- |
| **SRAM** | Mikrodenetleyici içi RAM, cache |
| **SDRAM** | Senkronize DRAM |
| **DDR, DDR2–5** | PC sistem RAM’leri (yüksek bant genişliği) |
| **eDRAM** | Dahili gömülü DRAM (SoC'lerde) |
| **FRAM/MRAM** | Yeni nesil, kalıcı RAM alternatifleri |

---

## 📌 Sonuç

- **Tape → Disk → RAM** evrimi, rastgele erişimin hız kazanma yoludur
- **SRAM**: hızlı ama büyük ve pahalı → cache için ideal
- **DRAM**: yavaş ama yoğun ve ucuz → sistem RAM için uygun
- Modern sistemler bu bellek türlerini **ihtiyaca göre hibrit** kullanır:
    - CPU içinde SRAM
    - PC RAM olarak DRAM
    - SSD'de Flash
    - Ayar verileri için EEPROM / FRAM

Yani RAM’in bugünkü formu, **hız, maliyet ve enerji dengesi** için onlarca yıl süren teknolojik evrimin sonucudur.

### 🧠 MMIO’da (Memory-Mapped I/O) 32-bit Aligned & 32-bit Data Read/Write Sınırlaması Ne Demektir?

---

## 📌 1️⃣ MMIO (Memory-Mapped I/O) Nedir?

MMIO, **çevre birimlerinin (peripheral) register’larının bellekteki adreslerle eşleştirilerek** işlemci tarafından **veri belleği gibi erişilmesi** yöntemidir.

> Örnek:
> 
> 
> `GPIOA->ODR` = 0x40020014 → bu adrese yazarsan, GPIO portundaki pinler değişir.
> 
> Bu adresler **bellek değil**, **çevre donanım register’larıdır**.
> 

---

## ⚠️ 2️⃣ 32-bit Aligned Nedir?

"**32-bit aligned**" = **adresin 4’ün katı olması** gerekir.

| Doğru Adresler (aligned) | 0x40000000, 0x40000004, 0x40000008, ... |
| Yanlış Adresler (unaligned) | 0x40000001, 0x40000002, 0x40000005 |

🔧 **Neden?**

- Donanım bu adreslere **tek cycle’da** erişmek üzere tasarlanır.
- Unaligned erişim donanım hatasına (bus fault) sebep olabilir.

---

## 🔄 3️⃣ 32-bit Data Read/Write Sınırlaması

"**32-bit data R/W**" = MMIO register'larına **yalnızca 32-bit veri ile** erişebilirsin.

Yani:

| Geçerli İşlem | Açıklama |
| --- | --- |
| `*((volatile uint32_t*)0x40020014) = 0x00000001;` | ✅ 32-bit write, uygun |
| `*((volatile uint16_t*)0x40020014) = 0x0001;` | ❌ **Donanım hatası** olabilir |
| `*((volatile uint8_t*)0x40020015) = 0x01;` | ❌ Adres 32-bit aligned değil |

🔴 Eğer 8-bit ya da 16-bit erişim yaparsan:

- Bazı MMIO birimleri buna izin vermez
- **Bus Fault**, **Hard Fault**, veya **veri bozulması** olabilir

---

## 🛠️ 4️⃣ Nasıl Güvenli Erişim Yapılır?

Her zaman:

```c
#define GPIO_ODR (*(volatile uint32_t*)0x40020014)
GPIO_ODR = 0x00000001;  // doğru kullanım

```

Alternatif:

```c
typedef struct {
    volatile uint32_t MODER;
    volatile uint32_t OTYPER;
    volatile uint32_t OSPEEDR;
    volatile uint32_t PUPDR;
    volatile uint32_t IDR;
    volatile uint32_t ODR;  // offset = 0x14
} GPIO_TypeDef;

#define GPIOA ((GPIO_TypeDef*)0x40020000)
GPIOA->ODR = 0x00000001;  // doğru ve tip güvenli

```

---

## 📦 5️⃣ Neden Bu Sınırlama Var?

- MMIO register’ları, **sabit boyutlu donanım register’larıdır (genellikle 32-bit)**
- Donanımda genellikle **yalnızca 32-bit erişim hattı vardır**
- 8-bit veya 16-bit yazarsan:
    - **Beklenmeyen bitler etkilenebilir**
    - **Atomic olmayan erişim** oluşur (özellikle çok çekirdekli sistemlerde tehlikelidir)

---

## ✅ Özet

| Kavram | Anlamı |
| --- | --- |
| **32-bit aligned** | Adres 4’e tam bölünebilir olmalı (`addr % 4 == 0`) |
| **32-bit data R/W** | MMIO register’larına yalnızca 32-bit veri ile erişilmeli |
| **MMIO hatası** | Unaligned veya 8/16-bit erişimde donanım hata verebilir |
| **Doğru erişim şekli** | `volatile uint32_t*` ile 32-bit adresleme yapılmalı |

Bu sınırlamalar hem **donanım uyumluluğu** hem de **kararlılık** için zorunludur. Özellikle gömülü sistemlerde bunlara dikkat edilmezse **ani sistem çökmeleri** yaşanabilir.