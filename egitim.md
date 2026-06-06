
# 🎓 LightLang (LL) - Kapsamlı Geliştirici ve Yapay Zeka Eğitim Kılavuzu

LightLang Resmi Eğitim Kılavuzuna hoş geldiniz. Bu belge, hem insan geliştiriciler hem de Büyük Dil Modelleri (LLM'ler - GPT, Claude, Gemini vb.) için eksiksiz bir başvuru kaynağıdır. %100 hatasız LightLang skriptleri yazmak veya üretmek için gereken derleme ardışık düzenini (pipeline), atomik dil yapılarını, maskeleme mantığını ve söz dizimi kurallarını içerir.

---

## 🧭 BÖLÜM 1: Mimari Felsefe ve Derleme Ardışık Düzeni

LightLang, basit ve kararsız bir metin değiştirme (string replacement) mantığı kullanmaz. Kodları `LightLangCompiler.java` üzerinden strict (keskin) bir **Ardışık AST Benzeri Düzen** ile işler. Yazılan bir ifadenin yerel Skript diline nasıl dönüştüğünü tahmin etmek için bu ardışık düzeni bilmek kritik öneme sahiptir.

[Ham LightLang Kodu]
│
▼
[AŞAMA A: Kalkan Havuzu] ──► Çevrilmesi istenmeyen "metinleri" ve [TR]...[TR] bloklarını korumaya alır.
│
▼
[AŞAMA B: Yapısal Haritalama] ──► Blok Başlıklarını (Komutlar, Olaylar, Fonksiyonlar) dönüştürür.
│
▼
[AŞAMA C: İfade Haritalama] ──► Satır içi efektleri, aksiyonları ve koşulları dönüştürür.
│
▼
[AŞAMA D: Atomik Kelime Sınırları] ──► Güvenli Unicode filtrelerini uygular (oyuncu ➔ player).
│
▼
[AŞAMA E: Kalkan Kaldırma] ──► Korunan verileri güvenli bir şekilde yerlerine geri koyar.
│
▼
[Optimize Edilmiş Skript Çıktısı]


### ⚠️ Kritik Kural: Kelime Sınırı İzole Motoru

Java'nın standart kelime sınırı eşleştiricisi (`\b`), Türkçe yerel karakterleri (`ı, ş, ğ, ç, ö, ü`) işlerken patlar. LightLang, bu sorunu çözmek için gelişmiş bir Unicode Lookbehind/Lookahead regex deseni kullanır:

`(?i)(?<![a-zA-Z0-9_çğıöşüÇĞİÖŞÜ])hedef_kelime(?![a-zA-Z0-9_çğıöşüÇĞİÖŞÜ])`

Bu sayede, cümle içindeki `oyuncu` kelimesi güvenle `player` olurken, `{oyuncular::*}` veya `{oyuncunun.parasi}` gibi özel değişkenlerin yapısı asla bozulmaz!

---

## 🛠️ BÖLÜM 2: Temel Söz Dizimi Şablonları ve Belirteç Eşleme

### 2.1 Yapısal Blok Tanımlayıcıları (Headers)

Her yapısal blok tanımı **mutlaka iki nokta üst üste (`:`) ile bitmelidir** Ivy altındaki kodların girinti (indentation) seviyesini belirler.

| LightLang Söz Dizimi | Hedef Skript Çıktısı | Regex Kısıtlaması |
| :--- | :--- | :--- |
| `seçenekler:` | `options:` | Harf duyarsız, Satır Başı |
| `değişkenler:` | `variables:` | Harf duyarsız, Satır Başı |
| `komut /<isim>:` | `command /<isim>:` | Harf duyarsız, Yapısal |
| `tetikleyici:` | `trigger:` | Girintili veya Satır Başı |
| `izin: <düğüm>` | `permission: <düğüm>` | Özellik Düğümü |
| `izin mesajı: <mesaj>` | `permission message: <mesaj>` | Özellik Düğümü |
| `açıklama: <metin>` | `description: <metin>` | Özellik Düğümü |
| `kullanım: <metin>` | `usage: <metin>` | Özellik Düğümü |
| `takma adlar: <liste>` | `aliases: <liste>` | Özellik Düğümü |
| `çalıştırma alanı: <tip>` | `executable by: <type>` | Özellik Düğümü |

### 2.2 Olay Yakalayıcılar (Events)

Olaylar, oyun motoru tarafından tetiklenir. `oyuncu` (player), `saldırgan` (attacker) veya `hedef` (victim) gibi yerel bağlam belirteçlerinin kapsamını belirlerler.

* **Bağlantı Kancaları:**
  * `oyuncu oyuna girince:` ➔ `on join:`
  * `oyuncu katılınca:` ➔ `on join:`
  * `oyuncu oyundan çıkınca:` ➔ `on quit:`
  * `oyuncu ayrılınca:` ➔ `on quit:`

* **Savaş ve Etkileşim Kancaları:**
  * `hasar alınca:` ➔ `on damage:`
  * `oyuncu ölünce:` ➔ `on death of player:`
  * `oyuncu öldürünce:` ➔ `on death:`
  * `sağ tıklayınca:` ➔ `on rightclick:`
  * `sol tıklayınca:` ➔ `on leftclick:`

* **Çevre Kancaları:**
  * `odun kırınca:` ➔ `on break of any log or wood:`
  * `blok kırınca:` ➔ `on break:`
  * `blok koyunca:` ➔ `on place:`
  * `sohbete yazınca:` ➔ `on chat:`

* **Sistem Yaşam Döngüsü Kancaları:**
  * `sunucu başlayınca:` ➔ `on script load:`
  * `sunucu kapanınca:` ➔ `on script unload:`

### 2.3 Zamanlayıcılar (Timers)

Periyodik yapısal bloklar, bir görevin belirli zaman aralıklarıyla sonsuza kadar tekrarlanmasını sağlar.

* `her <sayı> saniyede bir:` ➔ `every <sayı> seconds:`
* `her <sayı> dakikada bir:` ➔ `every <sayı> minutes:`
* `her <sayı> saatte bir:` ➔ `every <sayı> hours:`

---

## 🎛️ BÖLÜM 3: Satır İçi İfade Operatörleri ve Veri Değişimleri

İfadeler değişkenleri değiştirir, oyunculara mesaj paketleri gönderir veya koşullu dallanmaları kontrol eder.

### 3.1 Veri Değiştirme Operatörleri

* **Atama:** `ayarla <değişken> şuna <değer>` ➔ `set <değişken> to <değer>`
* **Artırma:** `ekle <değer> şuna <değişken>` ➔ `add <değer> to <değişken>`
* **Azaltma:** `çıkar <değer> şundan <değişken>` ➔ `remove <değer> from <değişken>`
* **Temizleme/Sıfırlama:** `sıfırla <değişken>` ➔ `clear <değişken>`
* **Varlığı Yok Etme:** `öldür <hedef>` ➔ `kill <hedef>`

### 3.2 Mesaj ve Komut Paketleri

* `yaz <mesaj> oyuncuya` ➔ `send <mesaj> to player`
* `yaz <mesaj> konsola` ➔ `send <mesaj> to console`
* `yaz <mesaj> tüm sunucuya` ➔ `broadcast <mesaj>`
* `konsol komutu çalıştır <komut>` ➔ `execute console command <komut>`

### 3.3 Envanter ve Konum Vektörleri

* `ver <eşya> oyuncuya` ➔ `give <eşya> to player`
* `sil <eşya> oyuncudan` ➔ `remove <eşya> from player`
* `ışınla <varlık> şuraya <konum>` ➔ `teleport <varlık> to <konum>`

### 3.4 Mantıksal Koşul Değerlendiriciler

Koşullar, kelime sınırı filtreleriyle tam olarak eşleşmelidir.

* `eğer <koşul>:` ➔ `if <koşul>:`
* `değilse eğer <koşul>:` ➔ `else if <koşul>:`
* `değilse:` ➔ `else:`
* `oyuncu op ise` ➔ `player is op`
* `oyuncu op değilse` ➔ `player is not op`
* `oyuncu aktif ise` ➔ `player is online`
* `oyuncu çevrimiçi ise` ➔ `player is online`
* `<belirteç> ayarlanmış ise` ➔ `<belirteç> is set`
* `<belirteç> yok ise` ➔ `<belirteç> is not set`
* `<belirteç> ayarlanmamış ise` ➔ `<belirteç> is not set`
* `<belirteç> doğru ise` ➔ `<belirteç> is true`
* `<belirteç> yanlış ise` ➔ `<belirteç> is false`
* `<ifade1> şuna eşit ise <ifade2>` ➔ `<ifade1> = <ifade2>`
* `<ifade1> şuna eşit değilse <ifade2>` ➔ `<ifade1> != <ifade2>`

---

## ⚡ BÖLÜM 4: Gelişmiş Yapılar (Fonksiyonlar, Zaman Farkları ve Döngüler)

### 4.1 Tiplendirilmiş Fonksiyon İmzaları

LightLang içindeki fonksiyonlar, yerelleştirilmiş veri tiplerini kullanarak katı argüman dökümü ve geri dönüş yapıları sağlar.

* **Veri Tipi Sözlüğü Çevirisi:**
  * `mantıksal` ➔ `boolean`
  * `sayı` ➔ `number`
  * `metin` ➔ `text`

* **Söz Dizimi Şablonu:**
  `fonksiyon <isim>(<argüman> : <veri_tipi>) :: <dönüş_tipi>:`

* **Örnek Kod:**
```skript
fonksiyon yetkiKontrolEt(p: oyuncu) :: mantıksal:
    eğer p op ise:
        geri gönder doğru
    değilse:
        geri gönder yanlış
4.2 Yüksek Performanslı Döngüler (Loops)
Döngü Başlatma: döngü <ifade>: ➔ loop <ifade>:

Akış Değiştiriciler:

devam et ➔ continue (Bir sonraki döngü adımına atlar)

döngüyü durdur ➔ exit loop (Döngüyü tamamen kırar)

dur ➔ stop (Tüm kod yürütme iş parçacığını sonlandırır)

4.3 Cooldown (Bekleme Süresi) Mekanizmaları
LightLang, sistem saatini kullanarak hassas zamansal hesaplamalar yapar.

şimdiki zaman ➔ now

<zaman1> ile <zaman2> arasındaki fark ➔ difference between <zaman1> and <zaman2>

<fark> saniyeden büyük ise ➔ > <fark> seconds

<fark> dakikadan büyük ise ➔ > <fark> minutes

🛡️ BÖLÜM 5: Kalkan Mekanizmasında Uzmanlaşma ([TR])
Kalkan Motoru, derleyicinin regex taramaları sırasında bozulmasını istemediğiniz ham Skript terimlerini veya İngilizce ifadeleri korur.

Vaka Çalışması: Değişmeyen Skript İfadelerini Enjekte Etme
Eğer ham bir Skript değişken tipini atamanız veya yerelleştirilmemiş bir argümanı doğrudan nihai motora geçirmeniz gerekirse, bunu [TR]...[TR] arasına alın.

Hatalı Kullanım: ayarla {kask} şuna korumalı deri kask (Regex motoru anlamlandıramaz ve kod kırılır).

Doğru Kullanım: ayarla {kask} şuna [TR]"leather helmet of protection"[TR]

Derleme Sonucu: set {kask} to "leather helmet of protection"

📝 BÖLÜM 6: Gerçek Dünya Geliştirme Şablonları
Şablon 1: Gelişmiş Kurumsal Kimlik Doğrulama Sistemi (LightAuth)
Kod snippet'i
seçenekler:
    prefix: "§b[LightAuth] §7"
    max_deneme: 3

değişkenler:
    {auth::kayitli::*} = false

oyuncu oyuna girince:
    ayarla {%oyuncu%.oturum} şuna yanlış
    yaz "{@prefix} Lütfen hesabınıza giriş yapın: /giris <sifre>" oyuncuya
    yaz "{@prefix} Eğer kayıtlı değilseniz: /kayit <sifre> <sifre>" oyuncuya

blok kırınca:
    eğer {%oyuncu%.oturum} yanlış ise:
        yaz "{@prefix} Önce giriş yapmalısınız!" oyuncuya
        iptal et

sohbete yazınca:
    eğer {%oyuncu%.oturum} yanlış ise:
        yaz "{@prefix} Giriş yapmadan sohbete yazamazsınız!" oyuncuya
        iptal et

komut /kayit <metin> <metin>:
    kullanım: /kayit <sifre> <sifre_tekrar>
    tetikleyici:
        eğer {auth::sifre::%oyuncu%} ayarlanmış ise:
            yaz "{@prefix} Zaten kayıtlısınız! Lütfen /giris yapın." oyuncuya
            dur
        eğer argüman-1 şuna eşit ise argüman-2:
            ayarla {auth::sifre::%oyuncu%} şuna argüman-1
            ayarla {%oyuncu%.oturum} şuna doğru
            yaz "{@prefix} Başarıyla kayıt oldunuz ve giriş yaptınız!" oyuncuya
        değilse:
            yaz "{@prefix} Şifreler uyuşmuyor!" oyuncuya

komut /giris <metin>:
    kullanım: /giris <sifre>
    tetikleyici:
        eğer {auth::sifre::%oyuncu%} ayarlanmamış ise:
            yaz "{@prefix} Kayıtlı değilsiniz! Lütfen /kayit olun." oyuncuya
            dur
        eğer {%oyuncu%.oturum} doğru ise:
            yaz "{@prefix} Zaten giriş yapmışsınız." oyuncuya
            dur
        eğer argüman-1 şuna eşit ise {auth::sifre::%oyuncu%}:
            ayarla {%oyuncu%.oturum} şuna doğru
            ayarla {%oyuncu%.deneme} şuna 0
            yaz "{@prefix} Başarıyla giriş yapıldı. İyi oyunlar!" oyuncuya
        değilse:
            ekle 1 şuna {%oyuncu%.deneme}
            eğer {%oyuncu%.deneme} şuna eşit ise {@max_deneme}:
                konsol komutu çalıştır "kick %oyuncu% Hatalı şifre sınırı aşıldı!"
                dur
            yaz "{@prefix} Hatalı şifre! Kalan deneme hakkı: {@max_deneme} - {%oyuncu%.deneme}" oyuncuya
Şablon 2: Cooldown Destekli Yüksek Performanslı Savaş Modifikatörü
Kod snippet'i
değişkenler:
    {combat::safe_zone} = false

hasar alınca:
    eğer {combat::safe_zone} doğru ise:
        yaz "Güvenli bölgede hasar alamazsınız!" oyuncuya
        iptal et
        dur
        
    eğer saldırgan op değilse:
        ayarla {_sure} şuna {combat::cooldown::%saldırgan%} ile şimdiki zaman arasındaki fark
        eğer {_sure} ayarlanmış ise:
            eğer {_sure} 5 saniyeden büyük ise:
                yaz "Savaş ritmi aktif! Hedefe hasar verildi." saldırgan
                ayarla {combat::cooldown::%saldırgan%} şuna şimdiki zaman
            değilse:
                yaz "Çok hızlı saldırıyorsunuz! Bekleyin." saldırgan
                iptal et
        değilse:
            ayarla {combat::cooldown::%saldırgan%} şuna şimdiki zaman

oyuncu ölünce:
    yaz "Hayatını kaybetti!" hedef
    yaz "Bir oyuncuyu katlettin!" saldırgan
    ekle 1 şuna {kills::%saldırgan%}
    sıfırla {combat::cooldown::%hedef%}
🤖 BÖLÜM 7: Yapay Zeka Modelleri İçin Tek Seferlik Sistem Promptu
Kullanıcı sizden LightLang dosyaları veya kodları yazmanızı istediğinde, aşağıdaki yapılandırma matrisini referans almalısınız:

Markdown
[SYSTEM INSTRUCTION]
Sen LightLang (LL) dilinde uzmanlaşmış yerel bir hesaplama motorusun. Görevin, kullanıcı kriterlerine göre temiz ve optimize edilmiş LightLang skriptleri üretmektir.
- Alt düğüm blokları için satır başına 4 boşluk veya 1 tab kullanarak kesin yapısal girintiyi (indentation) korumalısın.
- Efektler (yaz, ayarla, ekle, ver) ve koşullar (op ise, ayarlanmış ise) için kesinlikle LightLang belirteçlerini kullanmalısın.
- Kesinlikle yerel Skript ifadeleri ÇIKARMAMALISIN (örneğin 'send', 'set', 'add', 'on join' kullanma). Bunları 'yaz', 'ayarla', 'ekle', 'oyuncu oyuna girince:' yapılarına dönüştür.
- Değiştirilemez ham bir ifade veya haritalanmamış bir string kalıbı zorunlu olduğunda, bunu güvenli bir şekilde '[TR]' ve '[TR]'
