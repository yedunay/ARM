# Gömülü Sistemler - Bit-Banding, CMSIS ve Donanım Katmanları

## 📌 Bit-Banding Nedir? Neden Varlığı Gerekli?

**Bit-Banding**, ARM Cortex-M3 ve M4 gibi mimarilerde bulunan, RAM veya belirli periferik bellek alanlarındaki **tekil bitlere doğrudan, atomik** erişim sağlayan bir adresleme tekniğidir.

### Peki, neden register ile doğrudan işlemler yapabiliyorken Bit-Banding'e ihtiyaç duyulur?

1. **Atomiklik (Kesilmeye Karşı Dayanıklılık)**

   * Çoklu işleme (multithreading) ve kesme (interrupt) durumlarında bir registerın bitlerini okuma-değiştirme-yazma işlemi **kesintiye uğrarsa**, veri tutarsızlığı oluşur.
   * Bit-banding ile **tek satırda** bir biti 1 yapabilir veya silebilirsin.
   * Bu, **atomik (kesilemez)** ve **güvenli** bir işlemdir.

2. **Kod Basitliği ve Okunabilirlik**

   * Bazen bir biti set/clear etmek için mask ve shift işlemleri gerekir.
   * Bit-banding, bir adresi kullanarak bunu basit bir pointer ile doğrudan yapmayı sağlar.

3. **Donanımsal Hız**

   * Bit-banding mantığı donanım tarafından çözülür. Yani yazılım gecikmesi veya ek hesaplama gerekmez.

### Nerelerde Kullanılabilir?

* SRAM: 0x20000000 - 0x200FFFFF (1 MB)
* Peripheral: 0x40000000 - 0x400FFFFF (1 MB)
* Bit-band alias bölgeleri: 0x22000000'den başlar

### Neden Çok Kullanılmaz?

* Sadece belirli alanlarda geçerli.
* Kök adres + bit pozisyonu ile karmaşık adres hesaplaması ister.
* CMSIS gibi ücüncü katmanlar zaten okunabilir ve güvenli bit alanları sunar.
* Kod taşınabilirliğini azaltabilir.

---

## 🧩 CMSIS: Cortex Microcontroller Software Interface Standard

ARM tarafından standartlaştırılmış, çekirdeğe doğrudan erişimi sağlayan bir C yazılım katmanıdır.

### CMSIS'in Çekirdek İçeriği

* `core_cmX.h` → Çekirdek işlemci fonksiyonları (NVIC, SCB, SysTick)
* `cmsis_gcc.h`, `cmsis_armcc.h` → Derleyiciye özel inline assembly makrolar
* CMSIS-DSP → Optimize DSP fonksiyonları
* CMSIS-RTOS → RTOS soyutlama
* CMSIS-Driver → Peripheral soyutlama

### Register Erişimi Örneği

```c
#define USART1_SR (*(volatile uint32_t*)0x40011000)
```

Yerine CMSIS ile:

```c
USART1->SR |= USART_SR_TXE;
```

### Tanımlar

| CMSIS Makrosu | Anlamı              |
| ------------- | ------------------- |
| `__IO`        | volatile read/write |
| `__I`         | read only           |
| `__O`         | write only          |

---

## 🔧 Donanım-Soyutlama Katmanları

### 1. **Donanım (Hardware)**

Mikrodenetleyici çekirdeği, periferik birimler (GPIO, ADC), saat sistemi.

### 2. **ASM - Assembly Seviyesi**

`startup.s` gibi dosyalarda sistem başlatma kodları. Çok nadir de olsa inline assembly ile kritik şlemler.

### 3. **CMSIS**

ARM tarafından standartlaştırılan, taban yazılım soyutlaması.

### 4. **Vendor Peripheral Library**

* STM32 StdPeriph (eski)
* STM32 LL (Low Layer) → CMSIS'e yakın

```c
LL_GPIO_SetOutputPin(GPIOA, LL_GPIO_PIN_5);
```

### 5. **HAL Library**

* ST HAL: Yüksek seviyeli donanım soyutlama API'ı
* Daha okunabilir, ancak kaynak tüketimi fazla

```c
HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
```

### 6. **Driver / BSP (Board Support Package)**

* Kart özelleştirme için
* `bsp_uart.c`, `bsp_led.c` vs.

### 7. **Application Layer**

* Sensör okuma, veri işleme, ekran kontrolü gibi kullanıcı yazılımı

---

## 📐 Katmanlı Mimarinin Özeti

```
[ Uygulama ]
     ↓
[ HAL / BSP ]
     ↓
[ LL / StdPeriph ]
     ↓
[ CMSIS ]
     ↓
[ ASM / Donanım ]
```

Katmanlı yapı, yazılımın taşınabilirliğini, bakımını ve tekrar kullanılabilirliğini artırır. Bit-banding gibi özellikler donanım güvenliği sağlarken, CMSIS ve HAL gibi yapılar geliştiricinin şık ve sade kod yazmasını sağlar.
