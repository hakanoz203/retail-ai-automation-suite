<div align="center">

# 🛋️ Konfor Use Case Factory

**Boş mobilya ürün fotoğraflarını insanların ürünleri tecrübe ettiği fotogerçekçi "use case" görsellerine dönüştüren otonom n8n workflow'u.**

[![n8n](https://img.shields.io/badge/n8n-2.6.4+-EA4B71?style=flat-square&logo=n8n&logoColor=white)](https://n8n.io/)
[![fal.ai](https://img.shields.io/badge/fal.ai-FLUX.2_Pro_Edit-blueviolet?style=flat-square)](https://fal.ai/)
[![Notion](https://img.shields.io/badge/Notion-API-000000?style=flat-square&logo=notion&logoColor=white)](https://www.notion.so/)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

</div>

---

## 📖 Proje Hakkında

Konfor Use Case Factory, e-ticaret mobilya kataloglarındaki **boş ürün fotoğraflarını**, ürünün fiilen kullanıldığı **"use case" görsellerine** otonom olarak dönüştüren bir n8n otomasyon sistemidir.

**Örnek dönüşüm:**

| Girdi | Çıktı |
|---|---|
| Boş bir 3'lü koltuk takımı fotoğrafı | Aynı koltukta anne kitap okurken, çocuk telefon bakarken |
| Boş bir yemek odası takımı | Adam o masada yemek yerken |
| Boş bir köşe koltuk takımı | Kadın köşe koltukta uzanıp dinlenirken |

Kritik kısıtlama: **mobilyanın kendisi, kamera açısı, ışık ve arka plan birebir korunur.** Sadece sahneye doğal görünümlü insan(lar) eklenir.

### 🎯 Kullanım Alanları

- E-ticaret galeri görselleri (ana ürün fotoğrafının yanına use case versiyonu)
- Sosyal medya ve reklam içerikleri
- Katalog/broşür çalışmaları
- A/B test için varyantlı görsel üretimi

---

 ## Özellikler

-  **Tam otonom**: Google Drive'a fotoğraf bırak, gerisini sistem halleder
-  **Akıllı kategorizasyon**: OpenAI ChatGpt-4O vision ile otomatik ürün sınıflandırma (4 kategori)
-  **Premium görsel üretimi**: fal.ai FLUX.2 Pro Edit ile fotogerçekçi sonuçlar
-  **Senaryo havuzu**: Notion'da yönetilebilir, kategoriye özel senaryo bankası (29+ senaryo)
-  **İnsan onayı**: Notion UI üzerinden manuel approval workflow
-  **Maliyet kontrolü**: Görsel başına ~$0.03, çoklu işlemler için ekonomik

---

## 🏗️ Sistem Mimarisi

```
┌─────────────────────┐
│  Google Drive       │
│  /Konfor/Inbox      │ ← Ham ürün fotoğrafları
└──────────┬──────────┘
           │ (file_created trigger)
           ▼
┌─────────────────────┐
│  Download + Public  │ ← Drive dosyasını fal.ai için public yap
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  ChatGpt-4O         │ ← Vision API ile kategorize et
│  (Categorize)       │   {kategori, renk, ışık, boş_alan...}
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Parse Metadata     │ ← JSON parse + savunmacı yapı
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Notion: Senaryolar │ ← Kategoriye göre filtrele
│  (Fetch)            │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Pick Random        │ ← Havuzdan rastgele senaryo seç
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Build Prompt       │ ← FLUX.2 Edit için final prompt
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  fal.ai FLUX.2 Pro  │ ← Görsel üretimi (premium kalite)
│  Edit               │
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Download + Save    │ ← /Generated klasörüne kaydet
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Notion: Onay       │ ← Pending Approval kaydı oluştur
└─────────────────────┘
```

---

## 🛠️ Teknoloji Stack

| Bileşen | Teknoloji | Rol |
|---|---|---|
| Orkestrasyon | n8n (self-hosted) | Workflow motoru |
| Görsel depolama | Google Drive | Inbox/Generated/Final klasörleri |
| Vision AI | Claude Opus 4.7 (Anthropic API) | Ürün kategorizasyonu ve metadata çıkarımı |
| Veritabanı | Notion | Senaryo havuzu + onay tablosu |
| Görsel üretimi | fal.ai FLUX.2 Pro Edit | Premium image-to-image editing |

---

## 📋 Ön Gereksinimler

Sistemi kurmaya başlamadan önce şunlara ihtiyacın var:

- ✅ Self-hosted n8n instance (v2.6.4+)
- ✅ Google Drive hesabı + OAuth credentials
- ✅ Anthropic API key ([console.anthropic.com](https://console.anthropic.com))
- ✅ Notion hesabı + Internal Integration token
- ✅ fal.ai hesabı + API key ([fal.ai/dashboard/keys](https://fal.ai/dashboard/keys))

**Maliyet beklentisi:**
- fal.ai: ~$0.03 / üretilen görsel (FLUX.2 Pro Edit)
- Chatgpt: ~$0.005 / kategorizasyon çağrısı
- Toplam: 100 ürün için ~$3.50

---

## 🚀 Kurulum

### 1️⃣ Google Drive klasör yapısı

Drive'ında şu klasör yapısını oluştur:

```
/Konfor/
├── Inbox/         ← Buraya ham ürün fotoğraflarını yükleyeceksin (trigger)
├── Generated/     ← Üretilen görseller buraya kaydedilecek
└── Final/        
```

Her klasörün **ID**'sini al (klasör URL'sinden alınır). Bunları workflow JSON'una yapıştıracaksın.

### 2️⃣ Notion veritabanlarını oluştur

**Database 1: `Use Case Senaryolar`**

| Kolon adı | Tip | Açıklama |
|---|---|---|
| `senaryo_id` | Title | Benzersiz ID (örn: `k01`, `y07`) |
| `kategori` | Select | `koltuk_takimi`, `kose_takimi`, `yemek_odasi`, `yatak_odasi` |
| `tr_aciklama` | Rich text | Türkçe açıklama (örn: "Anne kitap okuyor") |
| `en_prompt_fragment` | Rich text | İngilizce prompt parçası |
| `aktif` | Checkbox | Geçici devre dışı bırakmak için |

**Database 2: `Use Case Onay`**

| Kolon adı | Tip |
|---|---|
| `Urun Adi` | Title |
| `Kategori` | Select (4 kategori) |
| `Senaryo` | Rich text |
| `Senaryo ID` | Rich text |
| `Orijinal URL` | URL |
| `Uretilen URL` | URL |
| `Status` | Select: `Pending Approval`, `Approved`, `Rejected`, `Retry` |
| `Retry Count` | Number |
| `Prompt` | Rich text |
| `Ana Renk` | Rich text |
| `Stil` | Select: `modern`, `klasik`, `avangart`, `rustic`, `minimalist` |

> ⚠️ **Önemli:** Notion Integration'ını her iki veritabanına da "Connect" etmeyi unutma. Bağlı değilse n8n boş döner.

### 3️⃣ Senaryo havuzunu seed et

`docs/senaryolar.md` dosyasındaki 29 hazır senaryoyu Notion `Use Case Senaryolar` veritabanına aktar. Her kategori için 7-8 senaryo bulunur:

- **Koltuk takımı (k01-k08):** Anne kitap okuyor, çift film izliyor, çocuk telefon bakıyor, baba gazete okuyor vb.
- **Köşe takımı (ks01-ks07):** Kadın uzanmış dinleniyor, aile film akşamı, genç müzik dinliyor vb.
- **Yemek odası (y01-y07):** Adam yemek yiyor, aile yemeği, kadın masada çalışıyor vb.
- **Yatak odası (yo01-yo07):** Kadın kahve içiyor, kitap okuyor, çift sohbet ediyor vb.

### 4️⃣ Credentials'ı n8n'e ekle

| Credential | Tip | Detay |
|---|---|---|
| Google Drive | OAuth2 | Scope: `drive.file` + `drive.readonly` |
| Anthropic | API key | Model: `claude-opus-4-7` |
| Notion | Internal Integration | Her iki DB'ye paylaşılmış olmalı |
| fal.ai | HTTP Header Auth | Header: `Authorization`, Value: `Key YOUR_FAL_KEY` |

### 5️⃣ Workflow'u import et

1. n8n UI → **Workflows → Import from File**
2. Repo'daki `workflows/konfor_use_case_factory.json` dosyasını seç
3. Tüm `REPLACE_WITH_*` placeholder'larını gerçek ID'lerle değiştir:
   - `REPLACE_WITH_INBOX_FOLDER_ID`
   - `REPLACE_WITH_GENERATED_FOLDER_ID`
   - `REPLACE_WITH_SENARYOLAR_DB_ID`
   - `REPLACE_WITH_ONAY_DB_ID`
4. Her node'a kendi credential'ını ata
5. Workflow'u **Publish** et

### 6️⃣ Test et

`/Konfor/Inbox` klasörüne 1 ürün fotoğrafı yükle ve workflow'un baştan sona çalışmasını bekle. Notion `Use Case Onay` tablosunda yeni bir `Pending Approval` kaydı görmen lazım.

---

## 📂 Repo Yapısı

```
konfor-use-case-factory/
├── README.md                          ← Bu dosya
├── LICENSE
├── workflows/
│   └── konfor_use_case_factory.json   ← n8n import dosyası
├── examples/
│   ├── before/                        ← Boş ürün fotoğrafı örnekleri
│   └── after/                         ← Üretilen use case görselleri
└── .gitignore
```

---

## 🧩 Node Detayları

Workflow 13 node'dan oluşur:

| # | Node | Tip | İşlevi |
|---|---|---|---|
| 1 | Drive Trigger | Google Drive Trigger | `/Inbox` klasörünü dinler |
| 2 | Download Image | Google Drive | Tetiklenen dosyayı indirir |
| 3 | Make Public | Google Drive | fal.ai için dosyayı public yapar |
| 4 | Build Public URL | Code | Drive direct download URL'si üretir |
| 5 | Categorize with Claude | Anthropic | Vision API ile metadata çıkarır |
| 6 | Parse Metadata | Code | Claude çıktısını güvenli parse eder |
| 7 | Fetch Scenarios | Notion | Kategoriye uyan senaryoları çeker |
| 8 | Pick Random Scenario | Code | Havuzdan rastgele birini seçer |
| 9 | Build Prompt | Code | FLUX.2 için final prompt inşa eder |
| 10 | Call fal.ai FLUX.2 | HTTP Request | Görsel üretimi (POST) |
| 11 | Download Generated | HTTP Request | Üretilen görseli indirir |
| 12 | Save to /Generated | Google Drive | Drive'a kaydet |
| 13 | Create Pending Row | Notion | Onay tablosuna kayıt ekle |

---

## 🎨 fal.ai FLUX.2 Pro Edit Konfigürasyonu

Sistem, fal.ai'nin en güncel premium düzenleme modeli olan **FLUX.2 Pro Edit** kullanır. Mobilyanın korunması ve insan eklenmesi için optimize edilmiş body:

```json
{
  "prompt": "{{ build_prompt }}",
  "image_urls": ["{{ source_url }}"],
  "image_size": "auto",
  "output_format": "jpeg",
  "safety_tolerance": "2",
  "enable_safety_checker": true
}
```

**Önemli parametreler:**
- `image_size: "auto"` → Kaynak görselin oranını/boyutunu korur (kamera açısı bozulmaz)
- `safety_tolerance: "2"` → E-ticaret için ideal sıkılık seviyesi
- `image_urls` array → FLUX.2 tekil string yerine array bekler (FLUX 1 Kontext'ten farkı)

---

## 🤖 Kategorize Agent Sistem Prompt'u

ChatGPT-4O vision agent, ürünü 4 kategoriden birine yerleştirir ve sonraki adımlar için zengin metadata üretir.

```json
{
  "kategori": "yemek_odasi",
  "guven": 95,
  "ana_renk": "gri",
  "ikincil_renk": "bej",
  "stil": "modern",
  "malzeme": "ahşap",
  "oda_tipi": "dining_room",
  "isik_yonu": "sol",
  "isik_tipi": "dogal",
  "arka_plan": "açık gri duvar, ahşap zemin",
  "bos_alan": "masa başı",
  "genel_aciklama": "modern yemek odası takımı"
}
```

`isik_yonu`, `bos_alan` ve `arka_plan` alanları sonraki prompt inşa adımında **kritik** rol oynar — eklenen insanın gölgesinin doğru düşmesi, doğru konuma yerleşmesi ve arka planın değişmemesi için kullanılır.

---

## 💡 Kullanım İpuçları

### Daha iyi sonuçlar için

✅ **Yüksek çözünürlüklü kaynak görseller kullan** (en az 1024×1024). Düşük çözünürlüklü girdiler bulanık yüzlere yol açar.

✅ **Aydınlatması iyi fotoğraflar tercih et.** Karanlık veya çok kontrastlı sahnelerde model insan yerleştirmede zorlanır.

✅ **Mobilyanın net görünür olduğundan emin ol.** Üst görseller veya çok yakın çekimler kategorize agent'ı yanıltabilir.

✅ **Senaryo havuzunu çeşitlendir.** Aynı 3-4 senaryo tekrar üretilirse galeride monotonluk oluşur. 7-8 farklı senaryo öneririm.

### Bilinen sınırlamalar

⚠️ **Google Drive trigger gecikmesi.** Drive trigger ~1 dakika gecikmeyle çalışır. Real-time bekleme.

⚠️ **Concurrency limiti.** fal.ai default 2 concurrent task verir. Büyük batch'lerde 429 alabilirsin — workflow ayarlarında concurrency 2-3'le sınırla.

---

## 🤝 Katkıda Bulunma

Pull request'ler memnuniyetle karşılanır. Büyük değişiklikler için önce bir issue açıp ne yapmayı planladığını tartışmanı rica ederim.

Özellikle katkıya açık alanlar:
- 🎭 Yeni senaryo önerileri (daha çeşitli persona ve durumlar)
- 🌐 Diğer mobilya markalarına adaptasyon
- 📊 Prompt dizayn ve sistem promptu iyileştirmeleri
- 🔧 Workflow optimizasyonları
---

**Hakan Özdemir**

- 💼 AI Automation & LLM Orchestration
- 🔧 n8n workflows, LangChain, Notion APIs

---

## 🙏 Teşekkürler

- [Konfor Mobilya](https://konfor.com.tr) - Use case ilhamı
- [n8n](https://n8n.io) - Workflow motoru
- [OpenAI](https://www.Openai.com) - Claude vision API
- [fal.ai](https://fal.ai) - FLUX.2 Pro Edit
