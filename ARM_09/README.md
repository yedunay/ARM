**Object Kod ve Derleme Süreci**

Bir yazılımın mikrodenetleyici üzerinde çalışabilir hale gelmesi, derleme ve bağlama (linking) adımlarından geçer. Bu süreçte *object code* (nesne kodu), kaynak dosyalardan (C, assembly gibi) elde edilen, fakat henüz tam adres çözümlemeleri yapılmamış ara formattır.

### 🔧 .c, .s, .o Dosyaları ve Derleme Akışı

| Dosya Türü | İçerik                        | Açıklama                               |
| ---------- | ----------------------------- | -------------------------------------- |
| `.c`       | C diliyle yazılmış kaynak kod | Yüksek seviyeli programlama            |
| `.s`       | Assembly dili kaynak kodu     | Düşük seviyeli direktifler ve komutlar |
| `.o`       | Object code (nesne dosyası)   | Derlenmiş ama linklenmemiş ara kod     |

---

### ⚙️ Derleme Adımları

1. **Preprocess (Önişlem)**
   Makro, `#include`, `#define` gibi direktiflerin işlenmesi

2. **Compile (Derleme)**
   `.c` veya `.s` dosyaları, derleyici tarafından makine diline çevrilerek `.o` uzantılı *object* dosyaları elde edilir.
   Bu dosyalarda sembolik adresler bulunur, yani fonksiyonların ya da değişkenlerin gerçek RAM/Flash adresleri henüz belli değildir.

3. **Linking (Bağlama)**
   Linker, birden fazla `.o` dosyasını birleştirerek tüm sembolik adresleri gerçek adreslere çözer. Nihai ürün `.elf`, `.hex`, `.bin` gibi çalıştırılabilir dosyalardır.

---

### 📌 Object Code Neden Tek Başına Yeterli Değildir?

* Object dosyaları sadece kendi içindeki sembollerle çalışır.
* Farklı `.o` dosyaları birbirinin fonksiyonlarını çağırmak istediğinde bu sembollerin adresi bilinmelidir → bu işi **linker** yapar.
* Bellek haritası, kesme vektörleri ve donanım registerları gibi konular bu aşamada tam adresleriyle yerleştirilir.

---

### 🧠 Özet

* `.o` dosyası, makine diline çevrilmiş fakat adresleri çözülmemiş **ara koddur**.
* Nihai ürün, bu object dosyalarının linklenmesiyle oluşur.
* Bu yapı, kodun modülerliğini sağlar ve ayrı ayrı derleme–birleştirme imkânı verir. Özellikle gömülü sistemlerde bootloader + uygulama ayrımı gibi senaryolarda vazgeçilmezdir.

`.s` (assembly) dosyalarında yer alan `PUBWEAK` ifadesi, genellikle GNU Assembler (GAS) ile yazılmış ARM startup kodlarında kullanılan bir direktiftir. Anlamı **"public weak symbol"**, yani **zayıf ve dışarıya açık bir sembol** tanımlamaktır.

---

### 📌 `PUBWEAK` Ne İşe Yarar?

`PUBWEAK`, aynı sembol ismine sahip başka bir güçlü (strong) tanım yoksa kullanılmak üzere bir fonksiyon veya değişkenin **varsayılan tanımını** sağlar.

---

### ⚙️ Kullanım Mantığı

```assembly
    .weak NMI_Handler
    .set  NMI_Handler, Default_Handler
```

veya

```assembly
    .global  NMI_Handler
    .type    NMI_Handler, %function
    .weak    NMI_Handler
```

GNU Assembly'de bu yapı bazen `PUBWEAK` makrosuyla tanımlanır. Bazı toolchain’lerde:

```assembly
    PUBWEAK NMI_Handler
```

ifadesi, yukarıdaki `.weak` ve `.set` kombinasyonuyla aynı işi yapar: **Kullanıcı bu fonksiyonu override etmezse, default handler çalışsın**.

---

### 🧠 Neden Kullanılır?

1. **Kullanıcı özelleştirme yapabilsin**:
   Örneğin `USART1_IRQHandler` fonksiyonu uygulayıcı tarafından C dosyasında tanımlanmışsa bu, `PUBWEAK` ile gelen default tanımı ezer (override eder).

2. **Varsayılan handler tanımı sunmak için**:
   Tüm kesmeler bir `Default_Handler` fonksiyonuna yönlendirilerek boş veya hata verici şekilde çalıştırılabilir.

---

### 🔒 Güçlü vs Zayıf Tanım

| Sembol Tanımı                                        | Öncelik                    |
| ---------------------------------------------------- | -------------------------- |
| Strong (güçlü) tanım – `void NMI_Handler(void)` gibi | ✅ Öncelikli                |
| Weak (zayıf) tanım – `PUBWEAK NMI_Handler`           | ❌ Sadece başka tanım yoksa |

---

### 🧩 Uygulama

* STM32 startup.s içinde tüm IRQ’lar genellikle `PUBWEAK` ile tanımlanır.
* Kullanıcı IRQ handler’ını `.c` dosyasında tanımlamazsa, bu `PUBWEAK` olan varsayılan `Default_Handler` çağrılır.

---

**Sonuç:** `PUBWEAK`, assembly dosyasında **override edilebilir varsayılan fonksiyonlar** tanımlamak için kullanılır. Bu sayede sistemde eksik handler’lar olsa bile yazılım çökmek yerine kontrollü şekilde çalışabilir.

ARM mimarisinde, özellikle **Cortex-M** serisi gibi RISC tabanlı işlemcilerde, **tüm veri işlemleri yalnızca registerlar arasında yapılır**, belleğe erişim yalnızca `LDR` (load) ve `STR` (store) komutları ile sağlanır. Bu yaklaşım **Load/Store Mimarisi** olarak adlandırılır ve ARM'ın temel tasarım felsefesidir.

---

## 📌 Load/Store Mantığı ve 32-bit Register Üzerinden İşlem

### 🧱 Load/Store Modeli

* Bellekten **veri okumak için `LDR`** komutu kullanılır.
* Belleğe **veri yazmak için `STR`** komutu kullanılır.
* Aritmetik, mantıksal, dallanma gibi işlemler yalnızca **registerlar arasında** yapılır.
* Bellekten doğrudan işlem yapmak **yasaktır**.

---

### 🔄 Neden `MOV` Kullanılamaz?

* `MOV` komutu **sabit değeri** ya da bir register içeriğini başka bir register'a kopyalar.
* Ancak 32-bit’lik büyük sabit değerler (`0x20001000` gibi) doğrudan `MOV` ile kopyalanamaz çünkü ARM mimarisinde bir komutun **kod uzunluğu 4 byte'tır (32-bit)** ve bu alana sadece sınırlı büyüklükte sabitler sığar.
* Bu yüzden büyük sabitler için **önce belleğe bu sabit adres yazılır**, sonra `LDR` ile oradan alınır.

---

### 🧠 İşlem Akışı Örneği

```assembly
    LDR R0, =0x20001000   ; R0 ← 0x20001000 (adres sabiti yüklenir)
    BLX R0                ; R0'daki adrese dallan (fonksiyon çağırma)
```

> Aslında `LDR R0, =0x20001000` ifadesi assembler tarafından, `PC` (Program Counter) yakınında bir **literal havuzuna** sabiti yerleştirip onu `R0`'a yükleyecek şekilde çevirilir:

```assembly
    LDR R0, [PC, #offset]     ; offset uzaklığındaki sabiti al
    ...
    ; offset sonundaki sabit:
    .word 0x20001000
```

---

### 📌 `BLX` ile Fonksiyon Çağırma

* `BLX R0` → R0’daki adrese **branch with link** yapılır, yani bir fonksiyon çağrılmış olur.
* `LR` (Link Register) otomatik olarak dönüş adresini tutar.

---

### ✅ Load/Store Mimarisi Avantajları

| Avantaj          | Açıklama                                                         |
| ---------------- | ---------------------------------------------------------------- |
| Pipeline uyumlu  | Sabit uzunluktaki komutlar → hızlı çalışır                       |
| Donanım sadeliği | Bellek işlemleri `LDR/STR`, işlem komutları ayrı                 |
| Performans       | Belleğe doğrudan işlem yapılmaz, bu da daha hızlı yürütme sağlar |

---

### 🚫 Bellekten Doğrudan İşlem Yapılamaz

Yanlış kullanım örneği:

```assembly
ADD R0, [R1], #4  ; ❌ Geçersiz, çünkü [R1] (bellek adresi) doğrudan işlemde kullanılamaz
```

Doğrusu:

```assembly
LDR R2, [R1]      ; R2 ← [R1] (önce bellekten oku)
ADD R0, R2, #4    ; R0 ← R2 + 4 (registerlar arasında işlem)
```

---

**Sonuç:**
ARM assembly'de `MOV` sabitleri kopyalamada sınırlıdır, büyük değerleri `LDR` üzerinden alırız. İşlemler daima registerlar arasında yürütülür; belleğe erişim yalnızca `LDR/STR` komutları ile olur. `BLX` gibi komutlarla register içindeki adrese atlama yapılarak fonksiyon çağrısı gerçekleştirilir. Bu yaklaşım Load/Store mimarisinin özüdür.

**PLL (Phase Locked Loop – Faz Kilitlemeli Döngü)** yapısı, ARM mikrodenetleyicilerde sistem saatini yüksek frekanslara çıkarabilmek ve çeşitli modüllere uygun hızlar sağlayabilmek için kullanılır. Bu sistem üç temel parametreyle çalışır: **M**, **N** ve **P**. Bunların birlikte nasıl çalıştığını şu şekilde açıklayabiliriz:

---

### 1. **M – Giriş Frekansı Bölücü (Pre-divider)**

M harfi, harici saat kaynağından (HSE veya HSI) gelen sinyalin ilk bölme katsayısını temsil eder.
PLL’nin kararlı çalışabilmesi için PLL giriş frekansı (VCO input) genellikle 1–2 MHz civarında tutulur. Bu yüzden önce saat sinyali M’ye bölünerek bu aralığa çekilir.

---

### 2. **N – Çarpan (Multiplier)**

Bu parametre, M ile bölünmüş düşük frekanslı sinyali çoğaltarak iç PLL osilatörünü besleyen yüksek frekanslı bir sinyal üretir. Bu sinyal **VCO çıkış frekansı**dır.
Bu değer ne kadar büyük olursa, çıkış saat frekansı da o kadar yüksek olur. Ancak mikrodenetleyicinin maksimum frekans sınırları aşılmamalıdır.

---

### 3. **P – Sistem Saat Bölücüsü (Post-divider)**

Yüksek frekansta üretilmiş VCO çıkışı, sistemin çalışacağı temel saat frekansına indirgenmek üzere P katsayısına bölünür. Bu işlem sonunda elde edilen sinyal, mikrodenetleyicinin **SYSCLK (ana sistem saati)** olarak kullanılır.

---

### Saat Yolu Genel Akışı

**Harici Saat Kaynağı (HSE veya HSI)**
→ M ile bölünür
→ N ile çarpılır
→ P ile bölünür
→ **SYSCLK** olarak çıkış verilir.

Bu yapı sayesinde, sabit bir harici kristal ile çok geniş frekans aralıklarında sistem saat frekansı üretilebilir. Bu da esnek güç ve performans kontrolü sağlar.

---

Bu yapıdaki parametrelerin seçimi dikkatle yapılmalıdır. Çünkü:

* VCO giriş frekansı 1–2 MHz aralığında tutulmalı
* VCO çıkışı maksimum sınırlarda olmamalı
* SYSCLK, mikrodenetleyicinin limitlerini aşmamalıdır

Bu kurallara uyulmazsa sistem kararsız çalışabilir veya hiç çalışmayabilir.
