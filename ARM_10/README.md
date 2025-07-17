
## 🧠 Multitasking Türleri ve Temelleri

**Multitasking (Çok Görevli Çalışma)**, mikrodenetleyicilerin veya işletim sistemlerinin işlemciyi çok sayıda göreve paylaştırmasını sağlar. İki ana yaklaşım vardır:

---

### 1️⃣ Cooperative Multitasking (İşbirlikçi Çok Görevlilik)

* Görevler **kontrolü kendi istekleriyle bırakır**.
* Bir görev işlemciyi bırakmazsa diğerleri çalışamaz.
* Küçük gömülü uygulamalarda tercih edilir.
* Basit yapısı sayesinde düşük kaynak gerektirir ama stabilite riski taşır.

**Örnek kullanım:**
Arduino loop döngüsü, temel STM32 uygulamaları.

**Özellikleri:**
✅ Az bellek tüketir
✅ Zaman kontrolü kullanıcıdadır
❌ Uzun süren görevler sistemi kitler

---

### 2️⃣ Preemptive Multitasking (Öncelikli Çok Görevlilik)

* İşletim sistemi görevleri **otomatik olarak keser** ve sırayla yürütür.
* Donanım zamanlayıcı ve kesmelerle çalışır.
* FreeRTOS gibi RTOS’larda yaygın kullanılır.

**Özellikleri:**
✅ Gerçek zamanlı garantiler sunar
✅ Görevler birbirini engelleyemez
❌ Daha karmaşık yapı ve kritik bölgelerde senkronizasyon ihtiyacı

---

## ⚙️ FSM (Finite State Machine) – Sonlu Durum Makineleri

FSM, bir görevin **durum tabanlı yönetilmesini** sağlar. Sistem her an bir durumda olur ve gelen olaylara göre başka bir duruma geçer.

**Avantajları:**

* Görev akışını düzenler.
* Kodun okunabilirliğini artırır.
* Cooperative multitasking’te her görev FSM mantığında ilerletilir.

**Örnek:**

```c
switch(state) {
  case WAITING:
    if(event == READY) state = PROCESSING;
    break;
  case PROCESSING:
    if(done) state = DONE;
    break;
}
```

---

## 📡 Prosesler Arası Haberleşme

Birden fazla görev varsa, görevlerin birbiriyle **veri paylaşması veya sinyal göndermesi gerekir**. Bu mekanizmaya IPC (Inter Process Communication) denir.

**Yöntemler:**

* **Bayraklar:** Basit durum bildirme (ör. veri geldi).
* **Queue:** Veriyi sıraya koyarak iletme.
* **Semaphore:** Kaynak erişimini kontrol etme.
* **Shared Variable + Mutex:** Ortak değişken kullanımında kilit mekanizması.

---

## ⏳ Cooperative Multitasking Kurmak İçin Gerekli Bileşenler

1️⃣ **Saat Fonksiyonu:**
Görevlerin ne zaman çalışacağını belirler. Örn: `HAL_GetTick()`

2️⃣ **FSM:**
Her görevi durum tabanlı ilerletir.

3️⃣ **Haberleşme Mekanizması:**
Görevlerin birbirini tetiklemesini sağlar.

**Özet Örnek:**

```c
if(HAL_GetTick() - last_time >= 500) {
   fsm_update();
   last_time = HAL_GetTick();
}

void fsm_update() {
   switch(state) {
       case IDLE:
         if(flag) state = RUNNING;
         break;
   }
}
```

---

## 📊 Kısa Karşılaştırma Tablosu

| Özellik           | Cooperative           | Preemptive                     |
| ----------------- | --------------------- | ------------------------------ |
| Kontrol           | Görev bırakır         | OS/Zamanlayıcı keser           |
| Zamanlama         | Kullanıcı kontrolünde | Otomatik kesme ile             |
| Gerçek zamanlılık | Zor                   | Kolay                          |
| Stabilite         | Düşük                 | Yüksek                         |
| Kullanım Alanı    | Basit sistemler       | Karmaşık/Endüstriyel sistemler |

---

## 🎯 Sonuç

* Küçük, basit, RTOS olmayan projelerde **cooperative multitasking + FSM + zaman fonksiyonu** idealdir.
* Daha yüksek gerçek zamanlılık ve güvenilirlik isteyen sistemlerde **preemptive multitasking** tercih edilir.

