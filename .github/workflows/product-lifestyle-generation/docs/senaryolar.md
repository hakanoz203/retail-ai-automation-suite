### 3.2 Notion: Senaryolar veritabanı

Bir Notion DB oluştur, adı: **`Senaryolar`**

| Kolon adı | Tip | İçerik örneği |
|---|---|---|
| `senaryo_id` | Title | `k01` |
| `kategori` | Select | `koltuk_takimi`, `kose_takimi`, `yemek_odasi`, `yatak_odasi` |
| `tr_aciklama` | Rich text | `Anne kitap okuyor` |
| `en_prompt_fragment` | Rich text | `a woman in her 40s reading a book, relaxed posture, soft natural smile` |
| `aktif` | Checkbox | true/false (geçici devre dışı bırakmak için) |

### 3.3 Notion: Onay veritabanı

İkinci bir DB: **`Use Case Onay`**

| Kolon adı | Tip |
|---|---|
| `Urun Adi` | Title |
| `Kategori` | Select (4 kategori) |
| `Senaryo` | Rich text |
| `Senaryo ID` | Rich text |
| `Orijinal URL` | URL |
| `Uretilen URL` | URL |
| `Status` | Select: `Pending Approval`, `Approved`, `Rejected`, `Retry` |
| `Retry Count` | Number (max 2) |
| `Prompt` | Rich text (uzun) |
| `Ana Renk` | Rich text |
| `Stil` | Select: `modern`, `klasik`, `avangart`, `rustic` |

## 4. Senaryo Havuzu (Notion'a manuel yükleme için)

İlk seed için aşağıdaki senaryoları Notion `Senaryolar` DB'sine ekle. Her kategori için 5-8 senaryo yeterli — workflow rastgele seçecek.

### Koltuk takımı (`koltuk_takimi`)

| ID | TR | EN prompt fragment |
|---|---|---|
| k01 | Anne kitap okuyor | a woman in her 40s reading a book, relaxed posture, soft afternoon light on her face |
| k02 | Çift film izliyor | a young couple sitting together watching TV, casual evening, both holding mugs |
| k03 | Çocuk telefon bakıyor | a teenager casually scrolling on a phone, legs crossed, comfortable posture |
| k04 | Baba gazete okuyor | a middle-aged man reading a newspaper, reading glasses on, relaxed weekend morning |
| k05 | Misafir çay içiyor | a friendly woman sipping tea from a glass, hosting a guest, warm smile |
| k06 | Köpekle oturuyor | a person sitting with a small dog on their lap, peaceful expression |
| k07 | Laptop ile çalışıyor | a young professional working on a laptop, focused but comfortable |
| k08 | Bebek emziriyor | a young mother gently holding a baby, calm domestic moment, soft lighting |

### Köşe takımı (`kose_takimi`)

| ID | TR | EN prompt fragment |
|---|---|---|
| ks01 | Kadın uzanmış dinleniyor | a woman lying down on the corner sofa, eyes half-closed, blanket over her legs |
| ks02 | Aile film akşamı | a family of three watching a movie together, popcorn bowl shared between them |
| ks03 | Genç müzik dinliyor | a young person with headphones, leaning back, eyes closed listening to music |
| ks04 | Kitap kulübü | three women chatting and laughing, books and coffee cups on the side table |
| ks05 | Yorgun adam uzanmış | a tired man lying full-length on the corner sofa after work, eyes closed |
| ks06 | Bebek uyuyor | a small baby sleeping peacefully on the corner sofa, soft blanket around them |
| ks07 | Çift kahve içiyor | a couple sitting close, sharing morning coffee, looking at each other warmly |

### Yemek odası (`yemek_odasi`)

| ID | TR | EN prompt fragment |
|---|---|---|
| y01 | Adam yemek yiyor | a middle-aged man enjoying dinner at the table, fork mid-air, content expression |
| y02 | Aile yemeği | a family of four having dinner together at the table, plates and glasses visible |
| y03 | Kadın masada çalışıyor | a woman working on a laptop at the dining table, coffee mug beside her |
| y04 | Misafir kahvaltı | three friends having a leisurely brunch, croissants and orange juice on the table |
| y05 | Çift romantik akşam | a couple having a candlelit dinner, wine glasses, romantic warm lighting |
| y06 | Çocuk ödev yapıyor | a child doing homework at the dining table, notebooks and pencils spread out |
| y07 | Büyükanne yemek hazırlıyor | an elderly woman setting the table with care, warm grandmotherly presence |

### Yatak odası (`yatak_odasi`)

| ID | TR | EN prompt fragment |
|---|---|---|
| yo01 | Kadın kahve içiyor | a woman sitting on the bed sipping morning coffee, wearing comfortable robe, soft window light |
| yo02 | Adam telefon bakıyor | a man sitting on the edge of the bed checking his phone, casual morning routine |
| yo03 | Kitap okuyor | a person reading a book in bed, propped up on pillows, bedside lamp on |
| yo04 | Çift sohbet ediyor | a couple sitting on the bed talking, relaxed evening conversation |
| yo05 | Kıyafet seçiyor | a woman standing by the wardrobe, holding up an outfit, deciding what to wear |
| yo06 | Bebek uyuyor (yatakta) | a baby peacefully sleeping in the middle of the made bed, soft daylight |
| yo07 | Meditasyon | a person sitting cross-legged on the bed in a meditation pose, peaceful expression |
