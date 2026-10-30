# STM32 Blocks Studio

**Blokları sürükle, kabloları bağla, STM32 kartına yükle.**

STM32 Blocks Studio, blok diyagramlarından STM32 HAL C kodu üreten, bu kodu
derleyen ve doğrudan karta yükleyen Türkçe bir Windows masaüstü uygulamasıdır.

```
Blok diyagramı ──► main.c ──► derleme ──► .elf ──► probe-rs ──► STM32 kartı
```

<!-- Ekran görüntüsü eklemek için: docs/ekran.png dosyasını koyup bu satırı değiştirin. -->
<!-- ![Uygulama ekranı](docs/ekran.png) -->

## İndir

[**Son sürümü indir**](https://github.com/Emiratlay/STM32-Block-Studio/releases/tag/V0.3)

`STM32-Blocks-Studio-<sürüm>-setup.exe` dosyasını indirip çalıştırın. Derleyici,
`make`, `probe-rs` ve ST-Link sürücüsü kurulumla gelir. Ayrıca kurmanız gereken
tek program [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html).

> Setup dijital imzalı olmadığı için Windows SmartScreen uyarı gösterebilir.
> **Daha fazla bilgi → Yine de çalıştır** ile devam edebilirsiniz.

## Özellikler

- **22 blok türü:** GPIO, UART, SPI, I2C, ADC, DAC, PWM, zamanlayıcı ve harici
  kesme.
- **İki kablo türü:** Akış kabloları çalışma sırasını, değer kabloları okunan
  veriyi taşır.
- **Canlı doğrulama:** Geçersiz pin, pin çakışması, eksik kurulum bloğu ve akış
  döngüsü anında bildirilir.
- **Kod önizleme:** Üretilen C kodunu görüntüleyin, kopyalayın, dışa aktarın.
- **Tek tuşla derle ve yükle:** Çıktı uygulama içindeki konsolda görünür.
- **Hazır örnekler:** LED, UART, buton kesmesi, ADC; NUCLEO-G474RE örnekleri
  dahil.

## Desteklenen kartlar

| Aile | Örnek | Not |
| --- | --- | --- |
| STM32F4 | STM32F401RE | |
| STM32F1 | STM32F103C8 | |
| STM32G4 | NUCLEO-G474RE | I2C bloğu henüz desteklenmiyor |

Yükleme için ST-Link gibi `probe-rs` uyumlu bir programlayıcı gerekir.

## Hızlı başlangıç

1. Setup'ı kurun, ardından STM32CubeMX'i kurun.
2. CubeMX'te kartınız için **Toolchain / IDE: Makefile** seçerek bir proje
   oluşturun.
3. Uygulamada **Hedef → Ayarlar** penceresinden BSP klasörünü ve çipi seçin.
4. **Dosya → Örnekler** menüsünden bir örnek açın.
5. **Ctrl+B** ile kodu yazın, **F5** ile derleyin, **F6** ile karta yükleyin.

Ayrıntılı adımlar ve sorun giderme: [Kullanım kılavuzu](docs/KULLANIM.md).

## Kısayollar

| Kısayol | İşlev |
| --- | --- |
| `F5` | Derle |
| `Shift+F5` | Derle ve yükle |
| `F6` | Karta yükle |
| `F7` | Kodu önizle |
| `Ctrl+B` | Kodu BSP'ye yaz |
| `F8` | Konsolu aç/kapat |

## Sınırlamalar

- Yalnızca Windows 10/11 (64 bit) için paketlenir.
- Saat varsayılan olarak HSI'dir; yüksek hızlar için BSP'de ayar gerekir.
- Canlı hata ayıklama henüz yoktur.
- Değer kabloları yalnızca `uint32_t` taşır.

## Lisans

STM32 Blocks Studio ücretsiz kullanılabilir, yeniden dağıtılamaz: [LICENSE](LICENSE). Üretilen C kodu size aittir. Kaynak kodu yayınlanmamaktadır.

Setup ile gelen üçüncü taraf araçlar kendi lisanslarıyla dağıtılır:
[THIRD-PARTY.txt](THIRD-PARTY.txt).

STM32, STMicroelectronics'in ticari markasıdır. Bu proje ST ile bağlantılı
değildir.
