# 06 — LLM ile Müşteri Geri Bildirimlerinin Analizi

## 1. Amaç ve yöntem

Müşteri geri bildirimlerinin duygu ve konu bakımından sınıflandırılması,
LLM çıktılarının doğruluğunun ölçülmesi ve olumsuz geri bildirim
veren yüksek churn riskli müşteriler için aksiyon önerileri
oluşturulması amaçlanmıştır.

Analizde `geri_bildirimler_temiz.csv` kullanılmıştır. Toplam 640
geri bildirimin 11 tanesinde metin bulunmadığından 629 kullanılabilir
geri bildirim değerlendirmeye alınmıştır.

Bu kayıtlar arasından `random_state=42` kullanılarak rastgele
200 geri bildirim seçilmiştir. Etiketleme için Google Gemini API
üzerinden `gemini-3.5-flash-lite` modeli kullanılmıştır.

Modelden her geri bildirim için aşağıdaki alanlar istenmiştir:

- **Duygu:** Olumlu, Olumsuz, Karışık, Alakasız
- **Konular:** randevu, fiyat, süre, personel, iş_kalitesi,
  yedek_parça, temizlik, diğer

Çıktılar yapılandırılmış JSON formatında alınmış ve
`llm_etiketleri.csv` dosyasına kaydedilmiştir. API kaynaklı
429 ve 503 hataları nedeniyle bazı kayıtlar yeniden denenmiş,
sonuçta 200 kaydın tamamı başarıyla etiketlenmiştir.

## 2. Altın set ve V1 kalite ölçümü

Seçilen 200 geri bildirimin 40 tanesi rastgele belirlenerek
elle etiketlenmiştir. Bu kayıtlar altın set olarak kullanılmıştır.
Altın set ile V1 LLM çıktıları `geri_bildirim_id` üzerinden
eşleştirilmiş ve bire bir kayıt kontrolü yapılmıştır.

### 2.1. Duygu sınıflandırması

V1 duygu sınıflandırmasında 40 kaydın tamamı doğru etiketlenmiştir.

| Duygu | Destek |
|---|---:|
| Olumlu | 18 |
| Olumsuz | 13 |
| Karışık | 9 |
| Alakasız | 0 |
| **Toplam** | **40** |

| Metrik | Sonuç |
|---|---:|
| Doğru sınıflandırılan kayıt | 40 |
| Toplam kayıt | 40 |
| Accuracy | %100 |

**Sınırlılık:** Altın sette Alakasız sınıfına ait örnek bulunmadığından
bu sınıfın performansı ölçülememiştir. Dolayısıyla %100 doğruluk,
modelin tüm duygu sınıflarında aynı başarıyı göstereceği anlamına gelmez.

### 2.2. Çoklu konu sınıflandırması

V1 çoklu etiket değerlendirmesinde mikro F1, makro F1 ve tam
eşleşme oranı birlikte incelenmiştir.

| Metrik | V1 sonucu |
|---|---:|
| Micro Precision | 0,8983 |
| Micro Recall | 0,8154 |
| Micro F1 | 0,8548 |
| Macro F1 | 0,8584 |
| Exact Match | %70 (28/40) |

Konu bazında F1 sonuçları:

| Konu | Precision | Recall | F1 | Destek |
|---|---:|---:|---:|---:|
| randevu | 1,000 | 0,750 | 0,857 | 8 |
| fiyat | 0,857 | 0,857 | 0,857 | 7 |
| süre | 0,846 | 1,000 | 0,917 | 11 |
| personel | 1,000 | 0,692 | 0,818 | 13 |
| iş_kalitesi | 1,000 | 0,667 | 0,800 | 9 |
| yedek_parça | 0,667 | 1,000 | 0,800 | 2 |
| temizlik | 1,000 | 1,000 | 1,000 | 4 |
| diğer | 0,818 | 0,818 | 0,818 | 11 |

Tam eşleşme, bir geri bildirimin bütün konu etiketlerinin
eksiksiz doğru tahmin edilmesini gerektirir. Bu nedenle
mikro F1 değerinden daha düşük gerçekleşmiştir.

Küçük destek sayısına sahip sınıfların sonuçları temkinli
yorumlanmalıdır. Özellikle yedek_parça sınıfında altın sette
yalnızca iki örnek bulunmaktadır.

## 3. Sistematik hata analizi

V1 değerlendirmesinde 40 kaydın 12'sinde en az bir konu etiketi
uyuşmazlığı görülmüştür. İnceleme sonucunda aşağıdaki hata
örüntüleri belirlenmiştir.

### 3.1. Personel ve iletişim etiketinin eksik atanması

Model bazı iletişim, erişim ve bilgilendirme sorunlarında
`personel` etiketini atlamıştır.

**Örnek — Geri bildirim 9383:**

> Telefonla kimseye ulaşamıyorum, çağrı merkezi sürekli meşg.

- Manuel etiket: randevu; personel
- LLM etiketi: diğer

**Örnek — Geri bildirim 9621:**

> Fatura tutarı verilen tekliften yüksek çıktı, önceden bilgilendirilmedim.

- Manuel etiket: fiyat; personel
- LLM etiketi: fiyat

Bu örnekler, çağrı merkezi erişimi ve müşteriye bilgi verilmemesi
gibi durumlarda iletişim boyutunun eksik yakalanabildiğini
göstermektedir.

### 3.2. İş kalitesi etiketinin eksik atanması

Bazı geri bildirimlerde model temel sorunu tespit etmesine
rağmen iş kalitesiyle ilişkili ikinci etiketi atlamıştır.

**Örnek — Geri bildirim 9013:**

> Aracımın içinde yağ lekesi bıraktılar, koltuk kirlenmişti.

- Manuel etiket: temizlik; iş_kalitesi
- LLM etiketi: temizlik

Benzer hata 9295 numaralı, aynı metne sahip geri bildirimde
de görülmüştür.

### 3.3. Benzer kavramların karıştırılması

**Örnek — Geri bildirim 9084:**

> Yedek araç verilmesi büyük kolaylık oldu.

- Manuel etiket: diğer
- LLM etiketi: yedek_parça

Model, yedek araç ifadesini yedek parça kategorisiyle
ilişkilendirmiştir. Oysa iki kavram farklıdır.

### 3.4. Metinde açıkça belirtilmeyen konuların atanması

**Örnek — Geri bildirim 9370:**

> Arıza giderilmemiş, aynı ses devam ediyor. Tekrar gitmek zorunda kaldım.

- Manuel etiket: iş_kalitesi
- LLM etiketi: iş_kalitesi; süre

Model, tekrar servis ziyaretini süre problemi olarak
yorumlamıştır. Ancak metinde doğrudan bir bekleme,
gecikme veya işlem süresi sorunu belirtilmemiştir.
Aynı hata 9633 numaralı geri bildirimde de görülmüştür.

### 3.5. Diğer gözlemler

9343 numaralı geri bildirimde şubede geçerli olmayan kampanya
mesajı nedeniyle oluşan fiyat boyutu atlanmıştır. 9348 numaralı
geri bildirimde ise POS cihazı arızası fiyat etiketiyle
ilişkilendirilmiştir. Bu örnekler, konu kategorilerinin
sınırlarının daha açık tanımlanması gerektiğini göstermektedir.

### 3.6. Hata analizi sonucu ve sınırlılıklar

Hata analizi; personel/iletişim ve iş kalitesi gibi ikincil
etiketlerin atlanması, benzer kavramların karıştırılması ve
metinde açıkça belirtilmeyen konuların atanması eğilimlerini
ortaya koymuştur.

Bazı uyuşmazlıklar aynı metne sahip geri bildirimlerde
tekrarlanmıştır. Bu nedenle 12 uyuşmazlık birbirinden bağımsız
12 hata örüntüsü olarak değerlendirilmemelidir.

Altın set tek bir kişi tarafından etiketlenmiştir. İkinci bir
etiketleyiciyle bağımsız tutarlılık ölçümü yapılmadığından,
bazı konu etiketleri yorum farklılığı içerebilir.

## 4. Prompt iyileştirmesi ve V1–V2 karşılaştırması (Bonus)

V1 hata analizinde belirlenen sorunları azaltmak amacıyla
sınıflandırma promptu V2 olarak güncellenmiştir.

V2 promptunda özellikle:

- Personel etiketinin iletişim, müşteriyle temas ve
  bilgilendirme sorunlarını da kapsaması netleştirilmiştir.
- Yedek araç ile yedek parça kavramları birbirinden ayrılmıştır.
- Süre etiketinin yalnızca açık bekleme, gecikme veya
  işlem süresi ifadelerinde kullanılması belirtilmiştir.
- Birden fazla konu açıkça destekleniyorsa çoklu etiket
  atanması, `diğer` etiketinin ise yalnızca listelenen
  konuların hiçbiri uygun değilse kullanılması açıklanmıştır.

Karşılaştırmada model değiştirilmemiş, V1 ve V2 için aynı
`gemini-3.5-flash-lite` modeli kullanılmıştır. Böylece temel
değişiklik prompt içeriği olmuştur.

### 4.1. Karşılaştırma örneklemi

Ücretsiz API kullanım sınırları nedeniyle V2 aşamasında
40 altın set kaydının ilk aşamada yalnızca 25'i tamamlanabilmiştir.
Bu nedenle V1 ve V2 karşılaştırması, her iki sürümde de
sonucu bulunan aynı 25 kayıt üzerinden yapılmıştır.

Kalan 15 kayıt geçici olarak beklemeye alınmıştır.
Bu bölümdeki sonuçlar 40 kayıtlık nihai değerlendirme
olarak yorumlanmamalıdır.

### 4.2. Duygu sınıflandırması karşılaştırması

| Metrik | V1 | V2 |
|---|---:|---:|
| Doğru tahmin | 25/25 | 25/25 |
| Accuracy | %100 | %100 |

Ortak 25 kaydın tamamında her iki sürüm de olumlu, olumsuz
ve karışık duygu etiketlerini doğru tahmin etmiştir.
Prompt iyileştirmesi bu örneklemde duygu sınıflandırmasına
ek bir performans artışı sağlamamıştır.

Bu 25 kayıtta Alakasız sınıfı bulunmadığından bu sınıf
değerlendirilememiştir.

### 4.3. Çoklu konu sınıflandırması karşılaştırması

| Metrik | V1 | V2 | Değişim |
|---|---:|---:|---:|
| Micro Precision | 0,9459 | 1,0000 | +0,0541 |
| Micro Recall | 0,8140 | 0,9070 | +0,0930 |
| Micro F1 | 0,8750 | 0,9512 | +0,0762 |
| Macro Precision | 0,9167 | 1,0000 | +0,0833 |
| Macro Recall | 0,8431 | 0,9125 | +0,0694 |
| Macro F1 | 0,8550 | 0,9497 | +0,0947 |
| Exact Match | %72 | %84 | +12 puan |

Konu bazında F1 karşılaştırması:

| Konu | V1 F1 | V2 F1 | Değişim |
|---|---:|---:|---:|
| randevu | 0,8889 | 0,8889 | 0,0000 |
| fiyat | 0,9091 | 0,9091 | 0,0000 |
| süre | 1,0000 | 1,0000 | 0,0000 |
| personel | 0,8750 | 1,0000 | +0,1250 |
| iş_kalitesi | 0,6667 | 0,8000 | +0,1333 |
| yedek_parça | 0,6667 | 1,0000 | +0,3333 |
| temizlik | 1,0000 | 1,0000 | 0,0000 |
| diğer | 0,8333 | 1,0000 | +0,1667 |

V2, ortak 25 kayıtta konu etiketleme performansını artırmıştır.
Micro F1 0,8750'ten 0,9512'ye, Macro F1 0,8550'ten
0,9497'ye yükselmiştir. Exact Match oranı %72'den %84'e
çıkmıştır.

V2'nin Micro Precision değeri 1,00 olmuş; bu örneklemde
yanlış konu etiketi üretmemiştir. En belirgin F1 artışları
personel, iş_kalitesi, yedek_parça ve diğer kategorilerinde
görülmüştür.

Bununla birlikte yedek_parça kategorisinin desteği yalnızca
bir kayıt olduğundan bu kategorideki artış genellenmemelidir.
Sonuçlar 25 kayıtla sınırlıdır ve kalan 15 kayıt tamamlandığında
aynı değerlendirme 40 kayıt üzerinden tekrarlanacaktır.

## 5. Yüksek churn riski ve olumsuz geri bildirim analizi

Churn modelinin test setinde ürettiği müşteri bazlı olasılıklar
ile LLM tarafından incelenen olumsuz geri bildirimler
eşleştirilmiştir.

Churn olasılığı 0,55 ve üzerinde olan 160 test müşterisi
yüksek riskli aday olarak değerlendirilmiştir.

İncelenen 200 geri bildirimlik örneklemde olumsuz geri bildirimi
bulunan üç yüksek riskli müşteri belirlenmiştir.

| Müşteri ID | Churn olasılığı | Gerçek churn | Olumsuz geri bildirim |
|---|---:|---:|---|
| 102827 | %73,21 | 0 | Çağrı merkezine ulaşılamaması |
| 103816 | %70,20 | 1 | POS cihazı nedeniyle ödeme sorunu |
| 103687 | %69,30 | 0 | Danışmanın yetersiz bilgilendirmesi |

### 5.1. Aksiyon önerileri

**Müşteri 102827 — Çağrı merkezine erişim sorunu**

Müşteriye güncel iletişim numarası üzerinden ulaşılması,
yaşadığı erişim sorununun dinlenmesi ve gerekiyorsa
servis/randevu sürecinde destek sağlanması önerilmiştir.

**Müşteri 103816 — Ödeme sorunu**

POS cihazı arızasının nedeni ilgili teknik birimle araştırılmalı
ve benzer aksaklıkların tekrarlanmaması için gerekli kontrol
sağlanmalıdır.

**Müşteri 103687 — Danışman iletişimi**

Müşterinin yanıt alamadığı konu ilgili birimden netleştirilmeli
ve müşteri yeniden aranarak doğru ve yeterli bilgilendirme
yapılmalıdır.

Bu öneriler LLM tarafından oluşturulmuş, ardından insan
kontrolünden geçirilmiştir. Önerilerde doğrulanmamış indirim,
kampanya veya hizmet vaadi oluşturulmamıştır.

**Önemli sınırlılık:** Bu üç müşteri, yalnızca LLM tarafından
incelenen 200 geri bildirimlik örneklemde olumsuz geri bildirimi
bulunan test müşterileridir. Tüm müşteri kitlesinde olumsuz
geri bildirimi bulunan müşteri sayısını temsil etmezler.

Ayrıca 102827 ve 103687 numaralı müşterilerin gerçek churn
etiketi 0'dır. Bu durum, yüksek risk tahmininin her müşteri
için gerçekleşmediğini ve modelin yanlış pozitif üretebildiğini
göstermektedir.

## 6. Şirketin sunmadığı kampanya ve tekliflerin önlenmesi

LLM'in şirket tarafından sunulmayan indirim, kampanya, hediye,
ücretsiz hizmet veya tazminat gibi vaatlerde bulunmasını
önlemek için üç katmanlı kontrol önerilmektedir:

1. **Prompt kısıtları:** Modelden yalnızca verilen geri bildirim
   ve doğrulanmış bilgiler üzerinden öneri üretmesi istenir.
   Şirket tarafından doğrulanmamış kampanya veya avantaj
   vaat etmesi açıkça yasaklanır.
2. **Onaylı bilgi kaynağı:** Aksiyon önerileri yalnızca şirketin
   onaylı hizmet politikaları ve doğrulanmış uygulamalarıyla
   sınırlandırılır. Bilgi bulunmuyorsa ilgili birime danışılması
   önerilir.
3. **İnsan kontrolü:** LLM çıktısı doğrudan müşteriye gönderilmez.
   Müşteri temsilcisi veya yetkili çalışan tarafından kontrol
   edildikten sonra kullanılır.

Bu yaklaşım, LLM'in doğrulanmamış ticari vaatlerde bulunma
riskini azaltır.

## 7. Genel değerlendirme

Çalışmada LLM, müşteri geri bildirimlerini yapılandırılmış
duygu ve konu etiketlerine dönüştürmek için kullanılmıştır.
V1'in 40 kayıtlık altın set değerlendirmesinde duygu
sınıflandırma doğruluğu %100, konu sınıflandırma Micro F1
skoru 0,8548 ve Exact Match oranı %70 olarak ölçülmüştür.

V1 hata analizi sonucunda konu tanımlarının netleştirilmesi
ve çoklu etiket kurallarının geliştirilmesiyle V2 promptu
oluşturulmuştur. Ortak 25 kayıt üzerindeki ön karşılaştırmada
V2'nin konu sınıflandırma Micro F1 skoru 0,9512'ye,
Exact Match oranı %84'e yükselmiştir. Duygu sınıflandırmasında
ise her iki sürüm de %100 doğruluk elde etmiştir.

V2 karşılaştırması henüz 25 kayıtla sınırlıdır. Kalan 15 kayıt
tamamlandığında 40 kayıtlık değerlendirme güncellenecektir.

LLM çıktıları müşteri aksiyonları için yardımcı karar desteği
olarak değerlendirilmiş; nihai öneriler insan kontrolüne
tabi tutulmuştur.