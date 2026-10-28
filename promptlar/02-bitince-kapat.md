# 02 · Bitince ADB'yi kapat

Ağ üzerinden ADB şifresiz çalışıyor. Yeni ana ekranın çalışması için açık kalması gerekmiyor. `PROMPT.md` bu kapatma adımını Claude'a sonda zaten yaptırıyor. Atlandıysa ya da emin olmak istiyorsan, aynı Claude Code oturumunda:

```
İş bitti. Ağ ADB'sini kapatmama yardım et. TV'de hangi hata ayıklama ayarlarını kapatacağımı sırayla söyle ve USB hata ayıklama yetkilerini iptal ettir. Kablosuz hata ayıklama açıksa portunu kapatmadan önce not al ve kapattıktan sonra tools/adb-closed.py ile kontrol et. TV'yi yeniden başlattıktan sonra 5555 portunu da tools/adb-closed.py ile kontrol et. Sadece aracın portun kapalı olduğunu söylemesini kabul et.
```

Bu promptu bir TV'de denemedik. Reponun kendi aracını çağırıyor.

## Elle

1. TV'de Geliştirici seçenekleri: USB hata ayıklama, Ağ/ADB hata ayıklama ve Kablosuz hata ayıklama, hangileri varsa kapat. USB hata ayıklama yetkilerini iptal et.
2. TV'yi yeniden başlat.
3. Repo klasöründe:

```bash
python3 tools/adb-closed.py <TV'nin IP adresi>
echo $?    # 0: port kapalı, 1: port hâlâ açık, 2: karar verilemedi
```

Windows'ta `python3` yerine `python`, sonuç için `echo $LASTEXITCODE`.

2 portun kapandığını göstermez. TV'den cevap gelmemiş demek: TV yeniden başlıyor, uykuda ya da IP adresi değişmiş olabilir. Biraz bekleyip tekrar dene. macOS'ta terminal uygulamanın yerel ağ izni yoksa da bu sonuç çıkabiliyor (Sistem Ayarları → Gizlilik ve Güvenlik → Yerel Ağ).
