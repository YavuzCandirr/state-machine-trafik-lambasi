# STM32 Kesme (Interrupt) Tabanlı Yaya Geçitli Trafik Lambası

Bu proje, STM32 mikrodenetleyicileri üzerinde tamamen **Kesme (Interrupt)** ve **Durum Makinesi (State Machine)** mimarisi kullanılarak geliştirilmiş, engellemesiz (non-blocking) bir yaya butonlu trafik lambası sistemidir.

Geleneksel projelerde sıklıkla görülen ve işlemciyi kilitleyen `HAL_Delay()` fonksiyonları veya `while` içinde sürekli buton yoklama (Polling) işlemi **kullanılmamıştır**. İşlemci ana döngüde (`while(1)`) tamamen boşta beklerken, tüm sistem donanımsal kesmeler üzerinden asenkron olarak yönetilmektedir.

## 🌟 Öne Çıkan Mühendislik Yaklaşımları

1. **Non-Blocking (Engellemesiz) Mimari:** Sistemde hiçbir bekleme (delay) fonksiyonu yoktur. `while(1)` döngüsü boştur.
2. **State Machine (Durum Makinesi):** Trafik lambasının fazları `enum` yapısı ile modellendi (Kırmızı, Sarı, Yeşil, Yaya Yeşil, Yaya Kırmızı). Sistem her an hangi durumda olduğunu bilir ve ona göre tepki verir.
3. **EXTI (Dış Kesme) ile Bayrak (Flag) Mantığı:** Yaya butona bastığı an sistem ışıkları hemen değiştirmez. Buton kesmesi sadece `button1 = 1;` bayrağını kaldırır. Bu, asenkron girdilerin senkron bir sisteme nasıl entegre edileceğinin klasik bir örneğidir.
4. **Timer Heartbeat (Zamanlayıcı Kalp Atışı):** TIM2 donanımı sistemin kalbi gibi çalışır. Belirli periyotlarla kesme üreterek zamanı (`saniyesayaci`) sayar, bayrakları kontrol eder ve duruma göre State Machine'i bir sonraki faza geçirir.
5. **Modüler Uyarı Efektleri:** Modüler aritmetik (`saniyesayaci % 2 == 0`) kullanılarak, ekstra bir zamanlayıcıya ihtiyaç duymadan yaya kırmızı ışığında flaşör (yanıp sönme) efekti oluşturulmuştur.

## ⚙️ Sistemin Çalışma Senaryosu (Fazlar)

* **Kırmızı (Araçlara Kırmızı):** Araçlar bekler. Eğer yaya butona basmışsa ve asgari süre dolmuşsa hemen "Yaya Yeşil" fazına geçilir. Butona basılmamışsa normal süresini bekleyip araçlar için "Yeşil" faza geçer.
* **Yeşil (Araçlara Yeşil):** Araçlar geçer. Belirlenen süre dolduğunda güvenli geçiş için "Sarı" faza dönülür.
* **Sarı:** Geçiş hazırlığı. Süre dolduğunda tekrar "Kırmızı" faza dönülür.
* **Yaya Yeşil:** Yaya butonuna basıldığında aktif olan özel fazdır. Yayalara yeşil yanarken araçlara kırmızı yanmaya devam eder.
* **Yaya Kırmızı (Uyarı):** Yaya geçiş süresi biterken yaya kırmızı ışığı **flaşör gibi yanıp sönerek** yayaları uyarır, ardından sistem normal araç kırmızısına döner.

## 📌 Pin Konfigürasyonu (GPIO Ayarları)

* **Araç Işıkları (Çıkış):** `red_led_Pin`, `yellow_led_Pin`, `green_led_Pin` (Port E)
* **Yaya Işıkları (Çıkış):** `y_red_led_Pin`, `y_green_led_Pin` (Port E)
* **Yaya Butonu (EXTI Giriş):** `button_Pin` (Yükselen Kenar Dış Kesmesi - RISING EXTI)
* **Zamanlayıcı (Dahili):** `TIM2` (Kalp atışı periyodu için Prescaler ve Period ayarlı)



### 👨‍💻 Geliştirici / Developer
<p align="center">
  <b>Yavuz ÇANDIR</b>
  <br>
  <a href="https://github.com/YavuzCandirr" target="_blank">
    <img src="https://img.shields.io/badge/GitHub-YavuzCandirr-black?logo=github" alt="GitHub">
  </a>
</p>
