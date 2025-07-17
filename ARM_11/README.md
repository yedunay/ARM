# ARM11 - Ders Özeti ve Trick Points

Bu ders, gömülü yazılım geliştirmede kullanılan katmanlı yapı prensibini, donanım ve yazılım arasındaki ilişkiyi düzenleyen temel mantıkları ve ARM tabanlı sistemlerde başvurulan temel teknikleri ele almaktadır.  
Ders boyunca özellikle **taşınabilir yazılım geliştirme**, **sistemin kararlı çalışmasını sağlama** ve **donanımdan bağımsız kod yazma** prensipleri detaylıca açıklanmıştır.

---

## 🏛 Yazılım Katmanları (Software Layers)

Yazılım projelerinde **katmanlı mimari** kullanmak temel bir prensiptir.  
Bu yapı, geliştirilmiş yazılımın farklı donanımlar üzerinde tekrar kullanılmasına olanak sağlar ve aynı zamanda kodun okunabilirliğini ve sürdürülebilirliğini artırır.

### 📚 Katmanlar ve Görevleri

| Katman            | Örnek Fonksiyon   | Görevi                              |
|-------------------|-------------------|-------------------------------------|
| **Application**   | `printf()`, `main.c` | Uygulama seviyesinde işlevler sağlar. |
| **Upper Driver**  | `LCD_PutChar()`     | Özel cihaz sürücüsü (LCD vb.)        |
| **Low Driver (HAL)` | `IO_Write()`      | Donanıma erişim sağlar.              |
| **Peripheral Library** | `GPIO_Write()` | Çevresel donanım kütüphanesi         |
| **CMSIS**         | `GPIOx->ODR = ...`  | İşlemci çekirdeği kayıtlarına erişim  |

### Neden Katmanlı Mimari Kullanılır?

- **Taşınabilirlik:** Donanım değişse bile aynı kod kolayca uyarlanabilir.
- **Modülerlik:** Parçalar birbirinden bağımsız geliştirilir.
- **Okunabilirlik:** Kod düzenli bölümlere ayrılır, yönetimi kolaylaşır.
- **Kararlılık:** Değişikliklerin etkisi sınırlandırılır, sistemin geneline zarar vermez.
- **Test ve Hata Ayıklama Kolaylığı:** Sorunun hangi katmanda olduğunu bulmak daha kolaydır.

---

## 🔧 Weak Fonksiyonlar

**Amaç:** Aynı isimli bir fonksiyon yeniden tanımlandığında, son tanım geçerli olur.

Bu yapı özellikle **kesme (interrupt)** fonksiyonlarında kullanılır. CMSIS kütüphanesinde `__WEAK` tanımı yer almaktadır.  
İstenirse kullanıcı kendi fonksiyonuyla bu fonksiyonu ezebilir.

---

## 🔌 Donanım Davranışları (CMOS, TTL, ST)

### MOSFET / CMOS Temelleri

- **Rds (Drain-Source Direnci)** genellikle yüksek tutulmalıdır. Eğer düşük olursa, drain bacağından aşırı akım geçebilir ve bu durum MOSFET’in ya da çevresindeki devre elemanlarının zarar görmesine, hatta yanmasına yol açabilir.
- **CMOS çıkışı:** 1 - 0 - High-Z (bağlantısız, kararsız çıkış durumu)
- High-Z (yüksek empedans) durumunda çıkış kararsız kalır → Pull-up veya Pull-down direnç kullanılması gerekir.

### Tristate Buffer

- Çıkış 1, 0 ya da devreden kopuk (High-Z) olabilir.
- Ortak veri hatlarında kullanılır.

---

### TTL vs. ST

| Özellik | TTL                        | ST (Schmidt-Trigger)                   |
|---------|-----------------------------|-----------------------------------------|
| Avantaj | Basit yapı, düşük akım, hızlı | Gürültü bağışıklığı (histerezis etkisi) |
| Kullanım | Eski sistemlerde hâlâ kullanılır | Modern devrelerde standart hâline gelmiştir |

> **Not:** ST, giriş sinyal dalgalanmalarına karşı çok daha dayanıklıdır.

---

## 🔤 Kısaltmalar Açıklamalı

| Kısaltma | Anlamı                                                   |
|----------|-----------------------------------------------------------|
| **CMSIS** | Cortex Microcontroller Software Interface Standard        |
| **HAL**   | Hardware Abstraction Layer (Donanım Soyutlama Katmanı)    |
| **TTL**   | Transistor-Transistor Logic                               |
| **ST**    | Schmidt-Trigger (Histerezisli Lojik)                      |
| **High-Z**| High Impedance (Yüksek Empedans, bağlantısız çıkış)       |
| **FSM**   | Finite State Machine (Sonlu Durum Makinesi)               |

---

## 🎯 Dersin Amacı

Bu dersin kazandırdığı temel kazanımlar şunlardır:

- Donanım değişse dahi yazılım kolayca yeniden uyarlanabilir.
- Kod yapısal olarak düzenli olduğu için yönetimi ve bakımı çok daha kolaydır.
- Elektronik donanım temelleri kavranır ve neden belirli devre elemanlarının kullanıldığı anlaşılır.

> Bu prensipler, ARM tabanlı sistemlerde **profesyonel yazılım geliştirme yaklaşımının temelini oluşturur.**
