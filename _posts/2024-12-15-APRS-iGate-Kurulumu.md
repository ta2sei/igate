---
title: "APRS iGate Kurulumu"
date: 2024-12-15
---

# APRS RX iGate Kurulumu

**Raspberry Pi, RTL-SDR dongle ve önceden oluşturulmuş bir görüntü kullanarak yalnızca APRS RX iGate kurulumu**

*Yazar: Sinan ISIK*  
*Tarih: 15 Aralık 2024*  

## Giriş

Bu kılavuz, Raspberry Pi ve RTL-SDR dongle kullanarak APRS RX iGate kurulumunu adım adım açıklamaktadır. Bu kılavuz, Raspbian Stretch ve Direwolf 1.4 kullanılarak güncellenmiştir. SD karta APRS iGate image dosyası bootable olarak yazıldıktan sonra Raspberry PI bu diske yazılan image ile açılır ve içeriğinde bu klavuzda belirtilen bazı dosyaların düzenlemesi gerektirmektedir.

## Gereksinimler

- Raspberry Pi (Zero, Zero W, 2B, 3B, 3B+)
- RTL-SDR dongle (TCXO'lu önerilir)
- SD kart (16 GB önerilir)
- İnternet bağlantısı

## Adımlar

### 1. Raspberry Pi Görüntüsünü İndirme ve Hazırlama

1.	Özel olarak oluşturulmuş Raspberry Pi görüntüsünü (`aprs-igate-stretch.zip`) buradan (`https://sourceforge.net/projects/amateur-radio-aprs-igate/files/` ) indirin.
2.	İndirilen .zip dosyayı bir klasöre açın ve FAT32 olarak formatlanmış bir SD karta açılan `aprs-igate-stretch-2.img` dosyasını (~7.31 GB) bootable olarak yazın.
Bu, sıkıştırılmış dosyayı açtığınızda yaklaşık 8 GB'a kadar genişleyecek 2,2 GB'lık bir dosyadır. Dosyayı açmakta zorlanıyorsanız, bu torrent'i (`http://g6nhu.co.uk/aprs_igate_image.torrent` ) kullanarak sıkıştırılmamış sürümünü de indirebilirsiniz. İndirdiğiniz `aprs-igate-stretch1.zip` dosyasını bir klasöre açın. Winrar ile açarken %99’da hata verdi. 7zip ile sorunsuz açtım. Dosyayı bozulmadan indirdiğinizi teyit etmek için MD5 doğrulaması yapabilirsiniz.
MD5 doğrulamasını online olarak (`https://emn178.github.io/online-tools/md5_checksum.html` ) adresinden yapabilirsiniz, ya da MD5 tool’unu (`https://www.pc-tools.net/win32/md5sums/` ) sayfasından indirerek smd5sum -c aprs-igate-stretch1.zip  komutu kontrol edebilir ve aşağıdaki MD5 hash kodu ile aynı olduğu doğrulanır.
MD5  (`aprs-igate-stretch.zip`) : `f8469173f3e3d9c420dbaefaff7d9875`
MD5 (`aprs-igate-stretch.img`) : `22998d62e7dd1b3f270834f6bfd5f089`
Açılan dosyası aprs-igate-stretch.img SD karta bootable olarak yazın . Bootable olarak SD Card’e yazmadan önce diski FAT32 olarak formatlayın. Bootable olarak .img dosyasını diske yasmak için Balena Etcher (`https://etcher.balena.io/` ) veya Win32 Disk Imager (`https://win32diskimager.org/` ) kullanabilirsiniz. Balena Etcher ile bende hata verdiği için Win32 Imager kullandım ve sorun olmadan bootable olarak SD karta yazdı. 
Image dosyası (.img) 8 GB'lık bir SD kart kullanılarak oluşturuldu ancak Raspberry Pi'nizde 16 GB'lık bir kart kullanmanızı öneririm.
3.	SD kartı Raspberry Pi'ye takın
4.	Raspberry Pi cihazınıza bilgisayarınızdan erişip yönetebilmek için bilgisayarınızın bağlı olduğu aynı switch’e Network kablosu ile bağlayın.  Raspberry Pi cihazı açıldığında Network kablosu takılı ise bağlı olduğu networkten otomatik IP alacak şekilde ayarlanmıştır. Hangi IP’yi aldığını anlamak için bilgisayarınızda Network Scanner gibi bir tool kullanarak (`https://www.softperfect.com/products/networkscanner/` ) networkte IP almış cihazlardan raspberry-igate gibi bir isimle bulabilirsiniz.
5.	IP’sini bulamadıysanız Raspberry Pi cihazınızı doğrudan erişmek için HDMI kablosu ile monitorünüze veya TV’nize bağlayın, USB mouse ve USB klavye bağlayarak açıp IP’yi kendiniz Manuel de verebilirsiz. GUI ekranından da aşağıdaki yapılandırma adımlarını gerçekleştirebilirsiniz.
6.	Monitor ve mouse, klavye baglantılarıyla uğraşmak istemezseniz en pratik olanı bilgisayarınızdan Raspberry Pi’ya IP’si üzerinden Putty ile (`https://www.putty.org/` ) SSH bağlantısı yaparak erişmektir.


### 2. Raspberry Pi Yapılandırması

1. Raspberry Pi'ye SSH ile bağlanın. `ssh pi@<IP_adresi>` Bende aldığı IP 192.168.1.156 olduğundan ben aşağıdaki komut ile bağlanıyorum:
    ```sh
    ssh pi@192.168.1.156
    ```
    İlk açılışta, 
    Kullanıcı adı: `pi`
    Sifre: `raspberry`

2. Raspberry Pi yapılandırma aracını açın:
    ```sh
    sudo raspi-config
    ```
3. Dosya sistemini genişletin: `Gelişmiş Seçenekler > Dosya Sistemini Genişlet`
4. Kullanıcı parolasını değiştirin: `Kullanıcı Parolasını Değiştir`
5. Saat dilimini ayarlayın: `Yerelleştirme Seçenekleri > Saat Dilimini Değiştir --> Istanbul`
6. Wi-Fi ülkesini ayarlayın: `Yerelleştirme Seçenekleri > Wi-Fi Ülkesini Değiştir --> TR`
7. Hostname'i ayarlayın: `Network Options > Hostname`

### 3. Direwolf Yapılandırması

APRS ayarları için öncelikle bir Amatör Telsiz Çağrı İşaretinizin olması gerekiyor. Çağrı işareti almak için Kıyı Emniyeti veya MEB üzerinden sınavına girerek temin edebilirsiniz. Yapılması gerekenler ve basvuru detayı için aşağıdaki linkten yararlanabilirsiniz. Çok zor bir sınav degil, Soru bankasında çıkan sorulara 1-2 gün çalışmak yetiyor A sınıfı çağrı işareti almak için. MEB her ay bir sınav tarihi yayınlarken, Kıyı Emniyeti yılda 2 defa düzenliyor. Herhangi birinden alabilirsiniz. Alacağınız Çağrı İşareti açısından bir fark bulunmuyor.

Kıyı Emniyeti Amatör Telsiz Sınav Başvuru: `https://www.turkiye.gov.tr/kegm-amator-telsiz-sinav-basvuru` 
Çağrı İşaretinize göre bir Passcode oluşturulması gerekiyor. Benim çağrı TA2SEI. Buna göre yayın yapacak igate istasyonu için TA2SE-10 olarak asagidaki adresten bir Passcode olusturdum. Sizde kendi çağrı işaretinize göre bir Passcode olusturabilirsiniz.
`https://apps.magicbug.co.uk/passcode/index.php`
CallSign: `TA2SEI-10`
Passcode: `20665`
 

1. Aşağıdaki komut ile `sdr.conf` dosyasını düzenleyin:
    ```sh
    cd ~
    sudo nano -c sdr.conf
    ```
2. Çağrı işaretinizi ve SSID'yi girin: `MYCALL <çağrı işareti>-<SSID>`
    ```plaintext
    MYCALL TA2SEI-10
    ```
3. IGSERVER satırını bölgenize göre düzenleyin: `IGSERVER <bölge>.aprs2.net` bizim bolge euro oldugu için euro olarak secildi.
    ```plaintext
    IGSERVER euro.aprs2.net
    ```
4. IGLOGIN satırını düzenleyin: `IGLOGIN <çağrı işareti>-<SSID> <APRS şifresi>`. Olusturdugunuz Passcode'a göre düzenlemek gerekiyor.
    ```plaintext
    IGLOGIN TA2SEI-10 20665
    ```
5. PBEACON satırını enlem, boylam ve çağrı işareti ile düzenleyin. iGate'in yayın yapacağı konumu https://maps.google.com adresinden bularak o noktaya sağ tıklayıp 40.123456, 29.123456 formatında bulabilirsiniz. İlki latitude: enlem, digeri ise longtitude:boylamı gösterir. Aşağıdaki komutta lat ve long değerlerine bu konum bilgileri girilir.
    ```plaintext
    PBEACON sendto=IG delay=2:00 every=15:00 symbol="igate" overlay=R lat=40.123456 long=29.123456 COMMENT="TA2SEI-10 | Marmara Unv. Istanbul/Pendik i-Gate Istasyonu"
    ```

Port 8000 ve Port 8001 kullanımda dediği için `sdr.conf` dosyasına asagidaki satirlar eklendi.
    ```plaintext
    AGWPORT 8002
    KISSPORT 8003
    ```
    
### 4. Direwolf'u Test Etme

1. Aşağıdaki komutu kullanarak testi gerçekleştirin:
    ```sh
    rtl_fm -f 144.80M - | direwolf -c sdr.conf -r 24000 -D 1 -
    ```
    Bu komut bende hata verdi, ama aşğıdaki gibi düzenleyince hatasız çalıştı. Sizde ilki çalışabilir.

    ```sh
    rtl_fm -f 144.80M | direwolf -c sdr.conf -r 24000 -D 1
    ```
   
3. Testten çıkmak için `ctrl-c` tuşlarına basın.

RTL için asaigaki gibi bir hata verdi. 
`Using device 0: Generic RTL2832U OEM`
`usb_claim_interface error -6`
`Failed to open rtlsdr device #0.`

Bunu gidermek için önce RTL cihazinin modeli ve ID'si lsusb komutu ile tespit edildi. (Asagidaki gibi ID karsisinda yazan 0bda=idVendor ve 2838=idProduct)
 ```sh
lsusb
```

`Bus 001 Device 004: ID 0bda:2838 Realtek Semiconductor Corp. RTL2838 DVB-T`
`Bus 001 Device 003: ID 0424:ec00 Standard Microsystems Corp. SMSC9512/9514 Fast Ethernet Adapter`
`Bus 001 Device 002: ID 0424:9514 Standard Microsystems Corp. SMC9514 Hub`
`Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub`


Asagidaki komut ile `rtl-sdr.rule` dosyasının son satırına bu `lsusb`'deki `idVendor` ve `idProduct` bilgileri kullanılarak asagidaki satir eklendi ve bir satir alta geilerek kaydedildi.

# Realtek Semiconductor Corp. RTL2838 DVB-T
`SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE:="0666"`

```sh
pi@raspigate:~ $ sudo nano -c /etc/udev/rules.d/rtl-sdr.rules
pi@raspigate:~ $ cat /etc/udev/rules.d/rtl-sdr.rules
```

Dosyanın sonuna eklendiği teyit edilir.
# Realtek Semiconductor Corp. RTL2838 DVB-T
`SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE:="0666"`

Asagidaki gibi `/etc/modprobe.d/raspi-blacklist.conf` dosyasına da şağıdaki satir eklenir bir satır asagiya geçilir ve kaydedilir ve sistem reboot edilir.
`blacklist rtl2838`

```sh
pi@raspigate:~ $ ls /etc/modprobe.d
blacklist-rtl8192cu.conf  blacklist-rtl8xxxu.conf  ipv6.conf  libpisock9.conf  raspi-blacklist.conf
```

```sh
pi@raspigate:~ $ sudo nano -c /etc/modprobe.d/raspi-blacklist.conf
blacklist rtl2838
```

```sh
pi@raspigate:~ $ sudo nano -c /etc/modprobe.d/raspi-blacklist.conf
pi@raspigate:~ $ sudo shutdown -r now
```

--------------------------------------------------------------------------------
```sh
lsusb
```
Bus 001 Device 004: ID 0bda:2838 Realtek Semiconductor Corp. RTL2838 DVB-T

```sh
cat /etc/udev/rules.d/rtl-sdr.rules
```
Asagidaki satırı `lsusb` sonucuna gore ekle
```sh
pi@raspigate:~ $ sudo nano -c /etc/udev/rules.d/rtl-sdr.rules
```

# Realtek Semiconductor Corp. RTL2838 DVB-T
`SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", MODE:="0666"`

Reboot olduktan sonra asagidaki satir calistirildi ve sonucun internet üzerinden http://aprs.fi sayfasinda goruntulendigi gözlendi.

```sh
pi@raspigate:~ $ rtl_fm -f 144.80M | direwolf -c sdr.conf -r 24000 -D 1
```

`Dire Wolf version 1.4`

`Reading config file sdr.conf`
`Audio device for both receive and transmit: null  (channel 0)`
`Channel 0: 1200 baud, AFSK 1200 & 2200 Hz, E+, 24000 sample rate.`
`Note: PTT not configured for channel 0. (Ignore this if using VOX.)`
`Ready to accept AGW client application 0 on port 8002 ...`
`Use -p command line option to enable KISS pseudo terminal.`
`Ready to accept KISS client application on port 8003 ...`
`Found 1 device(s):`
`  0:  Realtek, RTL2838UHIDIR, SN: 00000001`

`Using device 0: Generic RTL2832U OEM`
`Found Rafael Micro R820T tuner`
`Tuner gain set to automatic.`
`rtl_fm: symbol lookup error: rtl_fm: undefined symbol: rtlsdr_set_bias_tee`

`Now connected to IGate server euro.aprs2.net (85.188.1.129)`
`Check server status here http://85.188.1.129:14501`

`[ig] # aprsc 2.1.19-g730c5c0`
`[ig] # logresp TA2SEI-10 verified, server T2FINLAND`
`[ig] TA2SEI-10>APDW14:!4056.12NR02917.45E&TA2SEI-10 | Marmara Unv. Istanbul/Pendik i-Gate Istasyonu`


### 5. Direwolf'u Servis Olarak Ayarlama

1. Direwolf'u hizmet olarak etkinleştirin:
    ```sh
    sudo systemctl enable direwolf
    ```
2. Servisi başlatmak için:
    ```sh
    sudo systemctl start direwolf
    ```
3. Servisi durdurmak için:
    ```sh
    sudo systemctl stop direwolf
    ```
4. Servisin durumunu kontrol etmek için:
    ```sh
    sudo systemctl status direwolf
    ```

## Sonuç

Bu kılavuz ile Raspberry Pi ve RTL-SDR dongle kullanarak APRS RX iGate kurulumunu başarıyla tamamlayabilirsiniz. Herhangi bir sorun yaşarsanız bana yazabilirsiniz.

---

## Kaynak
https://qso365.co.uk/2018/04/updated-guide-to-setting-up-an-aprs-rx-only-igate-using-a-raspberry-pi-rtl-sdr-dongle-and-a-pre-built-image/
https://picoballoon.ist/2019/02/10/raspberry-pi-ve-rtl-sdr-kullanarak-kolayca-aprs-igate-yapimi/





