# BT Technology — Devam Notu

> Bu dosya, herhangi bir yeni sohbette "nerede kaldık" sorusunun cevabıdır.
> Yeni sohbete girince Claude'a: **"Repodaki DEVAM_NOTU.md'yi oku, kaldığımız yerden devam et"** de.

Son güncelleme: 2026-11-14 · Güncel sürüm: **v48-P**

---

## Proje nedir?
ESP32-S3 tabanlı metal dedektör firmware'i ("BT Technology"). ST7735 160×128 TFT ekran,
döner encoder (tekerlek) + buton ile menü, telefon web arayüzü (WiFi AP, 192.168.4.1).

## Firmware dosyaları (`firmware/`)
- **BT_Technology/** — NORMAL sürüm (aktif buzzer, yerel OTA). Ana/varsayılan.
- **BT_Technology_PASIF/** — PASİF buzzer sürümü. **Ses çalışması burada.** Güncel odak.
- **BT_Technology_UZAKTAN/** — WiFi kur + internetten self-update sürümü (şu an v42, geride).

## Donanım pinleri
`buzzerPin=7, sensorPin=1, pilPin=2, frekansCikisPin=3`. Encoder: pinCLK/pinDT/pinSW.

## Buzzer durumu (ÖNEMLİ)
- Eldeki buzzer: **5V aktif** (arkası kapalı) → sabit ~2.4 kHz, perdesi değişmez.
- İstenen sesler (1120 Hz ton, melodiler) **pasif buzzer** gerektirir. Pasif buzzer takılacak.
- PASİF firmware `tone()`/`noTone()` kullanır; aktif firmware `digitalWrite` ile aç/kapa.

## Ses kararı (kullanıcı onayladı)
- **İyonik ses = 3 kademe, sabit 1120 Hz:**
  - Kademe 1 (zayıf, eşik–1.5V): 100ms aç / 100ms kapa
  - Kademe 2 (orta, 1.5–3.0V): 50ms aç / 50ms kapa
  - Kademe 3 (güçlü, >3.0V): kesintisiz ton
- Kullanıcı tonu telefonda deneyerek buldu: **perde 1120 Hz, tınlama 2** ("Dostum, ton bu").

## v48-P'de eklenen (son iş)
- **Cihaz üzerinde ton ayarı:** İyonik modunda tekerlek tonu değiştirir
  (sağ = +40Hz ince, sol = −40Hz kalın, 400–2000Hz). Her ayarda yeni perdede kısa bip.
- **Kalıcı hafıza:** seçilen ton NVS'e (`Preferences`, namespace "bt", key "ionicHz") yazılır;
  kapatınca silinmez, açılışta yüklenir. (Not: eskiden cihaz HİÇBİR ayarı kaydetmiyordu.)
- İyonik ekranına **TON göstergesi** (alt sol, büyük rakam + Hz).

## Derleme / dağıtım (kablosuz, Arduino/kablo yok)
- GitHub Actions: `firmware/**` değişince otomatik derler → Release "firmware" etiketine
  3 `.bin` yükler (NORMAL, UZAKTAN, PASIF). Workflow: `.github/workflows/build.yml`.
- FQBN: `esp32:esp32:esp32s3:PartitionScheme=min_spiffs` (Minimal SPIFFS, 1.9MB APP w/ OTA).
- Kullanıcıya .bin verirken: **benzersiz isimli dosya** gönder (tarayıcı cache sorunu —
  aynı isimli release dosyası eski sürümü gösterebiliyor). Telefona indir → 192.168.4.1/update → yükle.

## Menü yapısı (PASİF, emülatörle birebir)
Ana menü: **IONIC · FREKANS · SERBEST · EŞİK**
- IONIC: sinyal yayları + durum (ZAYIF/GÜÇLÜ/ÇOK GÜÇLÜ) + % + 3 daire + TON kutusu.
- FREKANS: 11 metal (ALTIN,GUMUS,BRONZ,BOSLUK,KURSUN,ALIMINYUM,BAKIR,ELMAS,SU,MERMER,MUCEVHER)
  + radar. Frekanslar: 5100,8700,11300,700,4000,7000,11700,12700,10000,2300,5500.
- SERBEST: 5 haneli frekans girişi (100–50000 Hz).
- EŞİK: iyonik tetikleme eşiği (0.10–3.00V).

## Önizleme/emülatör HTML'leri (claude.ai artifact)
- Cihaz emülatörü (tüm menü + modlar): artifact "Cihaz Emulator"
- Cihaz ton ekranı önizleme: artifact "Cihaz Ton Ekrani"
- 3 kademe buzzer (equalizer düğmeli): artifact "3 Kademe Buzzer"
- Oktion ses analizleri + kullanıcının kod paterni sesi: ilgili artifact'lar

## Sıradaki olası işler (backburner)
- Pasif buzzer takılınca v48-P'yi cihaza yükleyip test (kullanıcı "yarın bakalım" dedi).
- Açılış melodisi (pasif buzzerla gerçek melodi) — ertelendi.
- UZAKTAN sürümünü v48 seviyesine güncelleme (şu an v42).
- İstenirse: tınlama/yumuşaklık cihazda ayarlanamaz (tone() sadece perde verir); sadece perde ayarlanır.

## İletişim notu
Kullanıcı: Barış (Türkçe konuşur). Cihaz uzakta (200-300km), bu yüzden her şey kablosuz
(GitHub Actions + web OTA) kurgulandı; kabloyla flash sadece boş çipin ilk yüklemesinde gerekir.
