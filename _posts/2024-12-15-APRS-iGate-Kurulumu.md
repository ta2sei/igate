---
title: "APRS iGate Kurulumu"
date: 2024-12-15
---

# APRS RX iGate Kurulumu

**Raspberry Pi, RTL-SDR dongle ve önceden oluşturulmuş bir görüntü kullanarak yalnızca APRS RX iGate kurulumu**

*Yazar: Sinan ISIK*  
*Tarih: 15 Aralık 2024*  

## Giriş

Bu kılavuz, Raspberry Pi ve RTL-SDR dongle kullanarak APRS RX iGate kurulumunu adım adım açıklamaktadır. Bu kılavuz, Raspbian Stretch ve Direwolf 1.4 kullanılarak güncellenmiştir. Kurulum süreci yaklaşık yarım saat sürmektedir ve yalnızca birkaç dosya düzenlemesi gerektirmektedir.

## Gereksinimler

- Raspberry Pi (Zero, Zero W, 2B, 3B, 3B+)
- RTL-SDR dongle (TCXO'lu önerilir)
- SD kart (16 GB önerilir)
- İnternet bağlantısı

## Adımlar

### 1. Raspberry Pi Görüntüsünü İndirme ve Hazırlama

1. Özel olarak oluşturulmuş Raspberry Pi görüntüsünü buradan (https://sourceforge.net/projects/amateur-radio-aprs-igate/files/) indirin.
2. İndirilen dosyayı açın ve bir SD karta yazın.
3. SD kartı Raspberry Pi'ye takın ve başlatın.

### 2. Raspberry Pi Yapılandırması

1. Raspberry Pi'ye SSH ile bağlanın:
    ```sh
    ssh pi@<IP_adresi>
    ```
2. Raspberry Pi yapılandırma aracını açın:
    ```sh
    sudo raspi-config
    ```
3. Dosya sistemini genişletin: `Gelişmiş Seçenekler > Dosya Sistemini Genişlet`
4. Kullanıcı parolasını değiştirin: `Kullanıcı Parolasını Değiştir`
5. Saat dilimini ayarlayın: `Yerelleştirme Seçenekleri > Saat Dilimini Değiştir`
6. Wi-Fi ülkesini ayarlayın: `Yerelleştirme Seçenekleri > Wi-Fi Ülkesini Değiştir`
7. Hostname'i ayarlayın: `Network Options > Hostname`

### 3. Direwolf Yapılandırması

1. `sdr.conf` dosyasını düzenleyin:
    ```sh
    cd ~
    sudo nano -c sdr.conf
    ```
2. Çağrı işaretinizi ve SSID'yi girin:
    ```plaintext
    MYCALL <çağrı işareti>-<SSID>
    ```
3. IGSERVER satırını bölgenize göre düzenleyin:
    ```plaintext
    IGSERVER <bölge>.aprs2.net
    ```
4. IGLOGIN satırını düzenleyin:
    ```plaintext
    IGLOGIN <çağrı işareti>-<SSID> <APRS şifresi>
    ```
5. PBEACON satırını enlem, boylam ve çağrı işareti ile düzenleyin.

### 4. Direwolf'u Test Etme

1. Aşağıdaki komutu kullanarak testi gerçekleştirin:
    ```sh
    rtl_fm -f 144.80M - | direwolf -c sdr.conf -r 24000 -D 1 -
    ```
2. Testten çıkmak için `ctrl-c` tuşlarına basın.

### 5. Direwolf'u Hizmet Olarak Ayarlama

1. Direwolf'u hizmet olarak etkinleştirin:
    ```sh
    sudo systemctl enable direwolf
    ```
2. Hizmeti başlatmak için:
    ```sh
    sudo systemctl start direwolf
    ```
3. Hizmeti durdurmak için:
    ```sh
    sudo systemctl stop direwolf
    ```
4. Hizmetin durumunu kontrol etmek için:
    ```sh
    sudo systemctl status direwolf
    ```

## Sonuç

Bu kılavuz ile Raspberry Pi ve RTL-SDR dongle kullanarak APRS RX iGate kurulumunu başarıyla tamamlayabilirsiniz. Herhangi bir sorun yaşarsanız bana yazabilirsiniz.

---

## Kaynak
https://qso365.co.uk/2018/04/updated-guide-to-setting-up-an-aprs-rx-only-igate-using-a-raspberry-pi-rtl-sdr-dongle-and-a-pre-built-image/
https://picoballoon.ist/2019/02/10/raspberry-pi-ve-rtl-sdr-kullanarak-kolayca-aprs-igate-yapimi/

