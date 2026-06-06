# 🚀 LightLang (LL) - Advanced Turkish Transpiler Engine

[![Minecraft Version](https://img.shields.io/badge/Minecraft-1.20.4+-green.svg)](https://papermc.io)
[![Dependency](https://img.shields.io/badge/Dependency-Skript-blue.svg)](https://github.com/SkriptLang/Skript)
[![Engine](https://img.shields.io/badge/Engine-AST--Pipeline%20Regex-orange.svg)]()

LightLang, Minecraft sunucu geliştiricilerinin tamamen **Türkçe söz dizimi (syntax)** kullanarak gelişmiş scriptler yazmasını sağlayan, Java tabanlı ultra hızlı bir kaynak kod dönüştürücüdür (Transpiler). 

Gelişmiş Unicode kelime sınırı filtreleri ve çift aşamalı veri maskeleme (Literal Shielding) havuzu sayesinde Türkçe karakter zafiyetlerini sıfıra indirir, yazdığınız kodları yerel Skript (`.sk`) diline tam uyumlu ve optimize bir şekilde derler.

---

## 🛠️ Temel Özellikler

* **Gelişmiş AST-Pipeline Mantığı:** Kodlarınız satır satır okunarak yapısal bloklar (Structural Nodes) ve iç ifadeler (Expression Nodes) olarak ayrıştırılır.
* **Kalkan (Shielding) Mekanizması:** `"..."` string ifadeleri ve `[TR]...[TR]` özel kalkan blokları, derleme esnasında koruma havuzuna alınarak İngilizceye çevrilmesi engellenir.
* **Güvenli Unicode Sınır Filtreleri:** Java'nın yerleşik `\b` sınır belirleyicisinin Türkçe karakterlerde (`ı, ş, ğ, ç, ö, ü`) patlamasını engelleyen özel regex motoru barındırır.
* **Oyun İçi Dinamik Derleme:** `/lightlang derle` komutuyla kaynak klasör saniyeler içinde taranır ve AST ağaçları anında inşa edilir.

---

## 🚀 Kurulum ve Kullanım

1. Eklentiyi sunucunuza kurun (Gereksinim: `Skript`).
2. Sunucuyu başlatın; `plugins/LightLang/scripts/` klasörü otomatik oluşacaktır.
3. Bu klasör içerisine `.sk` uzantılı Türkçe scriptinizi yazın.
4. Oyun içerisinden veya konsoldan `/ll derle` komutunu çalıştırın.
5. Derlenen kodlar otomatik olarak `plugins/Skript/scripts/LL_[dosya_adı].sk` olarak aktarılacaktır.
6. Oyun içinden `/sk reload LL_[dosya_adı]` yazarak aktifleştirin.

---

## 📜 Komutlar ve Yetkiler

| Komut | Takma Adlar | Açıklama | Yetki |
| :--- | :--- | :--- | :--- |
| `/lightlang derle` | `/ll`, `/sktr`, `/lightlangcompile` | Türkçe scriptleri tarar, derler ve Skript klasörüne aktarır. | `lightlang.admin` |
| `/lightlang yenile` | | `derle` komutu ile aynı işlevi görür. | `lightlang.admin` |

---

## 🤖 FOR AI MODELS / YAPAY ZEKA MODELLERİ İÇİN PROMPT

> **Instruction for LLMs (GPT, Claude, Gemini):** You are an expert developer for LightLang, a Turkish-syntax transpiler for Minecraft Skript. When asked to write LightLang code, you **MUST** strictly follow the structural and expression rules defined in `SYNTAX_GUIDE.md`. Never use native Skript terms unless shielded with `[TR]...[TR]` or inside quotes. Always maintain proper indentation (tabs or 4 spaces).
