
# Veri Analitiği ve Yapay Zekâ Uzman Yardımcısı – Teknik Vaka

## 1. Veri Hazırlığı ve Keşif

### 1.1. Müşteri Seviyesinde Analiz Tablosu

Analizde müşteriler, araçlar, servis kayıtları ve geri bildirimlerden oluşan dört veri seti kullanılmıştır. Birleştirme öncesinde servis ve geri bildirim kayıtları müşteri seviyesinde özetlenmiş, böylece birden fazla araç veya servis kaydı bulunan müşteriler nedeniyle satır çoğalması önlenmiştir.

Temizleme sonrasında veri setlerinin büyüklükleri şöyledir:

| Veri seti | Ham satır | Temiz satır |
|---|---:|---:|
| Müşteriler | 4.235 | 4.200 |
| Araçlar | 4.786 | 4.786 |
| Servis kayıtları | 32.770 | 32.460 |
| Geri bildirimler | 640 | 640 |

Analiz popülasyonu, 30.06.2025 tarihine kadar en az bir servis kaydı bulunan 3.974 müşteriden oluşturulmuştur. Müşteri başına tek satır içeren analiz tablosu 18 sütundan oluşmaktadır.

Müşteri davranışlarını ölçmek için aşağıdaki RFM metrikleri hesaplanmıştır:

| Metrik | Ortalama | Medyan |
|---|---:|---:|
| Recency (gün) | 116,83 | 102 |
| Frequency (servis sayısı) | 6,31 | 6 |
| Monetary (TL) | 82.701,02 | 67.419,98 |

Recency, kesim tarihi ile son servis tarihi arasındaki gün sayısını; frequency, gözlem dönemindeki toplam servis kaydı sayısını; monetary ise aynı dönemdeki toplam servis tutarını ifade etmektedir.

### 1.2. Veri Kalitesi ve Uygulanan Kararlar

Veri kalitesi incelemesinde öne çıkan üç sorun ve alınan kararlar şunlardır:

| Tespit | Uygulanan karar ve gerekçe |
|---|---|
| Müşteri tablosunda 35, servis kayıtlarında 310 tam tekrar satırı bulundu. | Tekrarlanan ID gruplarındaki kayıtların tüm alanları aynı olduğu doğrulandı. Tekrar satırları kaldırılarak mükerrer sayım önlendi. |
| Servis kayıtlarında 40 benzersiz negatif kilometre değeri ve negatif olmayan değerler arasında kronolojik kilometre düşüşleri tespit edildi. | Değerlerin gerçek işlem veya veri giriş hatası olduğu kesinleştirilemediğinden otomatik silme ya da düzeltme yapılmadı. |
| Servis kayıtlarının %20,95'inde memnuniyet puanı eksikti. | Eksik puanlar doldurulmadı. Puan bulunmaması memnuniyetsizlik olarak yorumlanamayacağından eksik değerler korundu; memnuniyet karşılaştırmaları mevcut puanlar üzerinden yapıldı. |

Bunlara ek olarak doğum yılı alanındaki 97 eksik ve olağan dışı değerler de doğrulanabilir kaynak bulunmadığı için değiştirilmemiştir. Ham dosyalar korunmuş, temizlik işlemleri türetilmiş veri setlerine uygulanmıştır.

### 1.3. Keşifsel Veri Analizi Bulguları

Churn olan ve olmayan müşteriler arasında üç temel farklılık gözlenmiştir.

**Bulgu 1 – Son servis tarihinden itibaren geçen süre churn grubunda daha yüksektir.**

| Recency (gün) | Churn olmayan | Churn olan |
|---|---:|---:|
| Ortalama | 104,31 | 179,95 |
| Medyan | 93 | 174 |

Churn olan müşterilerin son servislerinden itibaren geçen süre belirgin biçimde daha uzundur. Bu durum, servis ziyaretleri arasındaki sürenin müşteri kaybı riskini değerlendirmede önemli bir aday gösterge olduğunu düşündürmektedir.

**Bulgu 2 – Servis sıklığı ve toplam harcama churn grubunda daha düşüktür.**

| Metrik | Churn olmayan ortalama | Churn olan ortalama |
|---|---:|---:|
| Frequency | 6,45 | 5,57 |
| Monetary (TL) | 84.645,07 | 72.903,99 |

Churn olan müşterilerin gözlem dönemindeki servis sayısı ve toplam servis tutarı daha düşüktür. Monetary değişkenindeki uç ve negatif değerler nedeniyle ortalamalar dikkatli yorumlanmalıdır.

**Bulgu 3 – Müşteri memnuniyeti churn grupları arasında farklılaşmaktadır.**

| Memnuniyet | Churn olmayan | Churn olan |
|---|---:|---:|
| Puanlı müşteri sayısı | 3.275 | 652 |
| Ortalama puan | 4,008 | 3,793 |
| Medyan puan | 4,000 | 3,775 |

Müşteri bazında yapılan Mann–Whitney U testi sonucunda iki grubun memnuniyet puanı dağılımları arasında istatistiksel olarak anlamlı fark bulunmuştur (p < 0,001). Churn olmayan müşterilerin memnuniyet puanları daha yüksektir. Bu bulgu ilişki göstermekte olup tek başına nedensellik kanıtı değildir.

### 1.4. İş Birimi Sorusu: Servis Merkezlerinde Memnuniyet Farkı

Servis merkezlerinin ortalama memnuniyet puanları aşağıda verilmiştir.

| Servis merkezi | Ortalama memnuniyet |
|---|---:|
| Maslak | 3,92 |
| Kadıköy | 3,98 |
| Ankara1 | 3,99 |
| İzmir1 | 3,93 |
| Bursa1 | 3,94 |
| Antalya1 | 3,98 |
| Kocaeli1 | 3,96 |

Servis merkezlerinin memnuniyet puanları arasında istatistiksel olarak anlamlı bir farklılık bulunmuştur (Kruskal-Wallis: H = 20,7112; p = 0,0021). Ancak Bonferroni düzeltmeli ikili karşılaştırmalarda yalnızca Ankara1 ile Maslak arasındaki fark anlamlı bulunmuştur. Bu karşılaştırmaya ait rank-biserial etki büyüklüğü 0,0418 olup farkın büyüklüğünün oldukça küçük olduğunu göstermektedir.

Bu nedenle sonuçlar, servis merkezleri arasında genel bir performans veya kapasite farkı bulunduğu şeklinde yorumlanmamalıdır. Doğrudan kapasite aktarımı önermek yerine Maslak'taki müşteri geri bildirimlerinin, servis süreçlerinin ve puan verilmeyen kayıtların incelenmesi önerilmektedir.


## 2. Churn Tahmin Modeli

### 2.1. Hedef Değişken ve Özellik Seçimi

Model, 30.06.2025 itibarıyla en az bir servis kaydı bulunan 3.974 müşteri üzerinden oluşturulmuştur. Hedef dönem 01.07.2025–30.06.2026 olarak belirlenmiştir. Bu dönemde servis kaydı bulunmayan müşteriler `churn=1`, en az bir servis kaydı bulunan müşteriler `churn=0` olarak etiketlenmiştir.

| Sınıf | Müşteri sayısı | Oran |
|---|---:|---:|
| Churn (1) | 658 | %16,56 |
| Churn değil (0) | 3.316 | %83,44 |
| **Toplam** | **3.974** | **%100** |

Modelde 3 kategorik ve 9 sayısal olmak üzere toplam 12 özellik kullanılmıştır: 
- Kategorik: `sehir`, `musteri_tipi`, `kayit_kanali`
- Sayısal: `frequency`, `monetary`, `recency`, `arac_sayisi`, `ortalama_model_yili`, `aktif_garantili_arac_sayisi`, `geri_bildirim_sayisi`, `metni_olan_geri_bildirim_sayisi`, `farkli_kanal_sayisi`

### 2.2. Eğitim, Doğrulama ve Model Seçimi

Veri, sınıf oranları korunarak %80 eğitim (3.179 müşteri) ve %20 test (795 müşteri) şeklinde ayrılmıştır. Modeller, eğitim verisi üzerinde 5 katlı Stratified K-Fold çapraz doğrulama ile karşılaştırılmıştır.

En sık sınıfı tahmin eden DummyClassifier %83,40 doğruluk elde etmesine rağmen churn müşterilerinin hiçbirini tespit edememiştir. Bu nedenle model seçiminde accuracy yanında precision, recall, F1 ve PR-AUC metrikleri de dikkate alınmıştır.

| Model | CV ROC-AUC | CV PR-AUC | CV F1 (0,50) |
|---|---:|---:|---:|
| Random Forest | 0,7515 | 0,4601 | 0,4572 |
| Gradient Boosting (100) | 0,7556 | 0,4976 | 0,4525 |
| Gradient Boosting (200) | 0,7554 | 0,4980 | 0,4526 |

Gradient Boosting modelleri PR-AUC açısından daha yüksek sonuç verirken Random Forest az farkla daha yüksek F1 elde etmiştir. Random Forest, çapraz doğrulama sonuçlarında F1 metriğinde en iyi sonucu verdiği için nihai model adayı olarak belirlenmiştir.

### 2.3. Model Değerlendirmesi ve Eşik Seçimi

Sınıf dağılımı dengesiz olduğu için accuracy tek başına yeterli bir değerlendirme ölçütü olarak görülmemiştir. Churn müşterilerini yakalama başarısını ölçmek için recall, doğru işaretlenen müşterilerin oranını değerlendirmek için precision, bu iki metriğin dengesini görmek için F1 kullanılmıştır. ROC-AUC ve PR-AUC ise modelin farklı eşiklerdeki ayırt etme performansını değerlendirmek amacıyla raporlanmıştır.

DummyClassifier %83,40 accuracy elde etmesine rağmen churn müşterilerinin hiçbirini tespit edememiştir (recall=0). Random Forest ise 0,50 eşikte %50,76 churn recall ve 0,4085 F1 değerine ulaşmıştır. Bu sonuç, yüksek accuracy değerinin tek başına yeterli olmadığını göstermektedir.

Random Forest modelinin test setindeki ROC-AUC değeri 0,7144, PR-AUC değeri 0,4651'dir. Farklı karar eşiklerinin sonuçları aşağıda verilmiştir.

| Eşik | Precision | Recall | F1 | İşaretlenen müşteri | Doğru churn |
|---:|---:|---:|---:|---:|---:|
| 0,50 | 0,3418 | 0,5076 | 0,4085 | 196 | 67 |
| 0,55 | 0,3812 | 0,4621 | 0,4178 | 160 | 61 |
| 0,60 | 0,4554 | 0,3864 | 0,4180 | 112 | 51 |

Eşik seçimi eğitim verisindeki 5 katlı çapraz doğrulama sonuçlarına göre değerlendirilmiştir. 0,50 eşiği en yüksek CV F1 değerini (0,4572) verirken 0,55 eşiğinin F1 değeri (0,4567) oldukça yakındır ve daha az müşteriyi işaretlemektedir. Bu nedenle 0,55 eşiği, operasyonel kapasite ve yanlış sınıflandırma maliyetleri netleşene kadar geçici aday olarak belirlenmiştir.

0,55 eşiğinde test setinde 160 müşteri işaretlenmiş; bunların 61'i gerçekten churn olmuş, 99'u churn olmamıştır. Ayrıca 71 churn müşterisi kaçırılmıştır. Modelin değerlendirmesinde sınıf oranlarını koruyan stratified %80/%20 eğitim-test ayrımı ve eğitim verisinde 5 katlı Stratified K-Fold çapraz doğrulama kullanılmıştır. Stratified yöntem, her bölümde sınıf oranlarının korunmasını sağlamıştır. Zaman bazlı doğrulama yapılmadığından modelin farklı dönemlerdeki performansı ayrıca test edilmelidir.

Bu sonuçlar, modelin tek başına otomatik müşteri kararı veya kampanya tetiklemesi için yeterli olmadığını göstermektedir.

### 2.4. Önemli Değişkenler ve İş Yorumu

Test seti üzerinde hesaplanan permutation importance sonuçlarında öne çıkan beş değişken şöyledir:

| Sıra | Değişken | Önem |
|---:|---|---:|
| 1 | recency | 0,0844 |
| 2 | ortalama_model_yili | 0,0402 |
| 3 | musteri_tipi | 0,0180 |
| 4 | frequency | 0,0065 |
| 5 | monetary | 0,0062 |

- **Recency:** Son servisten itibaren geçen süreyi gösterir. Servis ziyaretlerinin seyrekleşmesi müşteri kaybı açısından önemli bir sinyal olabilir. Müşteri takibi ve zamanında iletişim açısından iş değeri yüksektir.
- **Ortalama model yılı:** Müşterinin araçlarının ortalama model yılını temsil eder. Araç yaşı; bakım ihtiyacı, servis sıklığı ve müşterinin servis tercihleriyle ilişkili olabilir. Müşteri segmentasyonu açısından anlamlıdır.
- **Müşteri tipi:** Bireysel veya kurumsal gibi müşteri grupları arasındaki davranış farklılıklarını yansıtabilir. Farklı müşteri gruplarına uygun iletişim ve bağlılık stratejileri geliştirilmesine yardımcı olabilir.
- **Frequency:** Gözlem dönemindeki servis sıklığını gösterir. Müşterinin servis kullanım alışkanlığı hakkında bilgi verir ve bağlılık düzeyinin değerlendirilmesinde kullanılabilir.
- **Monetary:** Gözlem dönemindeki toplam servis harcamasını temsil eder. Müşterinin servis işletmesi açısından ekonomik değerini anlamaya ve müşteri segmentasyonu yapmaya yardımcı olabilir.

Sonuçlar, özellikle son servisten itibaren geçen sürenin churn tahmininde öne çıktığını göstermektedir. Değişken önemleri modelin tahmin davranışını yansıtır; değişkenlerin churn üzerindeki etkisinin yönünü veya nedenselliği göstermez.

### 2.5. Sınırlamalar ve İş Açısından Değerlendirme

Modelin test ROC-AUC değeri 0,7144 olduğundan ayırt etme gücü sınırlıdır. Tek hedef dönemi bulunduğu için farklı dönemlerde zaman bazlı doğrulama yapılamamıştır. Ayrıca 0,55 eşikteki 99 yanlış pozitif ve 71 yanlış negatif, müşteri iletişim maliyetleri ile kaçırılan müşterilerin iş etkisinin birlikte değerlendirilmesi gerektiğini göstermektedir.

Model, müşteri temsilcilerine inceleme önceliği sunabilecek bir karar destek aracı olarak değerlendirilebilir. Üretim öncesinde ileri tarihli verilerle doğrulama, eşik ve maliyet analizi, model izleme ve periyodik performans kontrolü yapılmalıdır.

## 3. LLM ile Müşteri Geri Bildirimlerinin Analizi

### 3.1. Etiketleme ve Yöntem

640 geri bildirimin 11'inde metin bulunmadığından, kalan 629 kayıt arasından `random_state=42` ile rastgele 200 geri bildirim seçilmiştir. Google Gemini API üzerinden `gemini-3.5-flash-lite` modeli kullanılarak her kayıt için duygu (Olumlu, Olumsuz, Karışık, Alakasız) ve çoklu konu etiketleri (`randevu`, `fiyat`, `süre`, `personel`, `iş_kalitesi`, `yedek_parça`, `temizlik`, `diğer`) üretilmiştir. Sonuçlar yapılandırılmış CSV formatında kaydedilmiştir.

### 3.2. Altın Set ile Kalite Ölçümü

Seçilen 200 geri bildirimin 40'ı rastgele seçilerek elle etiketlenmiş ve LLM sonuçlarıyla karşılaştırılmıştır.

Duygu sınıflandırmasında 40 kaydın tamamı doğru etiketlenmiştir (%100 doğruluk). Ancak altın sette `Alakasız` sınıfına ait örnek bulunmadığından bu sınıfın başarısı ölçülememiştir.

Çoklu konu sınıflandırması sonuçları:

| Metrik | V1 sonucu |
|---|---:|
| Micro Precision | 0,8983 |
| Micro Recall | 0,8154 |
| Micro F1 | 0,8548 |
| Macro F1 | 0,8584 |
| Exact Match | %70 (28/40) |

40 kaydın 12'sinde en az bir konu etiketi uyuşmazlığı görülmüştür. Hata incelemesinde öne çıkan örnekler:

- **İletişim/personel etiketinin atlanması:** “Telefonla kimseye ulaşamıyorum, çağrı merkezi sürekli meşg.” metnine manuel olarak `randevu;personel`, LLM tarafından yalnızca `diğer` etiketi verilmiştir.
- **İş kalitesi etiketinin atlanması:** “Aracımın içinde yağ lekesi bıraktılar, koltuk kirlenmişti.” metninde manuel etiket `temizlik;iş_kalitesi`, LLM etiketi ise yalnızca `temizlik` olmuştur.
- **Benzer kavramların karıştırılması:** “Yedek araç verilmesi büyük kolaylık oldu.” ifadesi LLM tarafından `yedek_parça` olarak etiketlenmiştir.
- **Metinde açıkça belirtilmeyen konunun atanması:** Arızanın giderilmediğini ve tekrar servise gitmek zorunda kalındığını belirten bir geri bildirime, açık bir bekleme veya gecikme ifadesi olmamasına rağmen `süre` etiketi de atanmıştır.

Bulgular, modelin özellikle ikincil konu etiketlerini atlama ve yakın kavramları karıştırma eğilimini göstermektedir. Altın set tek kişi tarafından etiketlendiğinden manuel etiketlerde yorum farklılığı olabileceği de dikkate alınmalıdır.

### 3.3. Prompt İyileştirmesi (Bonus)

Hata analizine göre personel/iletişim kapsamı, yedek araç-yedek parça ayrımı, süre etiketinin kullanım koşulları ve çoklu etiket kuralları netleştirilerek V2 promptu hazırlanmıştır. Aynı Gemini modeliyle yapılan karşılaştırma, API sınırları nedeniyle ortak 25 altın set kaydı üzerinden gerçekleştirilmiştir.

| Metrik | V1 | V2 |
|---|---:|---:|
| Micro F1 | 0,8750 | 0,9512 |
| Macro F1 | 0,8550 | 0,9497 |
| Exact Match | %72 | %84 |

Ortak 25 kayıtta konu sınıflandırma sonuçları iyileşmiştir. Duygu sınıflandırmasında her iki sürüm de %100 doğruluk elde etmiştir. Ancak V2 karşılaştırması 25 kayıtla sınırlı olduğundan sonuçlar ön değerlendirme niteliğindedir.

### 3.4. Yüksek Churn Riskli Müşteriler için Aksiyon Önerileri

Churn olasılığı 0,55 ve üzerinde olan test müşterileri arasından, incelenen 200 geri bildirimlik örneklemde olumsuz geri bildirimi bulunan üç müşteri seçilmiştir. LLM'den her müşteri için müşteri temsilcisine yönelik tek cümlelik aksiyon önerisi üretilmiştir.

| Müşteri | Churn olasılığı | Geri bildirim konusu | Aksiyon önerisi |
|---|---:|---|---|
| 102827 | %73,21 | Çağrı merkezine ulaşılamaması | Müşteri güncel iletişim numarasından aranarak erişim sorunu dinlenmeli ve servis/randevu sürecinde ihtiyaç duyduğu destek belirlenmelidir. |
| 103816 | %70,20 | POS cihazı nedeniyle ödeme sorunu | Ödeme sırasında yaşanan POS sorunu ilgili teknik birimle incelenmeli ve müşteri çözüm hakkında bilgilendirilmelidir. |
| 103687 | %69,30 | Danışmanın yetersiz bilgilendirmesi | Müşterinin yanıt alamadığı konu ilgili birimden netleştirilerek müşteri yeniden aranmalı ve doğru bilgi verilmelidir. |

Bu üç müşteri, yalnızca 200 kayıtlık örneklemde tespit edilen olumsuz geri bildirimli yüksek riskli müşterilerdir; tüm müşteri kitlesini temsil etmezler. Üç müşteriden ikisinin gerçek churn etiketi 0'dır. Bu durum, tahminlerin kesin sonuç olarak değerlendirilmemesi gerektiğini göstermektedir.

LLM'in şirketin sunmadığı kampanya veya indirimleri önermesini engellemek için promptta doğrulanmamış ticari vaatler açıkça yasaklanmalı, öneriler yalnızca şirketin onaylı politika ve hizmet bilgilerine dayandırılmalı ve müşteriyle paylaşılmadan önce yetkili çalışan tarafından kontrol edilmelidir.

## 4. Hazır Raporun Eleştirisi

Taslak rapor, veri hazırlığı, model performansı ve iş sonuçlarının yorumlanması açısından incelenmiştir. İnceleme sonucunda aşağıdaki hata ve tutarsızlıklar tespit edilmiştir.

| Hata | Neden yanlış? | Doğrusu |
|---|---|---|
| **1. Modelin %84 doğruluk ve 0,99 ROC-AUC ile başarılı olduğu ve doğrudan üretime alınabileceği iddiası.** | Gerçek test sonuçlarında Random Forest modelinin doğruluğu 0,50 eşikte %75,60, 0,55 eşikte %78,62; ROC-AUC değeri 0,7144'tür. Ayrıca doğruluk tek başına terk sınıfındaki tahmin başarısını göstermez. | Model performansı gerçek test sonuçlarıyla raporlanmalıdır. Üretime geçmeden önce zamansal doğrulama, operasyonel maliyet ve yanlış tahminlerin etkisi değerlendirilmelidir. |
| **2. Birleştirme sonrası 32.770 satırın müşteri seviyesinde analiz edildiği iddiası.** | 32.770, servis kayıtlarının ham satır sayısıdır; müşteri sayısını temsil etmez. Tek müşterinin birden fazla servis kaydı olabilir. | Müşteri seviyesinde analiz için servis kayıtları müşteri bazında özetlenmelidir. Analiz popülasyonu, kesim tarihinden önce en az bir servis kaydı bulunan 3.974 müşteridir. |
| **3. Veri setinde 8.400 müşteri bulunduğu iddiası.** | Bu sayı temizlenmiş veriyle ve analiz popülasyonuyla uyuşmamaktadır. Tekilleştirme sonrasında 4.200 müşteri kaydı bulunmaktadır. Kesim tarihi öncesinde servis kaydı olan müşteri sayısı ise 3.974'tür. | Analiz kapsamındaki müşteri sayısı 3.974 olarak belirtilmelidir. |
| **4. Eksik memnuniyet puanlarının 3,94 ortalamasıyla doldurulması.** | Eksik değerleri ortalamayla doldurmak, gözlemlenmemiş memnuniyet puanlarına yapay değer atar ve dağılımı etkileyebilir. Ayrıca eksik puanların neden oluştuğu incelenmeden bu işlem yapılmıştır. | 5.251 servis kaydında (%20,95) memnuniyet puanı eksiktir. Bu değerler doldurulmadan bırakılmış, memnuniyet analizleri mevcut puanlar üzerinden yapılmıştır. |
| **5. Rastgele %80 eğitim / %20 test ayrımının genelleme yeteneğini ölçmek için yeterli görülmesi.** | Rastgele ayrım, zaman içinde geleceğe yönelik tahmin senaryosunu tam olarak temsil etmeyebilir. Özellikle müşteri ve servis geçmişinin zamansal yapısı dikkate alınmalıdır. | Mevcut rastgele ayrım temel değerlendirme için kullanılabilir; ancak üretim öncesinde geçmiş dönemle eğitip daha sonraki bir dönemle test eden zamansal doğrulama yapılmalıdır. |
| **6. Modele mevcut tüm değişkenlerin verilmesi ve algoritmanın ilgisiz değişkenleri kendisinin eleyeceğinin varsayılması.** | Her değişkenin modele verilmesi güvenli değildir. Özellikle tahmin anında bilinmeyen veya hedef döneminden bilgi taşıyan değişkenler veri sızıntısına neden olabilir. | Yalnızca 30 Haziran 2025 itibarıyla bilinebilen değişkenler kullanılmalıdır. `crm_musteri_durumu` ve `son_iletisim_notu` gibi değişkenler, tahmin anındaki kullanılabilirlikleri doğrulanmadığından modele dahil edilmemelidir. |
| **7. %84 doğruluğun operasyonel kullanım için yeterli kabul edilmesi.** | Sadece çoğunluk sınıfını tahmin eden Dummy model de %83,40 doğruluk elde etmiş, ancak terk eden müşterilerin hiçbirini tespit edememiştir. Bu nedenle doğruluk tek başına yanıltıcıdır. | Terk sınıfı için precision, recall ve F1-score gibi metrikler de değerlendirilmelidir. Random Forest modelinin 0,50 eşik değerindeki terk sınıfı recall değeri %50,76, F1-score değeri %40,85'tir. |
| **8. `crm_musteri_durumu` değişkeninin 0,61 önem değeriyle en güçlü değişken olarak sunulması ve güncellenmesinin önerilmesi.** | Bu değişken modelde kullanılan özellikler arasında değildir. Ayrıca tahmin anından sonraki CRM bilgileri hedefe ilişkin bilgi taşıyabilir. Dolayısıyla önem değeri ve buna dayalı öneri mevcut analizle desteklenmemektedir. | Test verisindeki permutation importance sonuçlarına göre en önemli beş değişken sırasıyla recency, ortalama model yılı, müşteri tipi, frequency ve monetary değeridir. |
| **9. p=0,03 sonucunun hipotezin %97 ihtimalle doğru olduğunu göstermesi ve düşük memnuniyetin terk davranışına neden olduğunun ileri sürülmesi.** | p-değeri, hipotezin doğru olma olasılığını göstermez. Ayrıca istatistiksel ilişki tek başına nedensellik kanıtı değildir. Taslaktaki t-testi sonucu da gerçekleştirilen analizle uyuşmamaktadır. | Müşteri bazında memnuniyet ortalaması terk etmeyenlerde 4,008, terk edenlerde 3,793'tür. Mann-Whitney U testi sonucunda p=2,16×10⁻²⁶ bulunmuştur. Bu sonuç gruplar arasında istatistiksel fark olduğunu gösterir; nedensellik göstermez. |
| **10. SM-Maslak için ciddi kalite sorunu olduğu ve iş hacminin başka merkezlere aktarılması gerektiği iddiası.** | Merkezler arasındaki ortalama puan farkları tek başına ciddi kalite sorununu veya kapasite aktarımının gerekliliğini göstermez. Çoklu karşılaştırma sonrasında yalnızca Ankara1 ile Maslak arasındaki fark anlamlıdır ve etki büyüklüğü düşüktür. | Servis merkezi ortalamaları ve Kruskal-Wallis testi birlikte değerlendirilmelidir. Yalnızca Ankara1-Maslak karşılaştırmasında Bonferroni düzeltmesi sonrasında anlamlı fark bulunmuştur; etki büyüklüğü küçüktür. Bu nedenle kapasite aktarımı yerine süreç ve geri bildirimlerin incelenmesi önerilir. |

**Genel değerlendirme:** Taslak raporda model performansı olduğundan yüksek gösterilmiş, müşteri ve servis kayıtlarının seviyeleri karıştırılmış, eksik değerler gerekçesiz doldurulmuş ve istatistiksel bulgulardan nedensel sonuçlar çıkarılmıştır. Ayrıca modelde kullanılmayan değişkenlere önem atfedilmiştir. Bu nedenlerle taslak rapor mevcut haliyle karar alma veya üretim sürecinde kullanılmamalıdır.
