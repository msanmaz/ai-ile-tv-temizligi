# 03 · Geri al

Önce TV'de hata ayıklamayı tekrar aç ([README, 3. adım](../README.md#3-tvde-usb-hata-ayıklamayı-aç)). Sonra repo klasöründe Claude Code'u varsayılan izin modunda aç:

```bash
cd cleanyourtv
claude --permission-mode default
```

```
TV'yi eski haline döndür. TV modelim: <MARKA MODEL>, IP adresi: <192.168.x.x>. Uyguladığımız liste: examples/<senin-tv>/debloat.txt. tools/restore.sh ile geri al ve ana ekranın eski haline döndüğünü TV'den ekran görüntüsüyle göster. Yeni ana ekran uygulamasını nasıl kaldıracağımı da söyle. Bitince ağ ADB'sini kapatmama yardım et.
```

Bu promptu bir TV'de denemedik. Reponun kendi geri alma aracını çağırıyor.

Modelin repoda yoksa liste yolu yerine Claude'un ilk kurulumda kaydettiği listenin ve geri alma betiğinin yolunu yaz.

## Elle

```bash
TV_HOST=<TV'nin IP adresi> tools/restore.sh examples/<senin-tv>/debloat.txt
```

Google TV'de sona fabrika ana ekranının paket adını ekle:

```bash
TV_HOST=<TV'nin IP adresi> tools/restore.sh examples/<senin-tv>/debloat.txt com.google.android.apps.tv.launcherx
```

Windows'ta (PowerShell), Google TV için sona `-Stock com.google.android.apps.tv.launcherx` ekleyerek:

```powershell
powershell -ExecutionPolicy Bypass -File tools\restore.ps1 -TvHost <TV'nin IP adresi> examples\<senin-tv>\debloat.txt
```

Geri alma, `tools/disable.sh` çalışmadan önce `~/.cleanyourtv/` klasörüne yazılan kaydı tekrar oynatıyor. Kayıt yoksa (eski bir çalıştırma ya da silinmiş bir kayıt) betik bunu söylüyor ve listedeki her şeyi açıyor.

Yeni ana ekran uygulaması TV'de kalıyor. İstemiyorsan Ayarlar → Uygulamalar sayfasından kaldır. Ayrıntılar: [docs/restore.md](https://github.com/Gathin23/cleanyourtv/blob/main/docs/restore.md).

Bitince yine ağ ADB'sini kapat: [02-bitince-kapat.md](02-bitince-kapat.md).
