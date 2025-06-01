### 📌 CMOS, Tri-State Buffer ve ARM MCU’larda High-Z Kullanımı

#### 🧠 Tri-State Buffer Nedir?

Tri-state buffer (üç durumlu tampon), çıkışında üç farklı durum üretebilen bir mantık devresidir:

1. **Lojik 1 (HIGH)**
2. **Lojik 0 (LOW)**
3. **Yüksek empedans (High-Z)** → devre dışı, yani sanki bağlantı yokmuş gibi davranır.

#### ⚙️ Neden Gerekli?

Tri-state yapılar, **birden fazla cihazın aynı veri yolunu paylaşabilmesi** için kullanılır. Örneğin bir veri hattında aynı anda birden fazla çıkış aktif olursa kısa devre oluşabilir. Bunu önlemek için sadece bir cihaz aktif çıkışta olur, diğerleri High-Z durumuna alınır.

#### 🧩 Tri-State Çalışma Mantığı

| Giriş (Enable) | Giriş (A) | Çıkış (Y) |
| -------------- | --------- | --------- |
| 0              | X         | High-Z    |
| 1              | 0         | 0         |
| 1              | 1         | 1         |

> Enable = 0 iken çıkış devreden tamamen ayrılır (yüksek empedans – High-Z durumu).

---

### 🧪 High-Z (Yüksek Empedans) Nedir?

High-Z, bir çıkış pininin elektriksel olarak devreyle bağlantısının kesilmiş olması durumudur. Ne lojik 0’dır ne lojik 1. Bu sayede aynı hattan başka bir cihaz veri gönderebilir.

---

### 🔧 ARM Mikrodenetleyicilerde High-Z Durumu

ARM tabanlı mikrodenetleyicilerde GPIO pinleri **MODER** ve **OTYPER** (ya da **CNF**, **CRL/CRH**) gibi register'lar ile yapılandırılır.

#### GPIO pin modları:

* **Input (Pull-up, Pull-down, Floating/High-Z)**
* **Output (Push-Pull, Open-Drain)**
* **Alternate Function (AF)**
* **Analog**

High-Z durumu genellikle şu yollarla elde edilir:

#### 1. **GPIO'u Input Floating moda almak**

```c
GPIOA->MODER &= ~(0x3 << (2 * pin_no));   // Input mode
GPIOA->PUPDR &= ~(0x3 << (2 * pin_no));   // Pull-up/down devre dışı
```

→ Bu durumda pin High-Z olur. Başka bir cihaz bu pini sürebilir.

#### 2. **Open-Drain Output ile High-Z**

```c
GPIOA->OTYPER |= (1 << pin_no);           // Open-drain
```

Open-drain modda sadece ‘0’ sürülebilir, ‘1’ için çıkış sürülmez → bu da High-Z'dir.

---

### 🔄 STM32 ve CMSIS ile Tri-State Kontrolü

CMSIS ile:

```c
// Pin giriş yapılır (High-Z için floating mod)
GPIOA->MODER &= ~(0x3 << (2 * 5));
GPIOA->PUPDR &= ~(0x3 << (2 * 5));
```

---

### 🎯 Nerelerde Kullanılır?

* **Veri yolu paylaşımı** (ör. dış bellek, LCD, SD kart vs.)
* **I²C protokolü**: open-drain + pull-up ile doğal olarak tri-state desteklidir.
* **Debug arayüzleri**: JTAG/SWD hatlarının devre dışında kalması gerekebilir.
* **Çoklayıcılar (MUX)**: sadece bir çıkışın aktif olabilmesi için

---

### 🧷 Not:

Tri-state yapılar mantıksal olarak **anahtar devreler gibidir**, fakat iki çıkışlı bir anahtarda her iki uç da aynı anda aktif olamaz. Bu nedenle üçüncü durum olan **High-Z**, sistemin kontrol edilebilir ve güvenli çalışması için hayati öneme sahiptir.

ARM tabanlı mikrodenetleyicilerde (özellikle STM32 gibi Cortex-M tabanlı çiplerde), **çevresel birimlere (peripheral)** başlangıçta doğrudan clock (saat darbesi) sağlanmaz. Bu tasarım tercihi **güç verimliliğini artırmak** ve sistem başlangıcında gereksiz donanımların enerji tüketmesini önlemek amacıyla yapılmıştır.

---

### 📌 Neden Peripheral'lara Başlangıçta Clock Verilmez?

#### 🔋 1. **Güç Tüketimini Azaltmak**

* Tüm çevresel birimlerin saat sinyaliyle beslenmesi, hem gereksiz enerji tüketimine yol açar hem de sistemin ısınmasına neden olur.
* Sadece kullanılan modüllere clock verilerek enerji tüketimi minimuma indirilir (low-power design felsefesi).

#### 🛠️ 2. **Yazılımla Esneklik Sağlamak**

* Kullanıcı, hangi modülün ne zaman aktif olacağına yazılım üzerinden karar verir.
* Clock sinyali, RCC (Reset and Clock Control) register’ları üzerinden aktif hale getirilir.

---

### 🧩 Örnek: USART1’e Clock Vermek (STM32, CMSIS ile)

```c
RCC->APB2ENR |= RCC_APB2ENR_USART1EN;  // USART1 modülüne clock ver
```

* Bu işlem yapılmadan USART1 register'larına erişim başarısız olur (ya da belirsiz değerler okunur).
* Bu clock etkinleştirme işlemi genellikle çevresel birimlerin kullanılacağı yapılandırma fonksiyonlarının başında yapılır.

---

### 📎 Hangi Modüller için Clock Açılır?

* **GPIO** → `RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;`
* **USART** → `RCC->APB2ENR |= RCC_APB2ENR_USART1EN;`
* **ADC / DAC**
* **SPI, I2C, CAN**
* **TIM (Timer modülleri)**
* **DMA, CRC, RNG vs.**

---

### ⚠️ Clock Verilmediğinde Ne Olur?

* Register okuma/yazma çalışmaz.
* Peripheral “kapatılmış” gibi davranır.
* Hata ayıklama sırasında “read as 0” ya da “bus error” gibi durumlar görülebilir.

---

### 🧠 Sonuç

ARM mikrodenetleyicilerde çevre birimlerine clock sinyali **varsayılan olarak kapalıdır**. Her birimin aktif edilmesi yazılımla manuel olarak yapılmalıdır. Bu hem enerji verimliliği sağlar hem de yazılıma çevre birimleri üzerinde tam kontrol kazandırır.

`CMSIS` tabanlı bir STM32F4 projesinde `STM32F411CxE` mikrodenetleyicisini hedefleyerek, `stm32f4xx.h` başlık dosyasını doğru şekilde dahil etmek ve konfigürasyonu yapılandırmak için aşağıdaki yapı kullanılır:

---

### 📁 1. `stm32f4xx.h` ve CMSIS Kullanımı

`stm32f4xx.h` dosyası, hedefe özel başlıkları yöneten bir merkezi dosyadır. CMSIS ile uyumlu çalışır ve `#define` ile belirttiğiniz işlemciye göre ilgili alt başlığı çağırır.

### 📌 Örnek Başlangıç Kodu

```c
#include "stm32f4xx.h"
```

Bu satır sayesinde, CMSIS çekirdeği ve cihaz özel başlık dosyaları otomatik olarak içeri alınır.

---

### ⚙️ 2. Derleyiciye Hedef İşlemcinin Tanıtılması

CMSIS yapısında, doğru `device header` dosyasının çağrılması için işlemci modeli `#define` ile tanıtılır veya build ayarlarında tanımlanır.

#### 📌 Kod Üzerinden Tanımlamak:

```c
#define STM32F411xE
```

> `STM32F411CxE`, `STM32F411xE` tanımıyla kapsanır. Bu tanım sayesinde `stm32f411xe.h` dosyası içeri alınır.

#### 📌 Derleyici (Makefile, CMake, IDE) Üzerinden Tanımlamak:

Örneğin:

```make
-DSTM32F411xE
```

ya da

```cmake
add_definitions(-DSTM32F411xE)
```

IDE (Keil, STM32CubeIDE vs.) kullanıyorsan, genellikle **Device** seçiminden bu tanım otomatik yapılır.

---

### 📁 3. CMSIS Yapısı İçinde Dosya Akışı

```c
#include "stm32f4xx.h"
   ↳ #include "stm32f4xx_hal.h" (veya doğrudan .h dosyaları)
      ↳ #include "stm32f411xe.h"
         ↳ Register ve bellek tanımları buradan gelir.
```

---

### ✅ Sonuç Olarak

* CMSIS ile çalışırken `#include "stm32f4xx.h"` kullanılır.
* `STM32F411xE` makrosu, doğru başlık dosyasının (`stm32f411xe.h`) çekilmesini sağlar.
* Bu tanım kodda veya derleyici ayarlarında mutlaka yapılmalıdır.
* Bu yapı HAL, LL ya da doğrudan register bazlı programlama için de ortaktır.

`Run to main()` seçeneği debug oturumlarında, `main()` fonksiyonu çağrılana kadar olan tüm başlangıç işlemlerini **atlayarak** doğrudan `main()` içine kırılma (breakpoint) koyar. Ancak bu seçenek **kaldırıldığında**, ARM tabanlı mikrodenetleyici sistemlerde debug oturumu:

---

## 🧠 `Run to main()` Kaldırıldığında Görülenler

### 🔁 `Startup Code`'un İşlenmesi

`main()` fonksiyonu çağrılmadan önce yürütülen başlıca işlemler:

### 📌 1. **Reset Handler** (IVT 0x04)

* Sistem sıfırlandığında ilk çalışan fonksiyondur.
* Genellikle `Reset_Handler()` fonksiyonudur.

### 📌 2. **.data ve .bss Segmentlerinin Ayarlanması**

* `RAM` üzerinde `.data` segmenti flash'tan kopyalanır.
* `.bss` segmenti sıfırlanır.
* Bu işlemler `startup_xx.s` dosyasında veya `system_stm32f4xx.c` gibi CMSIS dosyalarında olur.

### 📌 3. **SystemInit()**

* Saat (clock) ayarları, PLL konfigürasyonu gibi ilk sistem yapılandırmaları yapılır.
* `SystemInit()` fonksiyonu `system_stm32f4xx.c` içinde tanımlıdır.
* Bu fonksiyon, `startup_xx.s` dosyasından doğrudan çağrılır.

### 📌 4. **Libc/CRT Initialization (Opsiyonel)**

* Eğer kullanılan toolchain destekliyorsa, `__libc_init_array()` fonksiyonu ile C++ constructor'ları veya static objeler başlatılır.

### 📌 5. **main() Fonksiyonunun Çağrılması**

* Bütün ilk hazırlıklardan sonra kullanıcı koduna geçilir: `main();`

---

## 🧪 CMSIS-Mimarili Akış (Örnek):

```assembly
Reset_Handler:
    bl  SystemInit         ; Clock ve sistem ayarları
    bl  __libc_init_array  ; C/C++ destekli init
    bl  main               ; Kullanıcı kodu
```

---

## 🎯 Debug Perspektifiyle Önemi

* `Run to main()` **kaldırıldığında**, yukarıdaki her aşamaya adım adım girilebilir.
* Özellikle `SystemInit()`, `clock`, `RAM`, `stack`, `heap` gibi alanlarda sorun varsa bu adımlar kritik debug bilgisi sağlar.
* Başlangıç hatalarını ve ilk donanım konfigürasyonundaki problemleri teşhis etmek için bu seçenek genellikle **devre dışı bırakılır.**

---

## 📌 Özet

| Aşama               | İşlevi                                |
| ------------------- | ------------------------------------- |
| `Reset_Handler`     | Başlangıç vektör fonksiyonu           |
| `.data`/.bss init   | RAM yapılandırması                    |
| `SystemInit()`      | Saat ve temel sistem konfigürasyonu   |
| `__libc_init_array` | C/C++ başlangıç işlemleri (opsiyonel) |
| `main()`            | Kullanıcı programı başlar             |

**Run to main() kapalıysa**, tüm bu aşamaları debug adımlarıyla izleyebilir, sistemin başlangıcındaki tüm donanımsal ve yazılımsal süreçleri görebilirsin. Bu, sistem seviyesinde derinlemesine inceleme için gereklidir.
