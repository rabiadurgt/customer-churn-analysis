# 5. Churn Tahmin Modeli

## 5.1. Problem tanımı ve hedef değişken

Churn tahmini, 30 Haziran 2025 itibarıyla geçmişte en az bir servis kaydı bulunan müşteriler üzerinden gerçekleştirilmiştir.

- **Özellik gözlem dönemi:** 01.01.2022–30.06.2025
- **Özellik kesim tarihi:** 30.06.2025
- **Hedef dönem:** 01.07.2025–30.06.2026
- **Churn = 1:** Hedef dönemde servis kaydı bulunmayan müşteri.
- **Churn = 0:** Hedef dönemde en az bir servis kaydı bulunan müşteri.

Analiz popülasyonu 3.974 müşteriden oluşmaktadır. Bunların 3.316'sı (%83,44) churn değil, 658'i (%16,56) churn olarak etiketlenmiştir.

Hedef dönemdeki servis kayıtları yalnızca hedef değişkeni oluşturmak için kullanılmış, model özelliklerine dahil edilmemiştir.

## 5.2. Özellik seçimi ve veri sızıntısı kontrolleri

Modelde 12 özellik kullanılmıştır:

- Kategorik: `sehir`, `musteri_tipi`, `kayit_kanali`
- Sayısal: `frequency`, `monetary`, `recency`, `arac_sayisi`, `ortalama_model_yili`, `aktif_garantili_arac_sayisi`, `geri_bildirim_sayisi`, `metni_olan_geri_bildirim_sayisi`, `farkli_kanal_sayisi`

Müşteri kimliği, son servis tarihi, geri bildirim tarihleri ve hedef dönem servis sayısı özelliklerden çıkarılmıştır. Özellikle hedef dönem servis sayısının modele dahil edilmesi doğrudan hedef sızıntısına yol açabileceğinden engellenmiştir.

Recency değerleri ile son servis tarihi arasındaki tutarlılık kontrol edilmiş; geçersiz tarih veya gün farkı uyumsuzluğu bulunmamıştır. Seçilen özelliklerde eksik değer gözlenmemiştir.

Kategorik değişkenler One-Hot Encoding ile dönüştürülmüş, sayısal değişkenler mevcut değerleriyle modele aktarılmıştır.

## 5.3. Eğitim ve test ayrımı

Veri, churn sınıf oranları korunarak stratified yöntemle %80 eğitim ve %20 test olarak ayrılmıştır. Ayrım `random_state=42` ile tekrarlanabilir hale getirilmiştir.

- Eğitim: 3.179 müşteri
- Test: 795 müşteri

Model karşılaştırması eğitim verisi üzerinde 5 katlı Stratified K-Fold çapraz doğrulama ile yapılmıştır. Test seti nihai değerlendirme için ayrılmıştır.

Bu veri setinde tek bir hedef dönemi bulunduğundan farklı tarihsel dönemlerde tekrarlanan zaman bazlı doğrulama yapılamamıştır. Bu nedenle rastgele bölme sonuçları, gelecekteki dönemlerdeki performansın garantisi değildir.

## 5.4. Baseline modeli

En sık sınıfı tahmin eden DummyClassifier baseline modeli test setinde %83,40 doğruluk elde etmiştir. Ancak churn sınıfını hiç tahmin etmemiştir:

- Churn precision: 0
- Churn recall: 0
- Churn F1: 0

Sınıf dağılımının dengesiz olması nedeniyle yalnızca accuracy değerine bakmanın yanıltıcı olabileceği görülmüştür. Bu nedenle model değerlendirmesinde precision, recall, F1 ve PR-AUC metrikleri de kullanılmıştır.

## 5.5. Model karşılaştırması

Random Forest ve Gradient Boosting modelleri aynı özellikler ve aynı 5 katlı çapraz doğrulama düzeniyle karşılaştırılmıştır. Gradient Boosting modellerinde sınıf dengesizliğini ele almak için her eğitim katında dengeli örnek ağırlıkları kullanılmıştır.

Eğitim çapraz doğrulama sonuçları:

| Model | ROC-AUC | PR-AUC | F1 (0,50) |
|---|---:|---:|---:|
| Random Forest - mevcut | 0,7515 | 0,4601 | 0,4572 |
| Gradient Boosting - 100 | 0,7556 | 0,4976 | 0,4525 |
| Gradient Boosting - 200 | 0,7554 | 0,4980 | 0,4526 |

Gradient Boosting modelleri PR-AUC açısından daha yüksek sonuç vermiştir. Random Forest ise 0,50 eşikte az farkla daha yüksek F1 elde etmiştir. Modellerin sonuçları birbirine yakın olduğundan yalnızca tek bir çapraz doğrulama metriğine göre karar verilmemiştir.

Aynı bağımsız test setindeki değerlendirmede Random Forest, Gradient Boosting modeline göre ROC-AUC, PR-AUC ve karşılaştırılan eşiklerde F1 açısından az farkla daha iyi sonuç vermiştir. Bu nedenle nihai model adayı olarak Random Forest seçilmiştir.

## 5.6. Nihai modelin test performansı

Random Forest test sonuçları:

- ROC-AUC: 0,7144
- PR-AUC: 0,4651

Eşik karşılaştırması:

| Eşik | Accuracy | Precision | Recall | F1 | TN | FP | FN | TP | İşaretlenen müşteri |
|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 0,50 | 0,7560 | 0,3418 | 0,5076 | 0,4085 | 534 | 129 | 65 | 67 | 196 |
| 0,55 | 0,7862 | 0,3812 | 0,4621 | 0,4178 | 564 | 99 | 71 | 61 | 160 |
| 0,60 | 0,8214 | 0,4554 | 0,3864 | 0,4180 | 602 | 61 | 81 | 51 | 112 |

Eşik seçimi için eğitim verisindeki çapraz doğrulama sonuçları esas alınmıştır. 0,50 eşiği en yüksek F1 değerini (0,4572) vermiştir. 0,55 eşiğinin F1 değeri (0,4567) buna çok yakındır; ancak daha az müşteriyi işaretlemektedir.

Bu nedenle 0,55 eşiği, müşteri iletişim kapasitesi sınırlı olabilecek bir operasyon için **geçici operasyonel aday** olarak değerlendirilmiştir. 0,50 eşiği ise daha yüksek recall hedeflenen alternatif olarak korunmuştur. Bu karar, gerçek iletişim kapasitesi ve yanlış pozitif/yanlış negatif maliyetleri bilinmeden kesin optimum olarak yorumlanmamalıdır.

0,55 eşikte test setindeki 160 işaretli müşterinin 61'i gerçekten churn olmuş, 99'u churn olmamıştır. Ayrıca 71 churn müşterisi kaçırılmıştır. Bu sonuçlar modelin tek başına otomatik kampanya veya müşteri kararı vermesi için yeterli olmadığını göstermektedir.

## 5.7. Değişken önemleri

Değişken önemleri iki yöntemle incelenmiştir. Yorumlamada test seti üzerindeki permutation importance değerleri önceliklendirilmiştir.

| Sıra | Değişken | Permutation importance |
|---:|---|---:|
| 1 | recency | 0,0844 |
| 2 | ortalama_model_yili | 0,0402 |
| 3 | musteri_tipi | 0,0180 |
| 4 | frequency | 0,0065 |
| 5 | monetary | 0,0062 |

Recency en yüksek katkıyı sağlamıştır. Son servisinden itibaren geçen sürenin artması, churn tahmininde güçlü bir sinyal olarak öne çıkmaktadır. Ortalama araç model yılı ve müşteri tipi de katkı sağlamıştır.

Diğer değişkenlerin permutation importance değerleri görece düşüktür. Sıfıra yakın veya negatif değerler, test setinde ölçülebilir katkı görülmediğini gösterebilir; bu değişkenlerin churn üzerinde olumsuz veya nedensel etkisi olduğu anlamına gelmez.

Değişken önemleri modelin tahmin davranışını açıklar; nedensellik göstermez.

## 5.8. Sınırlamalar ve iş açısından değerlendirme

1. **Zaman bazlı doğrulama:** Tek hedef dönemi bulunduğundan farklı dönemlerde model kararlılığı test edilememiştir.
2. **Sınıf dengesizliği:** Churn oranı %16,56'dır. Accuracy tek başına yeterli değerlendirme ölçütü değildir.
3. **Yanlış pozitifler:** 0,55 eşikte 99 churn olmayan müşteri riskli işaretlenmiştir. Bu müşterilere yapılacak iletişimin maliyeti ve müşteri deneyimine etkisi değerlendirilmelidir.
4. **Yanlış negatifler:** Aynı eşikte 71 churn müşterisi kaçırılmıştır. Yüksek riskli müşterilerin kaçırılmasının iş maliyeti ayrıca ele alınmalıdır.
5. **Genellenebilirlik:** Test ROC-AUC değeri 0,7144'tür. Modelin ayırt etme gücü sınırlıdır ve üretim ortamına alınmadan önce yeni dönem verileriyle doğrulanmalıdır.
6. **Nedensellik:** Model ilişkileri ve tahmin sinyallerini öğrenir; belirli bir müşteri davranışının churn'e neden olduğunu kanıtlamaz.

İş açısından model, müşteri temsilcilerine inceleme önceliği oluşturabilecek bir karar destek aracı olarak değerlendirilebilir. Müşteriye yönelik aksiyonlar, model tahmininin yanı sıra müşteri geçmişi ve geri bildirimleri insan kontrolüyle incelenerek belirlenmelidir.

Üretim öncesinde zaman bazlı ileri dönem testi, eşik ve maliyet analizi, model izleme ve periyodik performans kontrolü yapılması önerilir.