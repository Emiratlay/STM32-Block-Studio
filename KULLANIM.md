# STM32 Blocks Studio — Kullanım Kılavuzu

Blok diyagramlarıyla **STM32 HAL (C)** kodu üreten, kodu derleyen ve `probe-rs`
ile doğrudan karta yükleyen Rust tabanlı masaüstü IDE.

Arayüz Türkçedir. Program, blok diyagramını tek bir `main.c` dosyasına çevirir;
HAL sürücüleri, startup ve linker betiği mevcut STM32CubeMX BSP projesinden gelir.

```
Blok diyagramı ──► blocks-core ──► main.c ──► BSP (make) ──► .elf ──► probe-rs ──► kart
```

## Özellikler

- **22 blok türü, 10 kategori:** GPIO, UART, SPI, I2C, ADC, DAC, PWM, zamanlayıcı
  ve harici kesme blokları.
- **İki kablo türü:** yeşil **akış** kabloları çalışma sırasını, elmas uçlu
  **değer** kabloları ADC/GPIO/SPI okumalarını UART/PWM/DAC bloklarına taşır.
- **Şema güdümlü bloklar:** alanlar tek bir yerden tanımlanır; palet, denetçi ve
  kod üreteci aynı şemadan beslenir.
- **Canlı doğrulama:** geçersiz pin, eksik kurulum bloğu, pin çakışması, akış
  döngüsü ve bağlantısız bloklar anında bildirilir.
- **Kod önizleme:** üretilen C kodunu uygulama içinde görüntüleme, kopyalama,
  dışa aktarma ve BSP'ye yazma.
- **Derleme + yükleme:** BSP klasöründe derleme komutu çalıştırılır, ardından
  `probe-rs` ile firmware karta yazılır. Tüm çıktı uygulama içi konsolda görünür.
- **Hedef aileler:** STM32F4, STM32F1 ve **STM32G4** (NUCLEO-G474RE dahil).
- **Gömülü örnekler:** LED yakıp söndürme, UART merhaba, EXTI buton, ADC → UART,
  NUCLEO-G474RE LED (PA5) ve buton (PC13) örnekleri.

## Kurulum

1. `STM32-Blocks-Studio-<sürüm>-setup.exe` dosyasını çalıştırın. Derleyici,
   `make`, `probe-rs` ve ST-Link sürücüsü uygulamayla birlikte kurulur.
2. [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html)
   programını ayrıca kurun.

> Setup dijital imzalı olmadığı için Windows SmartScreen uyarı gösterebilir.
> **Daha fazla bilgi → Yine de çalıştır** ile devam edebilirsiniz.

Araçların durumunu **Hedef → Ayarlar** penceresinde görebilirsiniz.

## Kullanım

1. Soldaki paletten bir bloğa tıklayın; blok tuvalin ortasına eklenir.
2. Blokları sürükleyerek yerleştirin. Boş alanı sürükleyerek tuvali kaydırın,
   tekerlekle yakınlaştırın.
3. Bir çıkış portundan (sağdaki daire/elmas) diğer bloğun giriş portuna sürükleyin.
4. Sağ paneldeki denetçiden pin, örnek ve parametreleri düzenleyin.
5. **F7** ile üretilen kodu önizleyin, **Ctrl+B** ile BSP'ye yazın, **F5** ile
   derleyin, **F6** ile karta yükleyin. Doğrulama hatası varsa derleme
   başlatılmaz; sağ paneldeki hataları düzeltin.

### Kısayollar

| Kısayol | İşlev |
| --- | --- |
| `Ctrl+N` / `Ctrl+O` | Yeni proje / proje aç |
| `Ctrl+S` / `Ctrl+Shift+S` | Kaydet / farklı kaydet |
| `F5` | Derle |
| `Shift+F5` | Derle + yükle |
| `F6` | Firmware'i karta yaz |
| `F7` | Kodu üret ve önizle |
| `Ctrl+B` | Kodu BSP'ye yaz |
| `F8` | Konsolu aç/kapat |
| `Del` / `Ctrl+D` | Seçili bloğu sil / çoğalt |
| Tekerlek | Yakınlaştır |
| Boş alan sürükle | Tuvali kaydır |

### Örnek projeler

**Dosya → Örnekler** menüsünden yüklenebilir:

- **LED Yakıp Söndür** — PC13 LED'ini 500 ms'de bir tersler.
- **UART Merhaba** — USART2 (PA2/PA3, 115200 8N1) üzerinden metin gönderir.
- **Buton ile LED (EXTI)** — PC13 düşen kenar kesmesinde PA5'i tersler.
- **ADC Raporu (ADC → UART)** — PA4 / ADC1 kanal 4 değerini UART'a yazar.
- **NUCLEO-G474RE · LED Yakıp Söndür** — LD2'yi (PA5 / D13) 250 ms'de bir tersler;
  kart G4 ailesi ve `STM32G474RE` olarak hazır gelir.
- **NUCLEO-G474RE · Buton ile LED** — mavi buton (B1/PC13) LD2'yi (PA5) tersler.

## BSP entegrasyonu (STM32CubeMX)

Üretilen `main.c` tek başına bir proje değildir; HAL katmanını ve başlangıç
kodunu BSP'den alır. Önerilen akış:

1. STM32CubeMX'te kartınız için **Makefile** çıktılı bir proje oluşturun
   (ör. STM32F401RE, STM32F103C8, **STM32G474RE**). Saat ağacını ve hata ayıklama
   pinlerini ayarlayın, kod üretin.
2. Projenin yolunda boşluk olmamasına dikkat edin (ör. `C:\STM32\MyBoard`).
3. Studio'da **Hedef → Ayarlar**:
   - **BSP klasörü:** `C:\STM32\MyBoard`
   - **Derleme komutu:** `make -j4`
   - **Çip:** `probe-rs` hedef adı (ör. `STM32F401RE`)
4. **Ctrl+B** ile kodu BSP'ye yazın. İlk yazımda mevcut
   `Core/Src/main.c` dosyası `main.c.stm32blocks.bak` olarak yedeklenir.
5. **MSP dosyasını derlemeden çıkarın.** Üretilen `main.c`, `HAL_*_MspInit`
   fonksiyonlarını kendisi tanımlar. `Makefile` içindeki `C_SOURCES`
   listesinden şu satırı kaldırın:
   ```make
   Core/Src/stm32f4xx_hal_msp.c   # F1: stm32f1xx_hal_msp.c · G4: stm32g4xx_hal_msp.c
   ```
   Aksi halde "multiple definition of `HAL_UART_MspInit`" benzeri bağlama
   hataları alırsınız.
6. **Çakışan kesme işleyicilerini kaldırın.** CubeMX'in ürettiği
   `Core/Src/stm32f4xx_it.c` (F1: `stm32f1xx_it.c`, G4: `stm32g4xx_it.c`) içinde,
   diyagramınızın kullandığı `EXTI*_IRQHandler` ve `TIM*_IRQHandler` fonksiyonları
   varsa bunları silin veya yorum satırına alın. `SysTick_Handler`, `NMI_Handler`,
   `HardFault_Handler` gibi işleyiciler kalmalıdır.
7. **F5** ile derleyin, **F6** ile yükleyin.

> Üretilen `main.c` her yazımda baştan oluşturulur. Elle yapılan değişiklikler
> kaybolur; kalıcı değişiklikleri bloklarda veya ayrı `.c` dosyalarında yapın.

### Saat yapılandırması

Üretilen kod `blocks_system_clock_config()` adında **zayıf (weak)** bir fonksiyon
tanımlar. Varsayılan gövde boştur, yani kart HSI 16 MHz ile çalışır. CubeMX'in
kurduğu saat ağacını kullanmak için BSP yedeğindeki `SystemClock_Config()`
gövdesini ayrı bir dosyaya taşıyın:

```c
/* Core/Src/clock.c */
#include "main.h"

void blocks_system_clock_config(void)
{
    /* CubeMX'in ürettiği SystemClock_Config() gövdesi. */
}
```

Bu güçlü tanım, `main.c` içindeki zayıf tanımın yerine geçer. Yeni dosyayı
`Makefile` içindeki `C_SOURCES` listesine eklemeyi unutmayın. Saat
yapılandırmasını tamamen atlamak için proje JSON'unda
`"configure_clock": false` kullanılabilir.

## Ayarlar

| Alan | Açıklama |
| --- | --- |
| `bsp_dir` | Makefile tabanlı CubeMX projesinin kök klasörü |
| `build_cmd` | BSP klasöründe çalıştırılacak derleme komutu (`make -j4`) |
| `firmware` | Boşsa `BSP`, `BSP/build`, `Debug`, `Release` altında aranır; önce `.elf`, sonra `.hex`, sonra `.bin` seçilir |
| `probe_rs` | `probe-rs` yolu; boşsa `PATH` ve bilinen klasörlerde aranır |
| `probe_chip` | `probe-rs --chip` değeri; boşsa pano çip adı kullanılır |
| `bin_base` | `.bin` yüklenirken taban adres (varsayılan `0x08000000`) |
| `flash_after_build` | Derleme başarılı olunca otomatik yükle |
| `copy_sources` | Derlemeden önce üretilen `main.c`'yi BSP'ye yaz |

Ayarlar proje dosyasıyla birlikte JSON olarak saklanır.

## Üretilen kodun yapısı

`main.c` sırasıyla şunları içerir:

1. Çevre birimi tutamaçları (`huart2`, `hspi1`, `hi2c1`, `hadc1`, `hdac`, `htimX`)
2. Prototipler ve `MX_*_Init` fonksiyonları
3. `MX_GPIO_Init` — pin modları, çıkış başlangıç seviyeleri, EXTI NVIC ayarları
4. `HAL_*_MspInit` — çevre birimi pinleri, AF numaraları ve saatler
5. Değer yardımcıları — `blk{id}_read()` biçiminde saf C fonksiyonları
6. Kesme geri çağrıları — `HAL_GPIO_EXTI_Callback`, `HAL_TIM_PeriodElapsedCallback`
7. `blocks_app_loop()` — Başlangıç bloğundan itibaren üretilen akış
8. `main()` — `HAL_Init`, saat, çevre birimi başlatma ve sonsuz döngü

Kesme akışlarında `Bekle`, `UART ... Gönder` gibi bloklayıcı bloklar
kullanılmamalıdır; kesme bağlamında uzun işlem yapılmaması gerekir.

## Sorun giderme

- **`probe-rs` bulunamadı:** Uygulamayı yeniden kurun veya **Ayarlar → probe-rs**
  alanına tam yolu girin.
- **Bağlı prob yok:** USB kablosunu, ST-Link sürücüsünü ve kartın beslenmesini
  kontrol edin. **Hedef → Bağlı probları listele** çıktısına bakın.
- **Bilinmeyen çip:** `probe-rs chip list` ile hedef adını doğrulayın ve
  **Ayarlar → Çip** alanına yazın. Örnek: `STM32F401RE`, `STM32F103C8`,
  `STM32G474RE`.
- **`multiple definition` bağlama hatası:** `stm32f4xx_hal_msp.c` (G4:
  `stm32g4xx_hal_msp.c`) dosyasını derlemeden çıkarın; `stm32f4xx_it.c`
  (G4: `stm32g4xx_it.c`) içindeki çakışan `EXTI*`/`TIM*` işleyicilerini silin
  (bkz. BSP entegrasyonu).
- **`undefined reference to HAL_...`:** BSP'nin ailesi (F4/F1/G4) ile projedeki
  pano ailesi uyuşmuyor olabilir; ayrıca ilgili HAL modülünün CubeMX'te
  etkinleştirildiğinden emin olun.
- **`make` bulunamadı:** Uygulamayı yeniden kurun veya **Ayarlar → Derleme**
  **komutu** alanını kontrol edin.
- **Firmware bulunamadı:** Derlemenin başarılı olduğundan emin olun veya
  **Ayarlar → Firmware** alanına `.elf` yolunu elle girin.
- **Türkçe karakterler bozuk:** Konsol çıktısı `make`/`gcc` tarafından
  üretiliyorsa `chcp 65001` ile UTF-8 kod sayfasına geçin. Uygulama içi metinler
  UTF-8'dir.

## Sınırlamalar

- STM32F4, STM32F1 ve STM32G4 aileleri desteklenir; pin/AF tablosu yaygın
  kartları kapsar, tüm varyantları değil. G4 tablosu CubeMX pin verisinden
  (STM32G47x) üretildi; listede olmayan bir pin için AF numarasını elle
  girebilirsiniz.
- **STM32G4'te I2C bloğu desteklenmez.** G4 I2C'si F4'ten farklı bir `TIMINGR`
  yapılandırması ister; doğrulama bu blokta hata verir. GPIO, UART, SPI, PWM,
  zamanlayıcı, EXTI, ADC ve DAC G4'te kullanılabilir.
- Saat ağacı varsayılan olarak HSI 16 MHz'dir (zayıf
  `blocks_system_clock_config()`). 170 MHz gibi bir hız için BSP'nizde güçlü
  (strong) bir tanım yapın.
- Tek bir `Başlangıç` bloğu ve döngüsüz akış desteklenir; `main()` zaten sonsuz
  döngüdür.
- ADC tek kanal, SPI tek bayt transferi, I2C yalnızca master yazma, DAC tek
  kanal yapılandırması desteklenir.
- 16 bit zamanlayıcılarda çok uzun periyotlar (yaklaşık 6,5 saniyeden fazla)
  donanımın izin verdiği en yakın değere yuvarlanır.
- Değer kabloları `uint32_t` taşır; kayan nokta ve dize değerleri yoktur.
- Yükleme `probe-rs` CLI üzerinden yapılır; STM32CubeProgrammer, DFU veya
  seri bootloader desteklenmez.
- Canlı hata ayıklama (breakpoint, register görüntüleme) henüz yoktur.

## Lisans

Ücretsiz kullanılabilir, yeniden dağıtılamaz. Ayrıntılar için `LICENSE` dosyasına bakın. Üretilen C kodu size aittir.
