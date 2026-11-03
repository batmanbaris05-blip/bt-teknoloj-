# BT Technology — Firmware

ESP32-S3 metal dedektörü firmware'i. Kod `firmware/BT_Technology/` içinde.

## Kablosuz güncelleme (telefonla, PC/Arduino gerekmez)

1. Kod her değiştiğinde **GitHub Actions** otomatik derler ve `.bin`'i hazırlar.
2. Hazır `.bin`'i **Releases** bölümünden indir:
   **https://github.com/batmanbaris05-blip/bt-teknoloj-/releases/latest**
   (dosya: `BT_Technology.ino.bin`, ~1.44 MB)
3. Telefonu cihazın **BT-TECHNOLOGY** WiFi'ına bağla.
4. Chrome → `http://192.168.4.1/update` → `.bin`'i seç → **GÜNCELLE**.

Derleme ayarı: ESP32-S3 Dev Module, Partition **Minimal SPIFFS (1.9MB APP with OTA)**.
