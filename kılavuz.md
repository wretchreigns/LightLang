LIGHTLANG RESMİ GELİŞTİRİCİ KILAVUZU (v1.0)

Yazar: Lightspeed (Wretch) & LightLang Geliştirme Ekibi
Sürüm: 1.0.0
Altyapı: AST (Abstract Syntax Tree) + Regex Tabanlı Dönüştürme Motoru

LightLang, Minecraft sunucularında Türkçe sözdizimi ile script yazmayı sağlayan bir transpiler sistemidir. Amaç, Skript ekosisteminde İngilizce syntax zorunluluğunu azaltmak ve geliştiricinin doğrudan Türkçe ifade ile çalışmasını sağlamaktır. LightLang, yazılan Türkçe scriptleri derleme sırasında Skript formatına dönüştürür ve böylece daha okunabilir, daha yerel ve daha erişilebilir bir geliştirme ortamı sunar.

Bu kılavuz, LightLang’in temel çalışma mantığını, blok yapısını, olay sistemini, koşul mekanizmasını, fonksiyon kullanımını, koruma katmanlarını ve sık karşılaşılan hata türlerini detaylı şekilde açıklar.

1. Sistem Mimarisi

LightLang’in temel yapısı yalnızca “kelime bul, değiştir” mantığına dayanmaz. Sistem, satırları yapılandırılmış biçimde analiz eder; blokları ayırır, anahtar kelimeleri belirler, koruma alanlarını işler ve ardından Skript karşılıklarını üretir.

1.1 AST Tabanlı Analiz

AST yaklaşımı sayesinde kod, satır satır değil; anlamlı bloklar hâlinde değerlendirilir. Bu, özellikle karışık ve iç içe geçmiş yapıların doğru çevrilmesi için önemlidir. Örneğin bir komut bloğu içindeki tetikleyici, bir koşul bloğunun içindeki eylem veya bir fonksiyonun iç yapısı, bağımsız parçalar olarak ele alınır.

Bu yaklaşımın sağladığı başlıca avantajlar şunlardır:

Kodun mantıksal yapısı daha doğru korunur.
Yanlış eşleşmeler azalır.
Blok içi bağlam daha güvenli biçimde işlenir.
Daha karmaşık scriptlerde hata oranı düşer.
1.2 Kelime Sınırı (Word Boundary) Koruması

LightLang’in en önemli özelliklerinden biri, kelime sınırlarını dikkate almasıdır. Bu sayede motor, bir kelimeyi metin içinde görüp onu yanlışlıkla dönüştürmez. Özellikle string içeriklerinde bu özellik kritik öneme sahiptir.

Örnek olarak, "oyuncular bugün çok iyi" gibi bir cümle içinde geçen “oyuncu” ifadesi, motor tarafından doğrudan “player” gibi çevrilmez. Çünkü bu ifade kod değil, metin olarak kabul edilir.

1.3 Satır Sonu ve Platform Uyumluluğu

Farklı işletim sistemleri farklı satır sonu biçimleri kullanır. Windows, Linux ve diğer ortamlarda satır sonlarının farklı olması bazen script dönüşümünü bozabilir. LightLang bu farkları normalize eder ve kodun platformdan bağımsız çalışmasını sağlar.

Bu sayede:

Windows’ta yazılmış dosyalar Linux sunucusunda bozulmaz.
Karma satır sonları temizlenir.
Düzenli ve öngörülebilir bir derleme süreci oluşur.
2. Temel Sözdizimi Yapısı

LightLang blok tabanlıdır. Her ana yapı iki nokta işareti (:) ile başlar. Alt bloklar girinti ile belirlenir. Girinti düzeni bozulursa derleyici yapıyı yanlış yorumlayabilir.

2.1 Yapısal Mantık

LightLang’de kod, hiyerarşik bir mantıkla yazılır. Ana bloklar, alt blokları içine alır. Bu yapı Skript’in genel mantığıyla uyumludur ve Türkçe okunabilirliği koruyarak işlevsel dönüşüm sağlar.

Aşağıdaki kurallar temel kabul edilir:

Her blok bir başlıkla başlar.
Alt bloklar içeriden yazılır.
Girinti düzeni tutarlı olmalıdır.
Açılan yapıların doğru kapanması beklenir.
3. Ayarlar ve Sabit Değerler
3.1 Seçenekler Bloğu

seçenekler: bloğu, proje genelinde sık kullanılan sabit değerlerin tanımlandığı alandır. Bu alan, Skript’teki options: karşılığıdır.

Örnek:

seçenekler:
    hiz_seviyesi: 5
    mesaj_basarili: "&aİşlem tamamlandı."
    mesaj_hata: "&cBir hata oluştu."

Bu yapı genellikle şunlar için kullanılır:

sabit sayılar
mesaj şablonları
komut parametreleri
tekrar kullanılacak değerler
3.2 Değişkenler

Değişkenler, dinamik veri tutmak için kullanılır. Oyuncu başına veri, süre bilgisi, para, seviye, yetki durumu gibi bilgiler değişkenlerde saklanabilir.

Örnek:

değişkenler:
    {hiz_kullanimi.%player%} = yanlış
    {cooldown.%player%} = 0

Değişken yapısında iki temel tür vardır:

Global değişkenler: Sunucu çapında saklanır.
Lokal değişkenler: Sadece ilgili blok içinde geçerlidir.

Lokal değişkenler genellikle _ ile başlar. Bu fark, performans ve düzen açısından önemlidir.

4. Komut Sistemi

Komutlar, kullanıcı tarafından tetiklenen en temel giriş noktalarından biridir. LightLang, komut bloğunu insan okunabilir Türkçe ifadelerle tanımlar ve bunları Skript komut yapısına dönüştürür.

4.1 Komut Tanımı
komut /superguc <metin>:
    açıklama: Oyuncuya özel güç verir
    kullanım: /superguc [güç]
    izin: guc.kullan
    izin mesajı: &cBu gücü kullanmaya yetkin yok
    çalıştırma alanı: oyuncu

    tetikleyici:
        # komut çalışınca yapılacak işlemler

Bu yapı içinde farklı alanlar tanımlanabilir:

açıklama: Komutun kısa tanımıdır.
kullanım: Oyuncuya veya kullanıcıya gösterilen kullanım bilgisi.
izin: Komutu kullanmak için gerekli yetki.
izin mesajı: Yetki yoksa gösterilecek uyarı.
çalıştırma alanı: Komutun nerede çalışacağını belirtir.
tetikleyici: Komut çalıştığında yürütülecek ana kod bloğu.
4.2 Parametreler

Komut içinde parametre kullanmak mümkündür. Bu sayede oyuncu komuta bir değer gönderebilir.

Örnek:

komut /hizver <sayı>:
    tetikleyici:
        ayarla {hiz.%player%} şuna argüman-1

Parametrelerin doğru türde tanımlanması önemlidir. Yanlış tip kullanımı derleme hatasına veya beklenmeyen davranışlara yol açabilir.

5. Olay Sistemi

LightLang’in güçlü taraflarından biri olay temelli yapıyı doğrudan desteklemesidir. Olaylar, sunucuda bir durum gerçekleştiğinde tetiklenen bloklardır.

5.1 Oyuncu Olayları
oyuncu oyuna girince:

Oyuncu sunucuya bağlandığında çalışır.

oyuncu oyundan çıkınca:

Oyuncu ayrıldığında çalışır.

oyuncu ölünce:

Oyuncu öldüğünde tetiklenir.

oyuncu öldürünce:

Bir oyuncu başka bir oyuncuyu öldürdüğünde çalışır.

hasar alınca:

Hasar alma durumlarında kullanılır. Bu olayda hedef ve saldırgan ayrımı yapılabilir.

Örnek:

oyuncu oyuna girince:
    yaz "&aSunucuya hoş geldin!" oyuncuya
5.2 Blok Olayları

Blok temelli sistemler, özellikle kaynak toplama, yapı bozma, koruma alanı ve oyun mekaniği üretmek için kullanılır.

odun kırınca:
blok kırınca:
blok koyunca:
sağ tıklayınca:
sol tıklayınca:

Bu olaylar, hem basit etkileşimlerde hem de gelişmiş sistemlerde kullanılabilir. Örneğin özel maden sistemi, büyülü eşya kullanımı veya özel yetenek tetikleme gibi mekanikler bu olaylara bağlanabilir.

5.3 Sunucu Olayları

Sunucu açılışı, kapanışı ve periyodik görevler bu başlık altında değerlendirilir.

sunucu başlayınca:
sunucu kapanınca:
her 5 saniyede bir:
her 10 dakikada bir:

Bunlar özellikle:

veri sıfırlama
otomatik duyuru
sistem kontrolü
zamanlı görevler

için kullanılır.

6. Koşul Sistemi

Koşullar, LightLang’in mantıksal karar mekanizmasını oluşturur. Bir olay tetiklendiğinde, belirli şartlar sağlanıyorsa işlem yapılır; sağlanmıyorsa alternatif yol izlenir.

6.1 Temel Koşullar

Aşağıdaki yapıların tamamı mantıksal kontrol için kullanılabilir:

oyuncu op ise:
oyuncu aktif ise:
oyuncu çevrimiçi ise:
<değer> ayarlanmış ise:
<değer> yok ise:
<değer> doğru ise:
<değer> yanlış ise:
6.2 Karşılaştırmalar

LightLang, karşılaştırma mantığını doğal Türkçe ifadelerle verir.

şuna eşit ise
şuna eşit değilse
şundan büyük ise
şundan küçük ise
şuna büyük ya da eşit ise
şuna küçük ya da eşit ise

Örnek:

eğer oyuncu op ise:
    yaz "&aYetkin var." oyuncuya
değilse:
    yaz "&cYetkin yok." oyuncuya
6.3 Zaman Koşulları

Zaman tabanlı kontroller, cooldown sistemleri ve gecikmeli mekanikler için kullanılır.

şimdiki zaman
<A> ile <B> arasındaki fark
<sayı> saniyeden büyük ise
<sayı> dakikadan küçük ise

Bu yapı, özellikle bekleme süreleri için oldukça kullanışlıdır.

7. Eylemler ve Yürütme Sistemi

Koşullar sağlandığında çalışan komutlar ve işlemler bu bölümde yer alır.

7.1 Mesaj Gönderme
yaz <mesaj> oyuncuya
yaz <mesaj> tüm sunucuya

Örnek:

yaz "&aİşlem başarılı." oyuncuya
yaz "&eYeni bir duyuru var." tüm sunucuya
7.2 Eşya İşlemleri
ver <eşya> oyuncuya
sil <eşya> oyuncudan

Bu işlemler envanter sistemi, ödül mekanikleri ve ceza sistemlerinde kullanılır.

7.3 Konum ve Teleport
ışınla <hedef> şuraya <konum>

Örnek:

ışınla oyuncu şuraya {spawn_noktasi}
7.4 Öldürme ve Canlı İşlemleri
öldür <hedef>

Bu komut, belirli bir canlıyı veya oyuncuyu anında etkisiz hâle getirmek için kullanılabilir. Yalnızca gerekli senaryolarda kullanılmalıdır.

7.5 Konsol Komutları
konsol komutu çalıştır "<komut>"

Bazı işlemler Skript içinden doğrudan yapılamadığında, sunucu konsolu üzerinden komut çalıştırmak gerekir. Bu yapı, gelişmiş eklenti entegrasyonlarında çok faydalıdır.

7.6 Akış Kontrolü
dur → Kod akışını o noktada keser.
iptal et → Mevcut olayı iptal eder.

Örnek:

hasar alınca:
    iptal et
    yaz "&cBu alanda hasar alamazsın." oyuncuya
8. Değişken İşlemleri

LightLang’de veri yönetimi oldukça önemlidir. Değişkenler bir oyuncunun durumu, sayaçlar, skorlar, para sistemi ve özel etkiler gibi pek çok alanda kullanılır.

8.1 Değer Atama
ayarla <değişken> şuna <değer>

Örnek:

ayarla {para.%player%} şuna 50
8.2 Değer Ekleme
ekle <değer> şuna <değişken>

Örnek:

ekle 10 şuna {para.%player%}
8.3 Değer Çıkarma
çıkar <değer> şundan <değişken>

Örnek:

çıkar 5 şundan {can.%player%}
8.4 Değişken Sıfırlama
sıfırla <değişken>

Bu işlem değişkeni tamamen siler ya da başlangıç durumuna döndürür.

9. Fonksiyon Sistemi

LightLang, tekrar eden işlemleri tek yerde tanımlayıp birçok noktada kullanmayı mümkün kılar. Bu yapı, daha temiz kod ve daha az tekrar demektir.

9.1 Fonksiyon Tanımı
fonksiyon hizKontrol(p: oyuncu) :: mantıksal:
    eğer {_p} op ise:
        geri gönder doğru
    değilse:
        geri gönder yanlış

Fonksiyon yapısı şu parçaları içerir:

fonksiyon adı
parametreler
dönüş tipi
gövde bloğu
geri gönder ifadesi
9.2 Dönüş Tipleri

LightLang, Türkçe tip adlarını kullanabilir. Bunların Skript veya genel tip karşılıkları şu şekilde yorumlanır:

mantıksal → boolean
sayı → number
metin → text/string
9.3 Fonksiyon Kullanım Alanları

Fonksiyonlar özellikle şu durumlarda kullanılır:

aynı kontrolün sık tekrar ettiği sistemler
yetki kontrolü
cooldown hesapları
puan sistemi
özel yetenek mantıkları
veri doğrulama işlemleri
10. Koruma Sistemi

LightLang’de otomatik çeviri sırasında bazı ifadelerin korunması gerekir. Bu bölüm, yanlış dönüşümleri önlemek için tasarlanmıştır.

10.1 Çift Tırnak Koruması

Çift tırnak içindeki metinler işlem sırasında korunur.

Örnek:

yaz "Yeni bir oyuncu katıldı!" tüm sunucuya

Burada cümle içindeki kelimeler çevrilmez. Çünkü sistem bunu metin olarak görür.

10.2 [TR] Etiketi

Bazı durumlarda tırnak kullanmak uygun olmayabilir. Böyle durumlarda [TR]... [TR] etiketi kullanılır.

Örnek:

execute console command "give %player% [TR]odun[TR]"

Bu sistem, çevrilmemesi gereken özel terimleri korumak için kullanılır. Derleme sonunda bu etiketler temizlenir ve çıktı sade hâlde bırakılır.

10.3 Neden Gereklidir?

Otomatik çeviri motorları bazen bağlamı yanlış yorumlayabilir. [TR] etiketi sayesinde:

özel isimler korunur
komut parametreleri bozulmaz
kullanıcı metni yanlış çevrilmez
kodun doğruluğu artırılır
11. Hata Türleri ve Yaygın Sorunlar

LightLang kullanımında en sık karşılaşılan hatalar genelde yapısal kaynaklıdır.

11.1 İki Nokta Eksikliği

Blok başlıklarının sonunda : unutulmamalıdır.

Yanlış:

eğer oyuncu op ise

Doğru:

eğer oyuncu op ise:
11.2 Girinti Bozukluğu

Alt blokların hizası korunmalıdır. Girinti yanlış olursa motor bloğu yanlış yorumlayabilir.

Örnek:

komut /test:
    tetikleyici:
        yaz "Merhaba" oyuncuya
11.3 Değişken Tür Karışıklığı

Lokal ve global değişkenler birbirine karıştırılmamalıdır.

{_zaman} → lokal
{zaman.%player%} → global

Lokal değişkenler geçici işlemler için daha uygundur. Global değişkenler kalıcı veri tutmak için kullanılır.

11.4 String İçine Kod Yazımı

Tırnak içindeki metinler kod gibi değerlendirilmemelidir. Bu yüzden mesaj ve komut ayrımı dikkatli yapılmalıdır.

12. Örnek Kullanım Senaryosu

Aşağıda LightLang’in pratik kullanımına örnek bir yapı verilmiştir:

seçenekler:
    hiz_seviyesi: 5

değişkenler:
    {hiz.%player%} = yanlış

oyuncu oyuna girince:
    yaz "&aSunucuya hoş geldin!" oyuncuya

komut /hizver:
    izin: lightlang.hiz
    izin mesajı: &cBu yetkiye sahip değilsin!
    çalıştırma alanı: oyuncu
    tetikleyici:
        konsol komutu çalıştır "effect give %player% minecraft:speed {@hiz_seviyesi} 1 true"
        yaz "&bHız etkisi uygulandı." oyuncuya

odun kırınca:
    yaz "&eBir odun kırdın." oyuncuya

Bu örnekte:

seçenekler bloğu sabit değer tutar
oyuncu oyuna girişte mesaj alır
komut özel yetkiyle sınırlandırılır
olay tetiklenince otomatik işlem yapılır
13. Geliştirme Notları

LightLang, ilerleyen sürümlerde daha geniş bir söz dizimi desteği, daha güçlü hata analizi ve daha gelişmiş dönüşüm kurallarıyla büyütülebilir. Özellikle aşağıdaki alanlar geliştirmeye açıktır:

daha detaylı fonksiyon desteği
daha iyi hata raporlama
otomatik satır düzeltme
iç içe blok çözümlemesi
gelişmiş modül sistemi
çoklu dosya derleme desteği
