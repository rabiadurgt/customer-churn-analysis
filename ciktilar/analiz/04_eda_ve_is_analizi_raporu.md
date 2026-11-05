# 04 - Keşifsel Veri Analizi ve İş Bulguları

## 1. Analiz kapsamı

Keşifsel veri analizi, 30.06.2025 kesim tarihine kadar oluşturulan 3.974 müşterilik analiz popülasyonu üzerinden gerçekleştirilmiştir. Churn hedefi, 01.07.2025–30.06.2026 dönemindeki servis davranışına göre tanımlanmıştır.

Bu bölümde müşteri davranışları, kategorik değişkenler, memnuniyet verileri ve churn ilişkileri incelenmiştir. Bulgular betimsel niteliktedir; tek başına nedensellik göstermez.

## 2. Churn gruplarına göre RFM karşılaştırması

| Metrik | Churn olmayan ortalama | Churn olan ortalama | Churn olmayan medyan | Churn olan medyan |
|---|---:|---:|---:|---:|
| Recency (gün) | 104,31 | 179,95 | 93 | 174 |
| Frequency | 6,45 | 5,57 | 6 | 5 |
| Monetary (TL) | 84.645,07 | 72.903,99 | 68.645,78 | 59.950,58 |

Churn olan müşterilerin son servislerinden itibaren geçen süre daha uzundur. Bu grupta gözlem dönemindeki servis sayısı ve toplam servis tutarı da daha düşüktür.

Bu farklılıklar, geçmiş servis davranışlarının churn tahmininde değerlendirilebilecek aday değişkenler olduğunu göstermektedir. Ancak bunların model performansına katkısı sonraki modelleme aşamasında doğrulanmalıdır.

Monetary değişkeninde uç değerler ve negatif tutarlar bulunduğundan ortalama değerler dikkatli yorumlanmalıdır. Medyan değerler merkezi eğilimi değerlendirmek için tamamlayıcı olarak sunulmuştur.

## 3. Kategorik değişkenlere göre churn oranları

### 3.1. Müşteri tipi

| Müşteri tipi | Müşteri sayısı | Churn sayısı | Churn oranı |
|---|---:|---:|---:|
| Bireysel | 3.270 | 597 | %18,26 |
| Kurumsal | 704 | 61 | %8,66 |

Bireysel müşterilerde gözlenen churn oranı kurumsal müşterilerden yüksektir. Bu fark müşteri segmentlerinin ayrı incelenmesini ve modelleme aşamasında doğrulanmasını gerektirebilir.

### 3.2. Kayıt kanalı

| Kayıt kanalı | Müşteri sayısı | Churn sayısı | Churn oranı |
|---|---:|---:|---:|
| Web | 850 | 163 | %19,18 |
| Mobil Uygulama | 374 | 71 | %18,98 |
| Çağrı Merkezi | 520 | 89 | %17,12 |
| Bayi | 2.230 | 335 | %15,02 |

Web ve mobil uygulama kanallarından kayıt olan müşterilerde daha yüksek churn oranları gözlenmiştir. Bununla birlikte kanalın tek başına churn davranışını açıkladığı sonucuna varılamaz.

### 3.3. Araç sayısı

| Araç sayısı | Müşteri sayısı | Churn sayısı | Churn oranı |
|---|---:|---:|---:|
| 1 | 3.420 | 609 | %17,81 |
| 2 | 554 | 49 | %8,84 |

İki aracı bulunan müşterilerin churn oranı daha düşüktür. Araç sayısı, müşteri bağlılığıyla ilişkili olabilecek bir segmentasyon değişkeni olarak değerlendirilebilir.

### 3.4. Aktif garantili araç sayısı

| Aktif garantili araç sayısı | Müşteri sayısı | Churn sayısı | Churn oranı |
|---|---:|---:|---:|
| 0 | 2.955 | 552 | %18,68 |
| 1 | 992 | 104 | %10,48 |
| 2 | 27 | 2 | %7,41 |

Aktif garantili aracı bulunan müşterilerde daha düşük churn oranları gözlenmiştir. Ancak iki aktif garantili aracı bulunan grubun yalnızca 27 müşteriden oluşması nedeniyle bu grubun oranı ihtiyatlı değerlendirilmelidir.

### 3.5. Şehre göre churn

Şehir adları yazım farklılıkları giderilerek standartlaştırılmıştır. Standartlaştırma ham veri üzerinde değil, analiz tablosunda ayrı bir değişken oluşturularak gerçekleştirilmiştir.

| Şehir | Müşteri sayısı | Churn sayısı | Churn oranı |
|---|---:|---:|---:|
| Bursa | 312 | 61 | %19,55 |
| İstanbul | 1.685 | 285 | %16,91 |
| İzmir | 523 | 88 | %16,83 |
| Adana | 211 | 35 | %16,59 |
| Kocaeli | 254 | 39 | %15,35 |
| Antalya | 313 | 48 | %15,34 |
| Ankara | 676 | 102 | %15,09 |

Bursa'da en yüksek, Ankara'da en düşük churn oranı gözlenmiştir. Şehirler arasındaki müşteri sayısı farklılıkları dikkate alınmalı; bu oranlar doğrudan servis kalitesi veya şehir kaynaklı bir neden olarak yorumlanmamalıdır.

## 4. Müşteri memnuniyeti ve churn ilişkisi

Gözlem dönemindeki servis kayıtları müşteri bazında birleştirilerek her müşteri için ortalama memnuniyet puanı hesaplanmıştır.

| Churn grubu | Toplam müşteri | Puanlı müşteri | Ortalama memnuniyet | Medyan memnuniyet | Puan kapsama oranı |
|---|---:|---:|---:|---:|---:|
| Churn olmayan | 3.316 | 3.275 | 4,008 | 4,000 | %98,764 |
| Churn olan | 658 | 652 | 3,793 | 3,775 | %99,088 |

Churn olmayan müşterilerin ortalama memnuniyet puanı, churn olan müşterilerden 0,215 puan daha yüksektir.

Memnuniyet puanı bulunmayan 47 müşterinin tamamında gözlem döneminde servis kaydı bulunmasına rağmen hiçbir servis kaydında puan yer almamaktadır. Bu müşteriler sıfır puanla temsil edilmemiş; memnuniyet karşılaştırmasına dahil edilmemiştir. Her iki churn grubunda puan kapsama oranının birbirine yakın olması karşılaştırmayı destekleyen bir unsurdur.

### 4.1. İstatistiksel karşılaştırma

Müşteri bazında hesaplanan ortalama memnuniyet puanlarının dağılımlarını karşılaştırmak için Mann–Whitney U testi uygulanmıştır.

| Ölçüt | Sonuç |
|---|---:|
| Churn olmayan müşteri | 3.275 |
| Churn olan müşteri | 652 |
| U istatistiği | 1.347.243 |
| p-değeri | <0,001 |
| Rank-biserial etki büyüklüğü | 0,26188 |

Test sonucunda iki churn grubunun memnuniyet puanı dağılımları arasında istatistiksel olarak anlamlı fark bulunmuştur. Rank-biserial etki büyüklüğü, churn olmayan müşterilerin daha yüksek memnuniyet puanlarına sahip olma eğilimini göstermektedir.

Bu bulgu, düşük memnuniyet ile churn arasında ilişki olabileceğini düşündürmektedir. Ancak memnuniyetin churn'e neden olduğu sonucunu kanıtlamaz.

## 5. Veri sınırlılıkları ve yorumlama

- Memnuniyet puanları servis kayıtlarının %20,95'inde eksiktir. Eksik değerler sıfır olarak doldurulmamıştır.
- Müşteri bazındaki memnuniyet karşılaştırması yalnızca en az bir puanlı servis kaydı bulunan müşterileri kapsamaktadır.
- Servis merkezi karşılaştırmasında servis kaydı seviyesinde tekrarlı müşteri gözlemleri bulunmaktadır. Bu durum istatistiksel testlerde bağımsızlık varsayımını sınırlayabilir.
- Monetary değişkeninde negatif, sıfır ve uç değerler bulunmaktadır. İşlem türü doğrulanmadan otomatik düzeltme yapılmamıştır.
- Kategorik değişkenlere göre hesaplanan churn oranları betimseldir. Değişkenler arasındaki olası etkileşimler ve diğer faktörler kontrol edilmemiştir.
- İstatistiksel anlamlılık, iş açısından önemli büyüklükte fark veya nedensellik anlamına gelmez.

## 6. İş açısından değerlendirme

EDA sonuçları aşağıdaki alanların modelleme ve müşteri segmentasyonu aşamalarında değerlendirilmesini desteklemektedir:

1. **Servis davranışı:** Son servis tarihinden itibaren geçen süre ve servis sıklığı, churn gruplarını ayırt eden aday sinyallerdir.
2. **Müşteri segmentleri:** Müşteri tipi, araç sayısı, garanti durumu ve kayıt kanalı bazında farklı churn oranları gözlenmiştir.
3. **Memnuniyet:** Churn olan müşterilerde ortalama memnuniyet daha düşüktür. Geri bildirim verileri, riskli müşterilerin incelenmesinde tamamlayıcı bilgi sağlayabilir.

Bu bulgular tek başına kampanya veya müşteri aksiyonu belirlemek için yeterli değildir. Model sonuçları, geri bildirim içerikleri ve operasyonel koşullarla birlikte değerlendirilmelidir.

## 7. Kaydedilen analiz çıktıları

- `ciktilar/analiz/musteri_analiz_tablosu.csv`
- `ciktilar/analiz/servis_gozlem.csv`
- `ciktilar/analiz/musteri_memnuniyet_ozeti.csv`
- `ciktilar/analiz/musteri_analiz_memnuniyet.csv`
- `ciktilar/analiz/memnuniyet_churn_karsilastirmasi.csv`
- `ciktilar/analiz/memnuniyet_churn_istatistiksel_test.csv`
- `ciktilar/analiz/grafikler/eda_recency_churn.png`

Bu rapor, müşteri analiz tablosu ve churn hedefinin oluşturulmasına ilişkin 03 numaralı raporu tamamlayan keşifsel analiz dokümanıdır.
