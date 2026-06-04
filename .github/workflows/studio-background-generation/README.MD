#  Studio-BG-Gen: Autonomous E-Commerce Lifestyle Orchestrator for Konfor

##  Proje Hakkında 
E-ticaret ve mobilya sektöründe yapay zeka destekli görsel üretimir. Standart "Text-to-Image" modelleri, ürünün geometrisini bozar, orijinali değiştirir ve objeyi odanın ortasında havada yüzdürür. 

**Studio-BG-Gen**, n8n ve **Fal.ai (Flux.2 LoRA Add-Background)** altyapısını kullanarak bu sorunu çözer. Ürünün orijinal piksellerini ve kadrajını koruyarak, onu profesyonel, minimalist ve satışa hazır bir stüdyo ortamına yerleştiren otonom bir pipeline dır.

##  Mimari ve Veri Akışı (The Pipeline)
Sistem, hantallıktan ve sonsuz döngü (loop) risklerinden arındırılmış, senkronize bir Google Drive - REST API mimarisi üzerine inşa edilmiştir:

1. **Google Drive Trigger (Polling):** "01_RAW_INPUT" klasörünü dinler. HEIC/TIFF hatalarını önlemek için sıkı bir MIME-type (sadece PNG/JPEG) ve boyut doğrulamasından (If node) geçer.
2. **Binary to Base64 (Code Node):** N8n'in disk tabanlı (`separate` mode) çalışma mantığında oluşan "undefined" ve context kaybetme krizlerini aşmak için, buffer üzerinden veriyi doğrudan RAM'e çekip Base64 formatına çevirir.
3. **Fal.ai Generate (Synchronous API):** Kuyruk (queue) mimarisinin yarattığı gereksiz "Status Check" döngülerini devreden çıkararak, modelden doğrudan son görsel URL'sini alır. `JSON.stringify` metodu ile Bad Control Character hataları sıfıra indirilmiştir.
4. **Google Drive Upload:** İşlenmiş, e-ticaret standartlarındaki yüksek çözünürlüklü lifestyle görselini "02_LIFESTYLE_OUTPUT" klasörüne yazar.
5. **Archive Original (Cleanup):** Polling mekanizmasının aynı ham görseli tekrar işleyip API bakiyesini sıfırlamasını (sonsuz döngü) engellemek için, orijinal dosyayı mutlak ID (absolute path) üzerinden "03_ARCHIVE_RAW" klasörüne taşır.

## Prompt Engineering
Yapay zekanın "pastel" veya "oda" komutlarında düştüğü klişe tuzaklar (masa ayağının havada çıkması veya odayı dolduran dikkat dağıtıcı eşyalar vs) mutlak kısıtlamalarla (Absolute Constraints) engellenmiştir:

* **Kadraj Kilidi:** `tight close-up product shot` ve `maintaining exact original scale` komutlarıyla kameranın geriye çekilmesi ve ürünün küçülmesi yasaklanmıştır.
* **Derinlik İptali:** Objeyi "müze eseri" gibi odanın ortasında bırakmasını önlemek için `pushed flat against the back wall` komutu ile arkadaki derinlik kapatılmıştır.
* **Renk Harmonisi (Pembe-Savar):** Modelin halüsinatif renk sapmalarını engellemek adına `sage green, slate gray, warm beige, or soft navy` gibi e-ticarete uygun, mat ve nötr spesifik renk havuzları tanımlanmıştır.

##  Kurulum (Setup)

Bu repoyu kendi lokal n8n ortamınızda kurmak için:

1. **N8n Workflow Import:** `studio-background-generation.json` dosyasını n8n arayüzünden içeri aktarın (Import).
2. **Google Drive Yapılandırması:** * Drive'ınızda 3 adet klasör oluşturun: `01_RAW_INPUT`, `02_LIFESTYLE_OUTPUT`, `03_ARCHIVE_RAW`.
   * Akıştaki Drive node'larında bu klasörlerin ID'lerini güncelleyin.
3. **Credentials (Kimlik Doğrulama):**
   * N8n üzerinden Google Drive OAuth2 veya Service Account bağlantınızı yapın.
   * "Generic Credential Type" altında bir `httpHeaderAuth` oluşturun. Name: `Authorization`, Value: `Key Fal_AI_Key` şeklinde yapılandırın.
4. **Deployment:** Workflow u Publish edin ve ve `01_RAW_INPUT` klasörüne arkaplasnız saf bir ürün(tercihen mobilya) fotoğrafı yükleyerek bandı tetikleyin.

---
*Built for production-grade e-commerce pipelines.*
