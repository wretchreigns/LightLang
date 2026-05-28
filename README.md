LightLang v1.0.0
Türkçe Skript Dönüştürücü Motoru

LightLang, Minecraft sunucuları için geliştirilmiş, Türkçe sözdizimi ile yazılan kodları Skript diline dönüştüren bir transpiler eklentisidir.

Java tabanlı bir dönüştürme motoru kullanır. Yazılan Türkçe scriptler, sunucu çalışırken otomatik olarak Skript formatına çevrilir.

Özellikler
Türkçe Syntax Desteği: Kodlar tamamen Türkçe yazılabilir ve Skript diline çevrilir.
Kelime Koruma Sistemi: Tırnak içindeki metinler ve değişkenler korunur, yanlış dönüşüm yapılmaz.
Fonksiyon Desteği: Türkçe parametre tipleri ile fonksiyon tanımlanabilir (mantıksal, sayı, metin).
Koruma Etiketi: Çevrilmemesi gereken ifadeler [TR]kelime[TR] formatı ile korunabilir.
Performans: Hafif yapı sayesinde hızlı derleme süresi sağlar.
Kurulum
LightLang-1.0.0.jar dosyasını sunucunun plugins klasörüne ekleyin.
Sunucuyu başlatın.
plugins/LightLang/scripts/ klasörü otomatik oluşturulur.
.sk uzantılı Türkçe script dosyalarını bu klasöre ekleyin.
/lightlang derle veya /ll derle komutu ile derleme işlemini başlatın.
Örnek Kullanım
seçenekler:
    hiz_seviyesi: 5

oyuncu oyuna girince:
    yaz "&aSunucuya hoş geldin! Hız sistemi aktif." oyuncuya

komut /hizver:
    izin: lightlang.hiz
    izin mesajı: &cBu yetkiye sahip değilsin!
    tetikleyici:
        konsol komutu çalıştır "effect give %player% minecraft:speed 999999 {@hiz_seviyesi} true"
        yaz "&bIşık hızına ulaşıldı!" oyuncuya

odun kırınca:
    yaz "&eHarika! Bir odun kırdın." oyuncuya
