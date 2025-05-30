# Gömülü Sistemler – Hafta 1: Temel Kavramlar ve ARM Mimarisine Giriş

Bu derste, gömülü sistemlerin ne olduğu, neden önemli oldukları ve bu sistemlerde yaygın olarak kullanılan ARM mimarisinin temelleri ele alınmıştır. Ayrıca mikrodenetleyici-mikroişlemci ayrımı gibi mühendislikte sık karşılaşılan temel kavramlar sade bir dille açıklanmıştır.

---

## 📌 Gömülü Sistem Nedir?

Gömülü sistem, belirli bir görevi yerine getirmek için tasarlanmış, yazılım ve donanımın birlikte çalıştığı özel amaçlı bir bilgisayar sistemidir. Çamaşır makinesinden otomobile, tıbbi cihazlardan akıllı saatlere kadar her yerde bulunurlar.

---

## ⚙️ Mikroişlemci vs Mikrodenetleyici

- **Mikroişlemci (CPU)** sadece işlem birimidir, çevresel birimlere ve belleğe dışarıdan ihtiyaç duyar.
- **Mikrodenetleyici (MCU)** ise CPU, RAM, Flash bellek ve çevresel birimleri tek bir yonga üzerinde barındırır. Gömülü sistemler için optimize edilmiştir.

---

## 🧠 ARM Mimarisi Nedir?

ARM, düşük güç tüketimi, yüksek verimlilik ve modüler yapı sunan bir RISC (Reduced Instruction Set Computing) mimarisidir. Gömülü sistemlerde ve mobil cihazlarda yaygın olarak tercih edilir. Cortex-M serisi, gerçek zamanlı uygulamalar için tasarlanmıştır.

---

## 🧩 Harvard vs Von Neumann Mimarisi

- **Von Neumann** mimarisinde veri ve komutlar aynı veri yolunu paylaşır.
- **Harvard** mimarisinde ise veri ve komutlar için ayrı yollar vardır. Bu, aynı anda veri erişimi ve komut çalıştırmaya imkân tanır. ARM Cortex-M çekirdekleri genellikle **Modified Harvard** mimarisi kullanır.

---

## 🧰 Geliştirme Ortamı ve Araçlar

Bir gömülü sistem geliştirmek için kullanılan temel araçlar:

- **IDE**: Geliştirme ortamı (örneğin STM32CubeIDE, Keil uVision)
- **Toolchain**: Derleyici + Bağlayıcı + Yükleyici (arm-none-eabi-gcc gibi)
- **Debugger**: Kodu adım adım izlemeye yarar (ST-Link, J-Link)
- **Programlayıcı**: Kodu mikrodenetleyiciye yükler

---

## 🎯 ARM Neden Tercih Ediliyor?

- Düşük güç tüketimi → batarya ile çalışan cihazlar için ideal
- Modüler mimari → üreticiye göre yapılandırılabilir
- Geniş ekosistem → STM32, NXP, TI gibi firmaların desteği
- Gelişmiş hata yönetimi, kesme kontrolü ve güç yönetim modları

---
