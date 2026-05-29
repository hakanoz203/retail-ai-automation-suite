#  Konfor-Autonomous-Blog-Orchestrator

##  Proje Vizyonu 
Kurumsal markaların e-ticaret ve SEO stratejilerindeki en büyük kanayan yara, düzenli ve kaliteli içerik üretilememesidir. Bu proje, **"Konfor Yatak" markası için tasarladığım uçtan uca otonom bir Blog üretim otomasyonudur. 

Sistem, basit bir web formuna girilen konu başlığını alır; güncel web araştırması yapar, markanın diline (tone-of-voice) uygun SEO uyumlu bir makale yazar, bu metni analiz ederek ona uygun premium bir pazarlama görseli üretir 
ve tüm çıktıları( Blog yazısını ve resmini) CMS (Notion) veritabanına yayınlanmaya hazır bir taslak olarak aktarır.

##  Mimari ve Veri Akışı 
Bu mimari, n8n üzerinde çoklu AI ajanları (Multi-Agent) ve asenkron veri işleme pipeline ları kullanılarak yapılmıştır:

1. **Trigger (Input):** `n8n Form Trigger` üzerinden blog konusu (örn: "Bel fıtığı için doğru yatak seçimi") sisteme beslenir.
2. **Otonom Araştırma ve Yazım (LangChain + GPT-4.1-mini + Tavily):** `Blog Post Agent`, konuyu Tavily arama motoru ile canlı olarak araştırır. Sadece güncel ve kanıta dayalı verileri kullanarak makaleyi yazar.
3. **Görsel Orkestrasyonu (Structured Output Agent):** İkinci bir yapay zeka ajanı, yazılan Blog metnini analiz eder. E-ticaret standartlarında bir görsel için JSON formatında yapılandırılmış (Structured Parser) bir prompt ve başlık çıkarır.
4. **Görsel Üretimi (Fal.ai Nano-Banana):** Üretilen grafik promptu, Fal.ai modellerine HTTP POST ile asenkron bir kuyruk (queue) olarak gönderilir. Sistem, görsel render edilene kadar akıllı bir *Polling (Wait & Check)* döngüsü ile kuyruğu dinler.
5. **CMS Entegrasyonu (Notion API):** Yazı ve üretilen görselin URL'si birleştirilir. API kısıtlamaları otonom parçalama (chunking) algoritmalarıyla aşılarak Notion'a "Taslak" statüsünde kaydedilir.

##  Kurşun Geçirmez Mühendislik Detayları (Bulletproof Engineering)
* **Rakip İzolasyonu:** Tavily arama motorunun yapılandırmasında `exclude_domains` kullanılarak pazardaki dev rakiplerin (Trendyol, Hepsiburada, N11) verileri zehirlemesi engellenmiştir.
* **Karakter Limiti Yönetimi:** Notion API'sinin blok başına uyguladığı 2000 karakterlik katı donanım sınırı, n8n expression'ları içindeki `substring` metodolojisi ile dinamik olarak 4 farklı bloğa bölünerek otonom olarak aşılmıştır.
* **Katı Prompt Disiplini:** Ajanların yanlış çıktı üretmesini engellemek için sistem mesajlarında görselin "gerçekçi bir fotoğraf değil, boşluk bırakılmamış premium bir e-ticaret resmi/grafiği" olması zorunlu kılınmıştır.

##  Kurulum ve Dağıtım (Deployment)

Lokal veya sunucu tabanlı n8n ortamınızda bu mimariyi ayağa kaldırmak için:

1. **İçe Aktarma:** `KonforYatak Blog Post Form.json` dosyasını n8n arayüzünden Import edin.
2. **Kimlik Doğrulama (Credentials):**
   * **OpenAI:** GPT-4.1-mini modeline erişim için API anahtarınızı bağlayın.
   * **Tavily AI:** Web arama kapasitesi için Tavily Header Auth ekleyin.
   * **Fal.ai:** Görsel üretimi için `Authorization: Key [API_KEY]` şeklinde HTTP Header Auth kurgulayın.
   * **Notion:** Veritabanı entegrasyonu için Notion OAuth veya Internal Integration anahtarınızı girin.
3. **Konfigürasyon:** Notion node'u içindeki `databaseId` değerini kendi hedef veritabanınızın ID'si ile güncelleyin.
4. **Çalıştırma:** Akışı "Active" duruma getirin ve form URL'si üzerinden ilk konunuzu tetikleyerek otonom orkestrasyonu başlatın.
