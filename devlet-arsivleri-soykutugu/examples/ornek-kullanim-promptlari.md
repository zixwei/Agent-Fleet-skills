# Hazır Kullanım İstemleri (Kopyala – Yapıştır Prompt Şablonları)

Bu dosyadaki hazır istemleri (promptları), araştırma aşamanıza göre doğrudan kopyalayıp **Google Antigravity** (veya beceriyi yüklediğiniz diğer yapay zekâ ajanlarına) gönderebilirsiniz.

---

## 1. Ana Başlangıç İstemi: e-Devlet Belgesi + Canlı Chrome DevTools MCP Taraması

*Kullanım zamanı: e-Devlet'ten Alt-Üst Soy PDF belgenizi indirdiğinizde ve Chrome'da `katalog.devletarsivleri.gov.tr` oturumunuzu açtığınızda.*

```markdown
Sana e-Devlet'ten aldığım "Alt-Üst Soy Bilgisi" belgemi yükledim. Chrome tarayıcımda `chrome://inspect/#remote-debugging` ayarını etkinleştirdim ve `https://katalog.devletarsivleri.gov.tr` adresine kendi e-Devlet şifremle giriş yaptım. (Bilinen aile lakaplarımız / ek bilgilerimiz: [...varsa buraya yazın...])

Lütfen `devlet-arsivleri-soykutugu` becerisini kullanarak açık olan Devlet Arşivleri Kataloğu sekmeme bağlan ve şu adımları uygula:

1. **4 Temel İpucu ve Kütük Duvarı Analizi:**
   - Baba ve anne tarafımdaki en eski atalarımı, baba/anne adlarını, doğum yıllarını (Milâdî → Hicrî/Rûmî karşılıklarıyla), Cilt–Hane–Sıra numaralarını ve köylerimizin Cumhuriyet öncesi **eski Osmanlıca adlarını** ve bağlı oldukları **eyalet / sancak / kaza / nahiye** zincirini (komşu kaza/nahiye geçiş ihtimalleriyle birlikte) çıkar.

2. **Bütçe Koruyan "Önce Fihrist" Katalog Taraması (1830–1500):**
   - Katalogda `NFS.d` (1830–1880 Nüfus), `ML.VRD.TMT.d` (1844–1845 Temettuat), `A.{RSK.d` (1835–1838 İmam-Muhtar), `MŞH.ŞSC.d` (Şer'iyye Sicilleri), `MAD.d` (Avârız/Mukataa) ve `TT.d / TK.GM.d` (15.–16. yy. Tapu Tahrir) fonlarında köyümü ve bağlı olduğu kaza/nahiye toplu defterlerini ara.
   - Güvenlik duvarını (`HTTP 418`) tetiklememek için paralel istek atmadan, önce defterlerin **Fihrist (`Mi=1..5`)** sayfalarını kontrol et; yanlış dönem veya farklı nahiyeye ait defterleri ele; doğru defterlerde **Poz (`Mi`) ≠ Varak (`vr.`)** farkını hesaplayarak köyümün tam hangi poz aralığında başladığını tespit et.

3. **Kanıtlı Eşleştirme ve Nokta Atışı İndirme Listesi:**
   - Önizlemeden okunabilen kayıtları kuşak matematiğiyle (`+ΔYıl` yaş farkı ve `1/N` hane filtresi) analiz et; her bulguyu `[KESİN KANIT]`, `[GÜÇLÜ EŞLEŞME]` veya `[İSİM İZİ]` olarak etiketle, okunamayan yerleri asla tahminle doldurma.
   - e-Satış sepetinden gereksiz tüm defteri değil, **sadece doğru sayfa aralıklarını** indirebilmem için tam `Fon Kodu | Defter No | Tarih | Varak No | Dijital Poz (Mi) Aralığı | İçerik` tablosunu ver.
```

---

## 2. İndirilmiş `.zip / .jpg` Klasörü İstemi: 1:1 Piksel Kesitli Kesin Okuma

*Kullanım zamanı: Devlet Arşivleri e-Satış sepetinden doğru sayfa aralıklarını `.zip` veya `.jpg` olarak bilgisayarınızdaki bir klasöre indirdiğinizde.*

```markdown
Devlet Arşivleri'nden indirdiğim filigransız yüksek çözünürlüklü `.zip / .jpg` arşiv belgelerini çalışma klasörüme koydum.

Lütfen `devlet-arsivleri-soykutugu` becerisindeki `scripts/crop_and_verify.py` aracını kullanarak:
1. Klasördeki tüm sayfaların envanterini çıkar; her sayfanın başlığındaki `Karye-i ... tâbi-i ...` ibaresini kontrol ederek yanlış/komşu köy sayfası karışmışsa kanıtıyla ayır.
2. Doğru köy sayfalarını Sağ (`R`) ve Sol (`L`) varaklara ve yatay okuma şeritlerine böl; tahmin yürütmeden, her kritik hane ve şerh satırından **1:1 doğal piksel yakınlaştırma kesiti (crop)** alarak oku.
3. Sadece düz üst soyumu değil; aynı lakabı taşıyan **amca, kardeş ve yan kol hanelerini**, doğum (`tevellüd`), vefat (`fevt`) ve askerlik (`reft be-askeriyye / redîf`) şerhlerini de eksiksiz çıkar.
4. Sayımlar arası yaş artışlarını (`+ΔYıl`) çapraz doğrula ve her halkanın kanıt seviyesini (`[KESİN] / [GÜÇLÜ] / [İSİM İZİ]`) belirterek kapsamlı soy kütüğü raporumu oluştur.
```

---

## 3. Göçmen ve Mübadil Aileler İçin Özel Tarama İstemi

*Kullanım zamanı: Ataları Balkanlar, Ege/Akdeniz Adaları, Girit, Kırım veya Kafkasya'dan göç etmiş / mübadeleyle gelmiş aileler için.*

```markdown
Atalarımız [Selanik / Drama / Girit / Bulgaristan-Tuna / Üsküp / Kafkasya vb.] bölgesinden gelerek [İl / İlçe / Köy] yerleşimine iskân edilmiş. e-Devlet Alt-Üst Soy belgemi (ve varsa İsim Denklik bilgimi) ekledim; Chrome'da `https://katalog.devletarsivleri.gov.tr` oturumum açık.

Lütfen `devlet-arsivleri-soykutugu` göçmen ve mübadil rehberini izleyerek:
1. 1923–1925 mübadelesi ise Cumhuriyet Arşivi `130-16-13-2` (*Tasfiye Talepnameleri*) ve `272` (*Toprak İskân*) fonlarında; 93 Harbi, Balkan Harbi veya Kafkas göçü ise Osmanlı Arşivi `DH.SN..THR.`, `DH.İ.UM`, `DH.MKT.`, `Y..PRK.KOM.` ve `A.}MKT.NZD.` fonlarında aile reisimizin adı ve iskân yeriyle tarama yap.
2. Geldikleri tam kaza ve köy adını netleştirdikten sonra, Osmanlı Arşivi'ndeki **1830–1875 Balkan/Adalar Nüfus (`NFS.d`) ve 1845 Temettuat (`ML.VRD.TMT.d`)** defterlerinde o köyün kaydını ara ve indirmem gereken tam defter/poz numaralarını çıkar.
```

---

## 4. Avrupa *Ortsgeschichte* Usulünde Köy ve Aile Tarihi Kitabı İstemi

*Kullanım zamanı: Soy kütüğü ve köy belgeleri toplandıktan sonra kalıcı bir aile/köy monografisi hazırlatmak istediğinizde.*

```markdown
Elde ettiğimiz tüm birincil arşiv belgelerini, filigransız hane kayıtlarını ve katalog bulgularını kullanarak; Avrupa yerel ve mikro-tarihçiliği (*Ortsgeschichte / Microstoria*) disipliniyle köyümüzün ve ailemizin tarihini çok bölümlü, baskıya hazır bir monografi olarak yaz.

Yazım kuralları:
- Tüm yer, kurucu sülale, şahıs ve kurum adlarının belgede geçen özgün Osmanlıca yazılışlarını parantez içinde göster; her olayı ve rakamı tam arşiv künyesine (`Fon, Defter/Dosya, Varak/Poz, Hicrî/Milâdî Tarih`) bağla.
- Köyün coğrafyasını, isim kökenini, 16.–19. yüzyıl idari/vergi evrimini, kurucu ocaklarını, salgın ve kıtlıklarını, dini/tasavvufi ve gündelik yaşamını, Osmanlı'dan Çanakkale ve İstiklâl Harbi'ne askerlerini/şehitlerini ve Cumhuriyet dönemi göç serüvenini katmanlı biçimde ele al; orijinal belge kesitlerini ilgili bölümlere tıpkıbasım levha olarak yerleştir.
```
