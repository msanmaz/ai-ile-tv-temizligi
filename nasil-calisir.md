# CleanYourTV nasıl çalışıyor

Kısa hali [README](README.md#1-nasıl-çalışıyor) dosyasında. Bu sayfa, Claude terminalde komut çalıştırırken ne olduğunu takip etmek isteyenler için. Her şey CleanYourTV'nin [README](https://github.com/Gathin23/cleanyourtv/blob/main/README.md) ve [PROMPT.md](https://github.com/Gathin23/cleanyourtv/blob/main/PROMPT.md) dosyalarının 28 Ekim 2026'daki halinden. Kendi kaydımızda gördüğümüz kısım ayrıca belirtildi.

## Bağlantı: ADB

ADB (Android Debug Bridge), Android cihazı bilgisayardan yönetmenin standart yolu. Normalde USB kabloyla çalışıyor, ama TV'lerde ağ üzerinden, 5555 numaralı porttan da açılabiliyor. USB hata ayıklamayı açtığında TV bu portu dinlemeye başlıyor. Bilgisayar ilk bağlandığında TV ekranında "USB hata ayıklamaya izin verilsin mi?" sorusu çıkıyor, bir kez kabul ediyorsun.

TV'ye repodaki `tools/tv.py` bağlanıyor. Bu, `adb-shell` paketini kullanan, tamamen Python ile yazılmış bir ADB istemcisi. Google'ın `adb` programını kurman gerekmiyor. Bizim kayıtta Claude bilgisayarda `adb` olmadığını görünce doğrudan bu aracı kurmayı seçti.

Bazı durumlarda bağlantı farklı kuruluyor:

- macOS'un yerel ağ gizliliği, izin verilmemiş bir uygulamanın yerel ağa erişmesini engelliyor ve bu "No route to host" hatası olarak görünüyor. İzin `python` ya da `adb` programına değil, komutu çalıştıran uygulamaya (Terminal, iTerm2, VS Code) veriliyor: Sistem Ayarları → Gizlilik ve Güvenlik → Yerel Ağ.
- Chromecast with Google TV ve Google TV Streamer gibi bazı cihazlar 5555 yerine eşleştirme kodlu Kablosuz hata ayıklama sunuyor. Claude kodu ve portu senden istiyor, bir kez `adb pair` ile eşleşip `adb tcpip 5555` çalıştırıyor.
- Claude'un komutları yerel ağa ulaşamıyorsa (komutları korumalı bir ortamda, sandbox'ta çalıştıran ayarlarda olur), ayrı bir terminalde `tools/relay.py <TV IP>` çalıştırmanı istiyor. Bu araç sadece kendi bilgisayarında dinleyen bir yönlendirici açıyor.

Elle denemek istersen:

```bash
python3 -m venv tools/venv && tools/venv/bin/pip install -r tools/requirements.txt
export TV_HOST=192.168.1.50                                  # TV'nin IP adresi
tools/venv/bin/python -I tools/tv.py --probe                 # model, Android sürümü, yazılım sürümü
tools/venv/bin/python -I tools/tv.py 'pm list packages'      # TV'de tek bir komut
```

## Yedek ve geri alma kaydı

İki ayrı kayıt var.

1. `PROMPT.md`, Claude'dan bir şeyi değiştirmeden önce tam paket listesini, kapalı paketlerin listesini, `getprop` çıktısını ve bütün ayarları bir yedek klasörüne kaydetmesini istiyor. Ayrıca kapattığı her şeyi geri açan bir betik yazmasını.
2. `tools/disable.sh` bir listeyi uygulamadan önce kendi kaydını `~/.cleanyourtv/` klasörüne yazıyor. Her TV ve liste için bir dosya. İçinde şunlar var:
   - listedeki her paketin o anki durumu: açık, zaten kapalı ya da yüklü değil;
   - ana ekranın hangi uygulamada olduğu;
   - üç animasyon hızı ayarı.

Aynı listeyi ikinci kez çalıştırırsan ilk kayıt korunuyor, temizlenmiş hal onun üstüne yazılmıyor. `tools/restore.sh` bu kaydı tekrar oynatıyor. O yüzden daha önce kendin kapattığın bir paket geri almadan sonra da kapalı kalıyor.

## Kapatma: `pm disable-user`

Paketler şu komutla kapatılıyor:

```
pm disable-user --user 0 <paket.adı>
```

TV'deki Ayarlar → Uygulamalar → Devre dışı bırak düğmesiyle aynı iş. Paket TV'de yüklü kalıyor, çalışmıyor ve görünmüyor. `pm enable` ile geri açılıyor. Projenin kuralı hiçbir şeyi silmemek: araçlar `pm uninstall` kullanmıyor, kullanan katkılar da kabul edilmiyor.

`PROMPT.md` dosyasının Claude'a verdiği kurallardan bazıları:

- Paketleri küçük partiler halinde kapat, her partiden sonra ekran görüntüsü al. Bir parti TV'nin çalışması için gereken bir şeyi kapatırsa hangisi olduğu anlaşılsın.
- Komutları tek tek çalıştır. Zincirlenmiş komutlar ilkinden sonra sessizce durabiliyor.
- Son partiden sonra yeniden başlatmadan önce 15 saniye kadar bekle. Android paket durumunu diske değişiklikten yaklaşık 10 saniye sonra yazıyor, hemen yeniden başlatılırsa eski halle açılıyor.
- Şunlara dokunma: canlı yayın ve HDMI'ı oynatan TV uygulaması, kaynak menüsü, kumanda ve Bluetooth eşleştirme servisleri, ekran klavyesi, Google Play Store ve Play Services. Ne yaptığı belli olmayan üretici paketlerini önce `dumpsys package` ile incele, emin değilsen sor.
- Android'in çekirdek paketlerini kapatma. `tools/disable.sh` bunları içeren bir listeyi zaten reddediyor. `PROMPT.md` sebebini de yazıyor: bu paketlerden ilk altısını kapatan bir liste yüzünden bir Mi TV Stick bir daha açılmamış.
- Adında factory, production ya da fac menu geçen paketleri sadece bakmak için bile açma. Bazı kartlarda açmak bir fabrika modunu tetikliyor ve geri dönüşü zor.
- Yayın uygulamalarına, sesli asistana, Chromecast'e, Miracast'e ve güncelleme servisine dokunmadan önce sana hangilerini kullandığını sor. Sadece işaretlemediklerini kapat.

### Hazır liste neye benziyor

Her model için `examples/<model>/debloat.txt` dosyası var. Beko B43B880B listesinden bir parça:

```
# --- Ads, content recognition and telemetry
tv.anoki.acr.controller                         # Anoki automatic content recognition
com.cltv.fast                                   # FAST channels: ad-supported channel rows
com.beko.tv.channel                             # "Beko Channel": the Beko twin of com.grundig.tv.channel (home-screen tiles and rows)
com.google.android.feedback                     # Google feedback / bug report uploader
```

- `#` ile başlayan satırlar açıklama. Her satırda bir paket adı, yanında ne olduğu yazıyor.
- ACR (automatic content recognition), ekranda ne izlendiğini tanıyan servis. FAST kanalları, ana ekrandaki reklamlı ücretsiz kanal satırları.
- Bir paketi kapatmak istemiyorsan satırının başına `#` koy.
- Dosyanın başında "KEEP" diye bir bölüm var: adı gereksiz gibi görünen ama TV'nin ihtiyaç duyduğu paketler. Beko'da `fusion.android.tv.demo` adlı paket kumandanın Input tuşunu çalıştırıyor. Hazır listenin asıl faydası bu tür bilgiler.

Beko listesinde 18 paket kapatılıyor. Bir kısmı ana ekranla ilgili ve yeni ana ekran kurulduktan sonra uygulanıyor.

## Yeni ana ekran

Repodaki `launcher/` klasöründe küçük bir Android TV ana ekran uygulaması var:

- Düz Java ile yazılmış, 532 KB civarı, 1 GB RAM'li TV'ler için yapılmış. İnternet izni ve arka plan servisi yok.
- TV'de en az Android 8.0 gerekiyor.
- Proje hazır APK dağıtmıyor. Uygulama senin bilgisayarında kaynak koddan derleniyor, bunun için JDK 17 ve Android SDK gerekiyor.

`launcher/build-and-install.sh` sırasıyla şunları yapıyor:

1. TV'nin Android sürümü yetmiyorsa hiçbir şey derlemeden duruyor.
2. Uygulamayı derleyip kuruyor ve varsayılan ana ekran yapıyor.
3. Ana ekran yeni uygulamaya geçmezse değişikliği geri alıyor.

Fabrika ana ekranını kapatırken iki koruma var:

- Kurulum betiği sadece tanıdığı dört fabrika ana ekranından birini kapatıyor. `com.google.android.tvlauncher` ve `com.google.android.apps.tv.launcherx` bunlardan.
- `tools/disable.sh`, listede ana ekranın sahibi olan uygulama varsa ve TV'de ana ekran olabilecek başka bir uygulama yoksa o satırı atlıyor. TV ana ekransız kalmıyor.

Google TV'de bir ek durum var: kurulum sihirbazı (`setupwraith`) ana ekranı kapabiliyor. Ama ilk kurulumda kaydedilen Wi-Fi ağı ona bağlı olabiliyor. Onu kapatmak o ağı siliyor, TV de Wi-Fi'dan ve ADB'den düşüyor. Claude bunu önce kontrol ediyor ve gerekirse ağı Ayarlar'dan yeniden eklemeni istiyor.

Ana ekranı sonradan değiştirmek için:

- Alttaki sırada 4 ile 7 arası uygulama olabiliyor, sırası `config.xml` dosyasından ya da bir temanın `config.json` dosyasından ayarlanıyor.
- Temalar `tools/theme.sh` ile yeniden derlemeden gönderiliyor.

## Kontrol: TV'den ekran görüntüsü

Claude her değişiklikten sonra kumanda tuşuna basıp TV'den ekran görüntüsü çekiyor ve bakıyor:

```bash
tools/venv/bin/python -I tools/tv.py --screenshot shots/01-after.png DPAD_RIGHT
```

Netflix, canlı yayın ve HDMI ekranlarının görüntüsü boş (0 bayt) geliyor. Bu TV'nin kendi davranışı, temizlik bir şeyi bozdu demek değil. Claude o durumda ekranda hangi uygulamanın açık olduğunu `dumpsys` ile kontrol ediyor.

## Sistem güncellemesinden sonra

Güncelleme temizlenmiş sistemin üstüne kuruluyor. Listedeki paketler geri gelebiliyor, ana ekran fabrika ayarına dönebiliyor, yeni hazır uygulamalar çıkabiliyor. `tools/check.py` TV'yi uyguladığın listeyle karşılaştırıyor, `--fix` tekrar kapatıyor:

```bash
TV_HOST=192.168.1.50 tools/venv/bin/python -I tools/check.py examples/<senin-tv>/debloat.txt
```

Listelerin çoğu güncelleme servisini açık bırakıyor. Bazı listeler kapatıyor. O TV'lerde güncelleme ancak servisi tekrar açınca geliyor.

## Uygulama içi reklamlar

Ana ekran temizliği uygulamaların kendi içindeki reklamları kaldırmıyor. Android 9 ve üstünde Private DNS'i reklam engelleyen bir sunucuya yönlendirmek bir kısmını kesiyor. Claude bunu isteğe bağlı olarak öneriyor:

```
settings put global private_dns_specifier dns.adguard-dns.com
settings put global private_dns_mode hostname
```

Geri almak için: `settings put global private_dns_mode opportunistic`.

Bedeli:

- YouTube reklamları geçmiyor.
- TV'nin bütün DNS sorgularını o sunucu görüyor.
- Modeminde Pi-hole ya da AdGuard Home gibi bir filtre varsa Private DNS onu atlıyor.

## Güvenlik

Ağ üzerinden ADB şifresiz. `ro.adb.secure=0` olan bazı yazılımlarda ağdaki herhangi bir cihaz izin sormadan TV'de komut çalıştırabiliyor. Yeni ana ekran hata ayıklama kapalıyken de çalışıyor, o yüzden iş bitince:

1. USB, Ağ/ADB ve Kablosuz hata ayıklamayı kapat, USB hata ayıklama yetkilerini iptal et.
2. TV'yi yeniden başlat.
3. `python3 tools/adb-closed.py <TV IP>` portun kapandığını söylesin.

Kablosuz hata ayıklamanın kendi portu var ve her açılışta değişiyor. Claude kapatmadan önce o portu not edip ayrıca kontrol ediyor. Bir ayarın kapalı görünmesi ya da bağlantının kopmuş olması portun kapandığını kanıtlamıyor. Sadece aracın "kapalı" demesine güven.
