# 04 · Güncelleme sonrası

Sistem güncellemesi temizlenmiş sistemin üstüne kuruluyor. Kapattığın paketler geri gelebiliyor, ana ekran fabrika ayarına dönebiliyor, hiçbir listede olmayan yeni uygulamalar çıkabiliyor.

Önce TV'de hata ayıklamayı tekrar aç ([README, 3. adım](../README.md#3-tvde-usb-hata-ayıklamayı-aç)). Sonra repo klasöründe Claude Code'u varsayılan izin modunda aç ve yaz:

```
TV bir sistem güncellemesi aldı. TV modelim: <MARKA MODEL>, IP adresi: <192.168.x.x>. tools/check.py ile examples/<senin-tv>/debloat.txt listesini TV'yle karşılaştır ve neyin geri geldiğini göster. Ben onaylarsam --fix ile tekrar kapat. Ana ekranın hâlâ yeni uygulamada olduğunu TV'den ekran görüntüsüyle kontrol et ve listede olmayan yeni hazır uygulamalar varsa bana sor. Bitince ağ ADB'sini kapatmama yardım et.
```

Bu promptu bir TV'de denemedik. Reponun kendi kontrol aracını çağırıyor.

## Elle

```bash
TV_HOST=<TV'nin IP adresi> tools/venv/bin/python -I tools/check.py examples/<senin-tv>/debloat.txt
TV_HOST=<TV'nin IP adresi> tools/venv/bin/python -I tools/check.py --fix examples/<senin-tv>/debloat.txt
```

İlki sadece karşılaştırıyor (0: liste uyuyor, 1: bir şey geri gelmiş, 2: kontrol yapılamadı). İkincisi geri geleni tekrar kapatıyor ve ana ekranı yeni uygulamaya geri veriyor.

Windows'ta (PowerShell) önce `$env:TV_HOST="<TV'nin IP adresi>"`, sonra `tools\venv\Scripts\python.exe -I tools\check.py examples\<senin-tv>\debloat.txt`. Çıkış kodlarının anlamı ve haftalık kontrol: [docs/firmware-updates.md](https://github.com/Gathin23/cleanyourtv/blob/main/docs/firmware-updates.md).

Bitince yine ağ ADB'sini kapat: [02-bitince-kapat.md](02-bitince-kapat.md).
