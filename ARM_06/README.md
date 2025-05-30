
## 📌 Interrupt Vector Table (IVT)

---

### 🧠 Tanım

Interrupt Vector Table (Kesme Vektör Tablosu), ARM Cortex-M mimarilerinde her bir kesmenin (interrupt) ya da özel durumun (exception) hangi fonksiyona yönlendirilmesi gerektiğini gösteren bir adres listesidir. Bu yapı, sistemin açılışında ilk başvurulan bellek alanıdır ve işlemciye ne yapması gerektiğini bildirir.

---

### 📍 Yerleşim ve Bellek Adresi

IVT, genellikle `0x00000000` adresinde veya alternatif olarak `0x08000000` (Flash başlangıcı) adresinde bulunur. Sistem açıldığında işlemci:

* `SP` (Stack Pointer) değerini IVT’nin ilk satırından (offset 0x00),
* `PC` (Program Counter) yani başlangıç fonksiyonu adresini ikinci satırdan (offset 0x04) alır.

---

### 🧱 Temel IVT Formatı (Cortex-M)

| Offset        | Anlamı                     | Açıklama                                    |
| ------------- | -------------------------- | ------------------------------------------- |
| `0x0000_0000` | Initial Stack Pointer (SP) | RAM’in en üst adresi                        |
| `0x0000_0004` | Reset Handler              | Sistem açıldığında ilk çalışacak fonksiyon  |
| `0x0000_0008` | NMI Handler                | Non-Maskable Interrupt                      |
| `0x0000_000C` | HardFault Handler          | Kritik sistem hatası                        |
| `...`         | ...                        | Diğer sistem ve çevre birimi interrupt’ları |

---

### 📎 Vektör Tablosunun Tanımı (C örneği)

```c
__attribute__ ((section(".isr_vector")))
const void* vector_table[] = {
    (void*) 0x20020000,      // Initial SP value
    Reset_Handler,           // Reset Handler
    NMI_Handler,             // NMI
    HardFault_Handler,       // HardFault
    ...
    USART1_IRQHandler,       // Vendor interrupt
    TIM2_IRQHandler,         // Timer interrupt
};
```

---

### 🧩 Vector Table Offset Register (VTOR)

Bazı ARM çekirdeklerinde (örneğin Cortex-M3/M4), IVT’nin yeri değiştirilebilir. Bu durumda:

* `SCB->VTOR` register'ı ile IVT'nin yeri değiştirilir.
* Bu sayede bir bootloader farklı bir vektör tablosu tanımlayıp uygulamaya geçiş yapabilir.

---

### 🔐 Güvenlik ve Önemi

IVT, sistemin açılış kontrolünü, kesme yönetimini ve hata kontrolünü sağlayan **kritik** bir yapıdır. Bu tablodaki yanlış bir adresleme sistemin tamamen kilitlenmesine yol açabilir. Her vektörün doğru tanımlanması, boşta kalan interrupt’ların default handler’lara yönlendirilmesi gerekir.

---

## 📌 ARM İşlemcilerde Little Endian ve Big Endian Farkı

---

### 🧠 Endianness Nedir?

Birden fazla byte’lık verilerin belleğe nasıl yerleştirileceğini belirleyen kurala **endianness** denir.

* **Little Endian:** En düşük anlamlı byte (LSB) düşük adrese yazılır.
* **Big Endian:** En yüksek anlamlı byte (MSB) düşük adrese yazılır.

---

### 🧪 Örnek

```c
uint32_t val = 0x12345678;
```

**Little Endian Bellek Temsili:**

```
Adres:     0x00   0x01   0x02   0x03
İçerik:    0x78   0x56   0x34   0x12
```

**Big Endian Bellek Temsili:**

```
Adres:     0x00   0x01   0x02   0x03
İçerik:    0x12   0x34   0x56   0x78
```

---

### ✅ Avantajlar

| Özellik        | Little Endian                       | Big Endian                      |
| -------------- | ----------------------------------- | ------------------------------- |
| ARM İşlemciler | Evet (varsayılan olarak)            | Hayır (bazı özel sistemlerde)   |
| Veri Doğrulama | LSB üzerinden hızlı kontrol yapılır | MSB'yi doğrudan okumak uygundur |
| Uyumluluk      | x86, ARM gibi sistemlerde yaygın    | Eski ağ sistemlerinde yaygın    |

---

## 📌 ARM Mimarilerinde Kesmeler, Reset Sonrası Durumu ve PIC ile Farkı

---

### 🔁 Reset Sonrası Kesmelerin Durumu

ARM Cortex-M mimarisinde sistem açıldığında:

* Sadece **Reset kesmesi aktiftir**.
* Diğer tüm kesme kaynakları pasiftir.
* Yazılım tarafından **NVIC** ile aktif hale getirilmelidir.

---

### ⚙️ NVIC ve ARM'deki Kesme Sistemi

NVIC (Nested Vector Interrupt Controller):

* 240’a kadar kesmeyi destekler.
* Öncelik seviyeleri atanabilir.
* Donanımda otomatik vektörleme sağlar.

---

### 🆚 ARM NVIC vs Klasik PIC

| Özellik                  | ARM NVIC           | PIC (x86 gibi)    |
| ------------------------ | ------------------ | ----------------- |
| Donanımda vektörleme     | ✅ Var              | ❌ Yazılımla       |
| Öncelik kontrolü         | ✅ Her kesmeye özel | ⚠️ Sabit          |
| Nested interrupt desteği | ✅ Donanımsal       | ❌ Yazılım gerekir |
| Performans               | ✅ Yüksek           | ❌ Göreceli yavaş  |

---

## 📌 `inline` Fonksiyon Nedir, Avantajları ve Farkı

---

### Tanım

`inline`, fonksiyonun çağrıldığı yere gömülmesini sağlayarak `call/return` maliyetini ortadan kaldırır.

---

### Avantajları

* Fonksiyon çağırma maliyeti yoktur.
* Küçük fonksiyonlarda performans artışı sağlar.

---

### Ne Zaman Olmaz?

* Döngü, `switch`, recursive varsa.
* Fonksiyon karmaşıksa.
* Derleyici kararıyla göz ardı edilebilir.

---

## 📌 Bit-Banding Nedir ve Neden Az Kullanılır?

---

### Tanım

Bit-banding, RAM veya çevre birimlerinde tek bir bit’e doğrudan erişim sağlayan özel adresleme tekniğidir.

---

### Avantajları

* Atomik erişim sağlar.
* Donanım düzeyinde güvenlidir.

---

### Neden Az Kullanılır?

* Sadece Cortex-M3/M4 destekler.
* Sadece belirli bellek bölgeleri için geçerlidir.
* Karmaşık adres hesaplama gerektirir.
* CMSIS bit alanları daha okunaklıdır.

---

## 📌 Load-Store Mimarisi

---

### Tanım

ARM gibi RISC mimarilerinde tüm işlemler registerlar arasında yapılır, belleğe erişim yalnızca `LDR/STR` ile olur.

---

### Avantajları

* Pipeline’a uygundur.
* Basit, sabit uzunlukta komut seti.
* Donanımı sadeleştirir.

---

### Dezavantajı

* Kod boyutu artabilir.
* Karmaşık işlemler daha çok komut ister.

---

## 📌 `volatile` ve RAM, Peripherial, Flash Kullanımı

---

### `volatile` Tanımı

`volatile`, derleyiciye değişkenin dış etkenlerle değişebileceğini bildirir.

---

### Kullanım Alanları

| Bellek Alanı      | `volatile` Gerekli mi? | Sebep                      |
| ----------------- | ---------------------- | -------------------------- |
| RAM (normal)      | ❌                      | Sabit veri                 |
| RAM (ISR flag)    | ✅                      | Kesme değiştirebilir       |
| Peripheral (MMIO) | ✅                      | Donanım tarafından değişir |
| Flash (const)     | ❌                      | Hiç değişmez               |

---

## 📌 ARM'de 4 Byte Hizalı Erişim Zorunluluğu

---

### Neden Gerekli?

* Bellek 32-bit hizalıdır.
* Hizasız erişim: performans kaybı veya `BusFault`.

---

### Hatalı Erişim Örneği

```c
uint8_t* p = (uint8_t*)0x20000001;
uint32_t val = *(uint32_t*)p;  // hizasız
```

---

### Doğru Erişim

```c
uint32_t* aligned = (uint32_t*)0x20000000;
uint32_t val = *aligned;
```

---

## 📌 ARM Sistemlerde Erişim Standardı: CMSIS

---

### CMSIS Bileşenleri

* CMSIS-Core: temel işlemci fonksiyonları
* CMSIS-DSP: optimize edilmiş matematik
* CMSIS-RTOS: RTOS soyutlama
* CMSIS-Driver: çevre birimi sürücüler
* CMSIS-SVD: register tanımı için XML formatı

---

### Register Erişimi

```c
#define USART1_SR (*(volatile uint32_t*)0x40011000)
```

veya CMSIS:

```c
USART1->SR |= USART_SR_TXE;
```

---

### Tanımlar

```c
__IO → volatile  
__I  → readonly  
__O  → writeonly  
```

---
