# 📖 LightLang Söz Dizimi (Syntax) ve Kurallar Kılavuzu

Yapay zeka modellerinin ve geliştiricilerin hatasız LightLang kodu yazabilmesi için tüm yapısal kurallar, olaylar (events) ve ifadeler (expressions) aşağıda listelenmiştir.

## 1. Yapısal Bloklar (Structural Rules)

Bu kurallar satır başlarında veya ana blok tanımlamalarında kullanılır. Sonlarında mutlaka `:` bulunmalıdır.

| LightLang Yapısı | Dönüştürüldüğü Skript Karşılığı | Açıklama |
| :--- | :--- | :--- |
| `seçenekler:` | `options:` | Script ayarları ve sabitleri alanı |
| `değişkenler:` | `variables:` | Global değişken tanımlama alanı |
| `komut /<isim> <argümanlar>:` | `command /<isim> <argümanlar>:` | Yeni bir oyun içi komut oluşturur |
| `tetikleyici:` | `trigger:` | Komutun çalıştıracağı aksiyon bloğu |
| `izin: <yetki>` | `permission: <yetki>` | Komut için gerekli permission |
| `izin mesajı: <mesaj>` | `permission message: <mesaj>` | Yetkisi olmayana gidecek mesaj |
| `açıklama: <metin>` | `description: <metin>` | Komut açıklaması |
| `kullanım: <metin>` | `usage: <metin>` | Hatalı kullanımda gösterilecek mesaj |
| `takma adlar: <adlar>` | `aliases: <adlar>` | Komutun alternatif kısaltmaları |
| `çalıştırma alanı: <player/console>` | `executable by: <player/console>` | Komutun çalıştırılabileceği yer |
| `eğer <koşul>:` | `if <koşul>:` | Şartlı blok başlatır |
| `değilse eğer <koşul>:` | `else if <koşul>:` | Alternatif şart bloğu |
| `değilse:` | `else:` | Hiçbir şart uymadığında çalışacak blok |
| `döngü <ifade>:` | `loop <ifade>:` | Döngü bloğu başlatır |

### 🛠️ Gelişmiş Fonksiyon Tanımlama
LightLang dinamik fonksiyon imzasını destekler. Dönüşüm otomatik yapılır:
* **Kullanım:** `fonksiyon <isim>(<argümanlar>) :: <veri_tipi>:`
* **Örnek:** `fonksiyon canUseSpeed(p: oyuncu) :: mantıksal:`
* **Derleme Sonucu:** `function canUseSpeed(p: player)::boolean:`

---

## 2. Olaylar (Events)

| LightLang Olay Tetikleyicisi | Skript Karşılığı |
| :--- | :--- |
| `oyuncu oyuna girince:` VEYA `oyuncu katılınca:` | `on join:` |
| `oyuncu oyundan çıkınca:` VEYA `oyuncu ayrılınca:` | `on quit:` |
| `hasar alınca:` | `on damage:` |
| `oyuncu ölünce:` | `on death of player:` |
| `oyuncu öldürünce:` | `on death:` |
| `odun kırınca:` | `on break of any log or wood:` |
| `blok kırınca:` | `on break:` |
| `blok koyunca:` | `on place:` |
| `sohbete yazınca:` | `on chat:` |
| `sağ tıklayınca:` | `on rightclick:` |
| `sol tıklayınca:` | `on leftclick:` |
| `sunucu başlayınca:` | `on script load:` |
| `sunucu kapanınca:` | `on script unload:` |

### Periyodik Zamanlayıcılar (Timers)
* `her <sayı> saniyede bir:` ➡️ `every <sayı> seconds:`
* `her <sayı> dakikada bir:` ➡️ `every <sayı> minutes:`
* `her <sayı> saatte bir:` ➡️ `every <sayı> hours:`

---

## 3. İfadeler ve Aksiyonlar (Expressions & Effects)

Satır içi kontroller, zaman işlemleri ve oyuncu aksiyonları için kullanılan kurallardır.

### Akış Kontrolü ve Zaman
* `dur` ➡️ `stop`
* `devam et` ➡️ `continue`
* `döngüyü durdur` ➡️ `exit loop`
* `geri gönder <değer>` ➡️ `return <değer>`
* `bekle <sayı> saniye` ➡️ `wait <sayı> seconds`
* `bekle <sayı> dakika` ➡️ `wait <sayı> minutes`
* `şimdiki zaman` ➡️ `now`
* `<değer1> ile <değer2> arasındaki fark` ➡️ `difference between <değer1> and <değer2>`
* `<zaman> saniyeden büyük ise` ➡️ `> <zaman> seconds`
* `<zaman> dakikadan büyük ise` ➡️ `> <zaman> minutes`

### Durum ve Koşul Kontrolleri
* `argüman-<sayı>` ➡️ `arg-<sayı>`
* `oyuncu op ise` ➡️ `player is op`
* `oyuncu op değilse` ➡️ `player is not op`
* `oyuncu aktif ise` VEYA `oyuncu çevrimiçi ise` ➡️ `player is online`
* `<nesne> ayarlanmış ise` ➡️ `<nesne> is set`
* `<nesne> yok ise` VEYA `<nesne> ayarlanmamış ise` ➡️ `<nesne> is not set`
* `<nesne> doğru ise` ➡️ `<nesne> is true`
* `<nesne> yanlış ise` ➡️ `<nesne> is false`
* `<x> şuna eşit ise <y>` ➡️ `<x> = <y>`
* `<x> şuna eşit değilse <y>` ➡️ `<x> != <y>`

### Efektler ve Dünyayı Değiştirme
* `konsol komutu çalıştır <komut>` ➡️ `execute console command <komut>`
* `yaz <mesaj> oyuncuya` ➡️ `send <mesaj> to player`
* `yaz <mesaj> konsola` ➡️ `send <mesaj> to console`
* `yaz <mesaj> tüm sunucuya` ➡️ `broadcast <mesaj>`
* `ver <eşya> oyuncuya` ➡️ `give <eşya> to player`
* `sil <eşya> oyuncudan` ➡️ `remove <eşya> from player`
* `ışınla <hedef> şuraya <konum>` ➡️ `teleport <hedef> to <konum>`
* `ayarla <değişken> şuna <değer>` ➡️ `set <değişken> to <değer>`
* `ekle <değer> şuna <değişken>` ➡️ `add <değer> to <değişken>`
* `çıkar <değer> şundan <değişken>` ➡️ `remove <değer> from <değişken>`
* `sıfırla <değişken>` ➡️ `clear <değişken>`
* `öldür <hedef>` ➡️ `kill <hedef>`

---

## 4. Atomik Kelime Filtreleri (Unicode Word Boundaries)

Aşağıdaki kelimeler, cümlenin neresinde olursa olsun (eğer tırnak veya `[TR]` içinde değillerse) otomatik olarak İngilizce karşılıklarına dönüştürülür:

* `oyuncu` ➡️ `player`
* `konsol` ➡️ `console`
* `hedef` ➡️ `victim`
* `saldırgan` ➡️ `attacker`
* `iptal et` ➡️ `cancel`
* `dünya` ➡️ `world`
* `konum` ➡️ `location`
* `etkinlik` ➡️ `event`
* `ismi` / `ismı` / `adı` ➡️ `name`
* `mantıksal` ➡️ `boolean`
* `sayı` ➡️ `number`
* `metin` ➡️ `text`

---

## 🌟 Önemli Altın Kural: Kalkan Kullanımı `[TR]`

Eğer dönüştürücünün (Transpiler) ellememesini istediğiniz yerel bir Skript ifadesi, özel bir veri veya İngilizce kalmasını istediğiniz bir fonksiyon argümanı varsa onu `[TR]...[TR]` arasına alın. 

**Örnek:** `ayarla {p} şuna [TR]paper[TR]` derlendiğinde `set {p} to paper` olur. Eğer kalkan koymazsanız sistem hata vermez ancak yerel dönüşümlere takılabilir.

---

## 📝 Örnek Tam Bir LightLang Dosyası

Aşağıdaki kod parçası, LightLang kurallarına %100 uyumlu gelişmiş bir **Gelişmiş Cooldown ve Giriş Kontrol** sistemidir.

```skript
seçenekler:
    ayristirici: "[LightLang]"

değişkenler:
    {giris.sayisi} = 0

oyuncu oyuna girince:
    ekle 1 şuna {giris.sayisi}
    yaz "{@ayristirici} Sunucuya hoş geldin, oyuncu!" oyuncuya
    yaz "Sunucuya yeni bir oyuncu girdi!" konsola

komut /ozelyetenek:
    izin: lightlang.yetenek
    izin mesajı: "Bu yeteneği kullanmak için izniniz yok!"
    tetikleyici:
        ayarla {_bekleme} şuna {%oyuncu%.bekleme} ile şimdiki zaman arasındaki fark
        eğer {_bekleme} ayarlanmış ise:
            eğer {_bekleme} 10 saniyeden büyük ise:
                yaz "Yeteneği başarıyla kullandın!" oyuncuya
                ayarla {%oyuncu%.bekleme} şuna şimdiki zaman
            değilse:
                yaz "Bu yeteneği tekrar kullanmak için beklemelisin!" oyuncuya
        değilse:
            ayarla {%oyuncu%.bekleme} şuna şimdiki zaman
            yaz "İlk defa yetenek kullandın!" oyuncuya

blok kırınca:
    eğer oyuncu op değilse:
        yaz "Blok kırma yetkiniz yok!" oyuncuya
        iptal et
