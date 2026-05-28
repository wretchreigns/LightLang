# 🚀 LIGHTLANG MASTERCLASS: RESMİ GELİŞTİRİCİ KILAVUZU & EĞİTİM DOKÜMANI (v1.0.0)
**Geliştirici:** Lightspeed (Wretch) & LightLang Core Team  
**Altyapı:** Gelişmiş AST (Abstract Syntax Tree) + Kelime Sınırı Korumalı Regex Transpiler Motoru  
**Hedef:** Minecraft sunucu ekosisteminde %100 Türkçe sözdizimi (syntax) ile kusursuz, optimize ve kayıpsız script geliştirme.

---

## 🧠 BÖLÜM 1: LIGHTLANG MOTORUNUN İÇ MİMARİSİ VE ÇALIŞMA PRENSİBİ

LightLang, ham metin üzerinde körü körüne "bul ve değiştir" (find and replace) yapan basit bir betik değildir. Eğer öyle olsaydı, yazdığınız her mesaj, değişken adı veya oyuncu ismi birbirine girer, kodunuz daha derlenmeden çökerdi. LightLang üç aşamalı bir derleme hattı (pipeline) kullanır:

```text
[Türkçe Kod] ➡️ [Kalkanlama ve İzolasyon (Tırnak & [TR])] ➡️ [AST ve Kelime Sınırı Analizi] ➡️ [Skript Çıktısı (.sk)]
1.1 Satır Normalizasyonu ve TemizlikFarklı kod editörleri (Notepad++, VS Code, IntelliJ IDEA) satır sonlarında farklı karakterler (\r\n veya \n) bırakır. Sunucu Linux ise Windows'tan gelen kodlar patlayabilir. LightLang derleyicisi dosyayı okuduğu ilk milisaniyede tüm satır sonlarını evrensel formata getirir, satır başlarındaki ve sonlarındaki görünmez boşluk hatalarını sıfırlar.1.2 Kelime Sınırı (Word Boundary - \b) TeknolojisiMotor, Türkçe bir kelimeyi İngilizceye çevirirken Java'nın Regex kütüphanesindeki \b (Word Boundary) kuralını temel alır.Örnek: Eğer kodunuzda oyuncu kelimesi geçiyorsa ve bu kelime oyuncular veya birlikte_oyuncu_ara şeklinde bir değişkenin parçasıysa, motor buna dokunmaz. Sadece tek başına duran, bir kod komutu olan oyuncu kelimesini yakalar ve player yapar.🛡️ BÖLÜM 2: DERLEYİCİNİN KUTSAL ZIRHI: [TR] VE STRİNG KORUMA MEKANİZMASIDerleyicinin en can alıcı, en hayati yeri burasıdır. Otomatik dönüştürücülerin en büyük zafiyeti, çevrilmemesi gereken oyun içi verileri (Rütbeler, Özel Eşya İsimleri, NBT Tagları) çevirerek kodu kullanılmaz hale getirmesidir. LightLang bu zafiyeti Çift Katmanlı Kalkan ile çözer.2.1 Çift Tırnak Koruması ("...")Kod satırının neresinde olursa olsun, çift tırnak (") içine alınmış olan hiçbir ifadeye LightLang motoru dokunamaz. Tırnak içi tamamen izole bir odaya alınır, çeviri motoru odanın dışındaki kodları dönüştürür, ardından odayı aynen çıktıya yapıştırır.Yazılan: yaz "Şu an aktif oyuncu sayısı az!" oyuncuyaİşlem: Motor "Şu an aktif oyuncu sayısı az!" kısmını dondurur. Dışarıdaki yaz ve oyuncuya kelimelerini çevirir.Çıktı: send "Şu an aktif oyuncu sayısı az!" to player (Mesajın içindeki "oyuncu" kelimesi player rütbesine dönüşmeden orijinal kalır).2.2 Büyük Geliştirici Sırrı: [TR] Kalkan MekanizmasıBazı durumlarda kod yazarken çift tırnak kullanamazsınız ya da tırnak kullansanız bile veritabanı/eklenti bağlantılarında ham Türkçe kelimeler bırakmanız gerekir. İşte burada devreye [TR]...[TR] zırhı girer.Çalışma Mantığı: Java kodundaki derleyici, regex taramasına başlamadan hemen önce [TR] ve [TR] etiketleri arasında kalan metni string havuzuna kaydeder ve oraya geçici bir token (benzersiz kod) koyar. Çeviri işlemi biter, en son aşamada o token kaldırılır ve senin yazdığın Türkçe kelime hiçbir harfi bozulmadan oraya enjekte edilir.🚀 Hayati [TR] Senaryoları (Gerçek Proje Örnekleri)Senaryo A: LuckPerms / Essentials Rütbe Atama KomutuSunucunuzda oyuncu, rehber, kurucu adında Türkçe rütbeler var. Konsola komut tetikletmek istiyorsunuz:Kalkansız Yazım: konsol komutu çalıştır "lp user %player% parent set oyuncu" ➡️ Hata: Motor tırnak içindeki karmaşık yapılarda bazen sondaki oyuncu kelimesini kod sanıp player yapabilir. Konsol parent set player çalıştırmaya çalışır ve rütbe sistemi çöker.Zırhlı Yazım: konsol komutu çalıştır "lp user %player% parent set [TR]oyuncu[TR]"Derleyici Çıktısı: execute console command "lp user %player% parent set oyuncu" (Sistem tam istediğin gibi Türkçe rütbeyi korur).Senaryo B: Dünya İsmi KontrolleriMinecraft haritanızın adı Türkçe karakter içeriyor veya doğrudan Türkçe bir kelime (Örn: Orman, Zindan).Zırhlı Yazım: eğer oyuncu'nun dünyası şuna eşit ise [TR]Orman[TR]:Derleyici Çıktısı: if player's world is "Orman":Senaryo C: Özel Değişken İsimleri KarışıklığıSkript içinde bazen öyle bir değişken ismi seçersiniz ki eklentinin anahtar kelimesiyle çakışır. Örneğin oyuncunun kırdığı odun tipini tutacaksınız:Zırhlı Yazım: ayarla {blok.%player%} şuna [TR]odun[TR]Derleyici Çıktısı: set {blok.%player%} to odun📋 BÖLÜM 3: EKSİKSİZ LIGHTLANG KELİME SÖZLÜĞÜ (TÜM ÖZELLİKLER)Derleyici kodunda (LightLangCompiler.java) tanımlanmış olan ve kod yazarken kullanabileceğiniz tüm kelimeler, bloklar ve yapıların tam listesi:3.1 Yapısal ve Blok Başlıkları (Structural Rules)Kodun ana iskeletini ve hiyerarşisini kuran, sonuna kesinlikle iki nokta (:) koyulması gereken kelimeler.Türkçe YazımıDönüştüğü Skript KarşılığıAçıklama / Kullanım Amacıseçenekler:options:Kod içi sabitlerin (mesajlar, sayılar) tanımlandığı alan.değişkenler:variables:Sunucu açıldığında RAM'de oluşacak varsayılan veriler.komut /<isim>:command /<isim>:Yeni bir oyun içi komut oluşturma bloğu başlatır.tetikleyici:trigger:Komut girildiğinde çalışacak asıl kodun başlangıcı.izin:permission:Komutu kullanabilmek için gereken yetki node'u.izin mesajı:permission message:Yetkisi olmayan oyuncuya gidecek hata bildirimi.açıklama:description:Komutun ne işe yaradığına dair sistem notu.kullanım:usage:Yanlış komut girildiğinde oyuncuya gösterilecek şablon.takma adlar:aliases:Komutun kısa yolları (Örn: /ll yerine /lightlang).çalıştırma alanı:executable by:Komutun sadece oyuncu mu yoksa konsol mu çalıştıracağı.eğer <koşul>:if <koşul>:Mantıksal kontrol bloklarını başlatır.değilse eğer:else if:İlk şart uymadıysa ikinci bir şartı kontrol eder.değilse:else:Yukarıdaki hiçbir şart uymadıysa çalışacak son çare bloğu.döngü <ifade>:loop <ifade>:Belirli bir listeyi veya oyuncuları döngüye alır.3.2 Olay Tetikleyicileri (Events)Kodun sunucu tarafından hangi eylem gerçekleştiğinde otomatik olarak ateşleneceğini belirleyen bloklar.Türkçe Olay TanımıSkript KarşılığıNe Zaman Tetiklenir?oyuncu oyuna girince:on join:Oyuncu sunucuya ayak bastığı an.oyuncu katılınca:on join:oyuna girince ile birebir aynı işlevi görür.oyuncu oyundan çıkınca:on quit:Oyuncu oyunu kapattığında veya bağlantısı koptuğunda.oyuncu ayrılınca:on quit:oyundan çıkınca ifadesinin alternatifidir.hasar alınca:on damage:Bir canlı veya oyuncu herhangi bir sebeple canı azaldığında.oyuncu ölünce:on death of player:Sadece gerçek bir oyuncu can verdiğinde çalışır.oyuncu öldürünce:on death:Sunucuda herhangi bir ölüm gerçekleştiğinde tetiklenir.odun kırınca:on break of any log or wood:Balta veya elle her türlü ağaç gövdesi kırıldığında.blok kırınca:on break:Sunucudaki herhangi bir bloğun kırılma anında.blok koyunca:on place:Oyuncu yere bir blok yerleştirdiğinde.sohbete yazınca:on chat:Oyuncu chate bir mesaj gönderdiğinde (Mesajı yakalamak için).sağ tıklayınca:on rightclick:Oyuncu elindeki eşyayla veya boş elle sağ tıkladığında.sol tıklayınca:on leftclick:Oyuncu havaya veya bir bloğa sol vuruş yaptığında.sunucu başlayınca:on script load:Eklenti yüklendiğinde veya sunucu açıldığında.sunucu kapanınca:on script unload:Sunucu kapatılırken verileri güvenle kaydetmek için.her <sayı> saniyede bir:every <sayı> seconds:Arka planda sürekli dönen saat (Örn: her 5 saniyede bir:).her <sayı> dakikada bir:every <sayı> minutes:Uzun süreli periyodik döngüler için.her <sayı> saatte bir:every <sayı> hours:Günlük veya saatlik sistem yenilemeleri için.3.3 İfadeler, Koşul ve Karşılaştırma Terimleri (Expressions)eğer bloklarının içinde iki veriyi birbiriyle kıyaslamak veya durum analizi yapmak için kullanılan motor kelimeleri.oyuncu op ise ➡️ player is op (Oyuncu yetkili mi?)oyuncu op değilse ➡️ player is not opoyuncu aktif ise / oyuncu çevrimiçi ise ➡️ player is online<veri> ayarlanmış ise ➡️ <veri> is set (Değişken içi dolu mu?)<veri> yok ise / <veri> ayarlanmamış ise ➡️ <veri> is not set (Değişken boş mu?)<veri> doğru ise ➡️ <veri> is true (Mantıksal true kontrolü)<veri> yanlış ise ➡️ <veri> is false (Mantıksal false kontrolü)şuna eşit ise ➡️ = (Örn: eğer argüman-1 şuna eşit ise 1:)şuna eşit değilse ➡️ !=şundan büyük ise ➡️ >şundan küçük ise ➡️ <şuna büyük ya da eşit ise ➡️ >=şuna küçük ya da eşit ise ➡️ <=3.4 Zaman ve Cooldown Mekanizmalarışimdiki zaman ➡️ now (Sistem saati)<zaman1> ile <zaman2> arasındaki fark ➡️ difference between <zaman1> and <zaman2><sayı> saniyeden büyük ise ➡️ > <sayı> seconds<sayı> dakikadan büyük ise ➡️ > <sayı> minutesbekle <sayı> saniye ➡️ wait <sayı> secondsbekle <sayı> dakika ➡️ wait <sayı> minutes3.5 Aksiyonlar ve Efektler (Effects)Koşullar sağlandıktan sonra oyuna doğrudan müdahale eden emir komutları.yaz <mesaj> oyuncuya ➡️ send <mesaj> to playeryaz <mesaj> konsola ➡️ send <mesaj> to consoleyaz <mesaj> tüm sunucuya ➡️ broadcast <mesaj> (Duyuru yapar)konsol komutu çalıştır "<komut>" ➡️ execute console command "<komut>"ver <eşya> oyuncuya ➡️ give <eşya> to playersil <eşya> oyuncudan ➡️ remove <eşya> from playerışınla <hedef> şuraya <konum> ➡️ teleport <hedef> to <konum>öldür <canlı> ➡️ kill <canlı>3.6 Değişken (Variable) Yönetim Komutlarıayarla <değişken> şuna <değer> ➡️ set <değişken> to <değer>ekle <değer> şuna <değişken> ➡️ add <değer> to <değişken>çıkar <değer> şundan <değişken> ➡️ remove <değer> from <değişken>sıfırla <değişken> ➡️ clear <değişken> (Veriyi tamamen yok eder)3.7 Akış Kontrolleridur ➡️ stop (Aşağıdaki satırların okunmasını engeller, kodu bitirir)iptal et ➡️ cancel event (Kazanılan hasarı, blok kırılmasını vs. durdurur)döngüyü durdur ➡️ exit loopdevam et ➡️ continuegeri gönder <veri> ➡️ return <veri> (Fonksiyonlarda çıktı verir)3.8 Nerede Görülürse Çevrilen Kritik Sınır Kelimeleri (Global Filter)Kodun neresinde tek başına durursa dursun otomatik olarak evrensel karşılığına dönen kelimeler:oyuncu ➡️ playerkonsol ➡️ consolehedef ➡️ victimsaldırgan ➡️ attackerdünya ➡️ worldkonum ➡️ locationetkinlik ➡️ eventargüman-<sayı> ➡️ arg-<sayı>ismi / ismı / adı ➡️ name🏗️ BÖLÜM 4: GELİŞMİŞ TÜRKÇE FONKSİYON MİMARİSİTekrar eden kod bloklarını temizlemek için LightLang tam fonksiyon desteği sunar. Fonksiyon tanımlarken veri tiplerini de Türkçe yazabilirsiniz:mantıksal ➡️ boolean (doğru/yanlış)sayı ➡️ numbermetin ➡️ textTasarım Şablonu:Plaintextfonksiyon <isim>(<parametre>: <veri_tipi>) :: <dönüş_tipi>:
    # Kodlar
    geri gönder <veri>
🎓 BÖLÜM 5: SIFIRDAN İLERİ DÜZEYE GELİŞTİRİCİ EĞİTİM KAMPI (3 PROJE)Bu bölüm, yukarıdaki sözlüğü ve mantığı kullanarak sunucunuza yükleyebileceğiniz tam teşekküllü, üretime hazır 3 adet Türkçe sistem senaryosu içerir.📜 PROJE 1: Gelişmiş Combat Log / Savaştan Kaçma SistemiOyuncular birbirine vurduğunda 10 saniye savaşa girerler. Savaşta oyundan çıkarlarsa itemleri silinir ve cezalandırılırlar.Plaintextdeğişkenler:
    {savas_suresi.%player%} = 0

hasar alınca:
    eğer saldırgan op değilse:
        # Saldıran ve hasar alan oyuncuları savaşa sok
        ayarla {savas_suresi.%saldırgan%} şuna şimdiki zaman
        ayarla {savas_suresi.%hedef%} şuna şimdiki zaman
        yaz "&c&lSAVAŞA GİRDİN! 10 Saniye Çıkma!" oyuncuya

oyuncu oyundan çıkınca:
    # Oyuncunun son savaşı üzerinden 10 saniye geçip geçmediğini kontrol et
    eğer {savas_suresi.%oyuncu%} ayarlanmış ise:
        eğer {savas_suresi.%oyuncu%} ile şimdiki zaman arasındaki fark 10 saniyeden küçük ise:
            # Cezalandırma aşaması ([TR] kalkanı ile eşya silme)
            yaz "Konsol: %oyuncu% savaştan kaçtı!" tüm sunucuya
            konsol komutu çalıştır "clear %oyuncu%"
            konsol komutu çalıştır "kill %oyuncu%"
            # Değişkeni temizle
            sıfırla {savas_suresi.%oyuncu%}
📜 PROJE 2: Gelişmiş Cooldownlı Maden ve Ekonomi SistemiOyuncular odun kırdıkça para kazanır. Ancak hileleri önlemek için her odun kırma arasında 3 saniye bekleme süresi (cooldown) olmalıdır.Plaintextseçenekler:
    odun_odulu: 15
    bekleme_suresi: 3

değişkenler:
    {maden_para.%player%} = 0
    {maden_cooldown.%player%} = 0

odun kırınca:
    eğer {maden_cooldown.%player%} ayarlanmış ise:
        eğer {maden_cooldown.%player%} ile şimdiki zaman arasındaki fark {@bekleme_suresi} saniyeden küçük ise:
            yaz "&cÇok hızlı kazıyorsun! Biraz bekle." oyuncuya
            iptal et
            dur
            
    # Cooldown sınırını aşmışsa ödülü ver
    ekle {@odun_odulu} şuna {maden_para.%player%}
    ayarla {maden_cooldown.%player%} şuna şimdiki zaman
    yaz "&aHarika! Odun kırdın ve {@odun_odulu} TL kazandın." oyuncuya

komut /parambak:
    çalıştırma alanı: oyuncu
    tetikleyici:
        yaz "&eMevcut Madenci Paranız: &b%{maden_para.%player%}% TL" oyuncuya
📜 PROJE 3: Yetki Kontrollü ve [TR] Kalkanlı Rütbe Satın Alma SistemiOyuncuların 1000 puana ulaştığında tamamen Türkçe olan Asker rütbesini oyun içi komutla alabildiği, koruma kalkanlarının havada uçuştuğu gelişmiş sistem.Plaintextseçenekler:
    rutbe_fiyati: 1000

değişkenler:
    {puan.%player%} = 2000

komut /rutbeol:
    açıklama: Puan karşılığı rütbe atlama komutu
    kullanım: /rutbeol
    çalıştırma alanı: oyuncu
    tetikleyici:
        eğer {puan.%player%} şundan büyük ya da eşit ise {@rutbe_fiyati}:
            # Ücreti tahsil et
            çıkar {@rutbe_fiyati} şundan {puan.%player%}
            
            # [TR] KALKANI BURADA DEVREDE: "Asker" kelimesi asla bozulmaz
            konsol komutu çalıştır "lp user %player% parent set [TR]Asker[TR]"
            
            yaz "&aBaşarıyla [TR]Asker[TR] rütbesine yükseldiniz!" oyuncuya
            yaz "&bSunucu: &f%player% &7adlı oyuncu puanıyla rütbe atladı!" tüm sunucuya
        değilse:
            yaz "&cRütbe almak için yeterli puanın yok! Gereken: {@rutbe_fiyati}" oyuncuya
⚠️ BÖLÜM 6: HAYATİ HATA TÜRLERİ VE DERLEME KRİZLERİ (TROUBLESHOOTING)Eğer kodunuz derlenirken hata veriyorsa veya Skript tarafında BUILD FAILED görüyorsanız, %99 ihtimalle aşağıdaki 4 hatadan birini yapmışsınızdır:6.1 İki Nokta (:) İhmaliLightLang hiyerarşik bir dildir. Bir blok açıyorsanız (olay, komut, koşul) o satır mutlaka : ile bitmelidir.Hatalı: eğer oyuncu op iseDoğru: eğer oyuncu op ise:6.2 Girinti (Indentation / Tab) Kaymasıtetikleyici: veya eğer: bloklarının altına yazılan kodlar mutlaka 1 Tab (veya 4 boşluk) içeriden başlamalıdır. Tüm satırların dikey hizası birbirine eşit olmak zorundadır. Hizayı bozduğunuz an derleyici bloğun bittiğini sanır.6.3 Lokal ({_}) ve Global ({}) Değişken KarmaşasıSadece o anlık işlem yapıp, satır bittiğinde silinmesini istediğiniz verilerde (Örn: zaman farkı hesaplamaları) mutlaka başına alt tire koyarak lokal değişken kullanın: {_kalan_sure}.Eğer oyuncunun parasını, rütbesini veya veritabanına kaydolacak verisini tutuyorsanız alt tireyi kaldırın: {para.%player%}. Geçici verileri global yapmak sunucuyu yorar.6.4 String İçine Kod Sıkıştırma HatasıÇift tırnak içinin tamamen korunduğunu söylemiştik. Eğer tırnak içine dinamik bir değişken yazacaksanız bunu Skript'in kuralına göre yüzde işaretleri içinde yapmalısınız.Hatalı: yaz "Tebrikler {para.%player%} paran var" oyuncuya (Tırnak içi korunduğu için ekranda aynen yazı olarak {para.%player%} çıkar).Doğru: yaz "Tebrikler %{para.%player%}% paran var" oyuncuya