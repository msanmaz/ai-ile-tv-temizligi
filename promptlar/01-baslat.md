# 01 · Başlat

Repoyu klonladığın klasörde Claude Code'u varsayılan izin modunda aç:

```bash
git clone https://github.com/Gathin23/cleanyourtv
cd cleanyourtv
claude --permission-mode default
```

## Prompt

```
@PROMPT.md uygula. TV modelim: <MARKA MODEL>
```

24 Ekim 2026'daki kaydımızda yazdığımız, ilk kurulum iznine kadar denedik:

```
@PROMPT.md uygula. TV modelim: Beko B43B880B
```

- `@PROMPT.md`, reponun içindeki talimat dosyasını Claude'a veriyor. Dosyanın tamamını okuyor, kopyalayıp yapıştırmana gerek yok.
- Modeli tam yaz: marka ve model kodu. TV'nin arkasındaki etikette ya da Ayarlar → Hakkında sayfasında var. Claude repoda bu adla hazır liste arıyor.
- Talimat dosyası İngilizce. Sen Türkçe yazınca Claude bizim kayıtta Türkçe cevap verdi.

## TV'nin IP adresini biliyorsan

Claude TV'yi ağda arıyor, bulamazsa soruyor. IP'yi biliyorsan baştan ver:

```
@PROMPT.md uygula. TV modelim: <MARKA MODEL>. TV'nin IP adresi: <192.168.x.x>
```

IP adresi TV'nin ağ ayarlarında, bağlı olduğun ağın ayrıntılarında yazıyor. Modeminin bağlı cihazlar listesinde de görünür.

## Modelin listede yoksa

Aynı promptu kullan. Hazır liste yoksa Claude TV'deki paketleri kendisi listeliyor, gruplara ayırıyor ve emin olmadıklarını sana soruyor. İş biraz uzar.

Bitince Claude'dan iki şeyi iste ve sakla:

- uyguladığı liste (`debloat.txt` biçiminde),
- geri alma betiği.

`PROMPT.md` sonda bunları zaten vermesini istiyor. Yine de nereye kaydettiğini sor. Listeni birkaç gün kullandıktan sonra repoya gönderirsen aynı modeli kullanan bir sonraki kişi keşif adımını atlar ([CONTRIBUTING.md](https://github.com/Gathin23/cleanyourtv/blob/main/CONTRIBUTING.md)).

## İzin soruları

Varsayılan modda Claude her komut için izin istiyor. Bizim kayıtta ilk izin sorusu repodaki Python aracı için bir sanal ortam kurmak içindi ([ornek/](../ornek/)). Sorulduğunda:

- Komutu oku. Ne yaptığını anlamadıysan "1. Yes" demeden önce Claude'a sor.
- "Switch to auto mode" seçeneğini ilk denemede seçme. Otomatik modda Claude sormadan devam eder.
