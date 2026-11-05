# 03 - Müşteri Analiz Tablosu ve Churn Hedefinin Oluşturulması

## 1. Analiz kapsamı ve zaman pencereleri

Analiz, müşterilerin geçmiş servis davranışlarından yararlanarak sonraki dönemde yetkili servise gelmeme durumunu incelemek amacıyla gerçekleştirilmiştir.

- **Gözlem dönemi:** 01.01.2022 – 30.06.2025
- **Kesim tarihi:** 30.06.2025
- **Hedef dönemi:** 01.07.2025 – 30.06.2026

Analiz popülasyonu, kesim tarihine kadar en az bir servis kaydı bulunan müşterilerden oluşturulmuştur. Bu kapsamda 3.974 müşteri analize dahil edilmiştir.

Gözlem döneminde 25.061 servis kaydı ve 4.508 servis geçmişi bulunan araç tespit edilmiştir.

## 2. Müşteri seviyesinde analiz tablosu

Müşteri, araç, servis ve geri bildirim veri kaynakları müşteri seviyesinde birleştirilmiştir. Birleştirme öncesinde araç, servis ve geri bildirim kayıtları müşteri bazında özetlenmiştir. Böylece bir müşteriye ait birden fazla servis veya geri bildirim kaydının ana tabloda satır çoğalmasına neden olması önlenmiştir.

Oluşturulan analiz tablosu 3.974 satır ve 18 sütundan oluşmaktadır. Her müşteri için tek bir satır bulunmaktadır.

### 2.1. RFM metrikleri

Müşteri davranışlarını ölçmek amacıyla aşağıdaki metrikler hesaplanmıştır:

- **Recency:** Kesim tarihinden müşterinin son servis tarihine kadar geçen gün sayısı.
- **Frequency:** Gözlem dönemindeki toplam servis kaydı sayısı.
- **Monetary:** Gözlem dönemindeki toplam servis tutarı.

| Metrik | Ortalama | Medyan | Minimum | Maksimum |
|---|---:|---:|---:|---:|
| Recency (gün) | 116,83 | 102 | 0 | 413 |
| Frequency | 6,31 | 6 | 1 | 24 |
| Monetary (TL) | 82.701,02 | 67.419,98 | -15.893,88 | 2.941.493,53 |

Monetary değişkeninde negatif ve sıfır tutarlı servis kayıtları bulunmaktadır. Negatif tutarların düzeltme veya iade, sıfır tutarların ise ücretsiz ya da garanti kapsamındaki işlemlerle ilişkili olabileceği değerlendirilmiştir. İşlem niteliğini kesin olarak doğrulayacak ek bilgi bulunmadığından bu kayıtlar otomatik olarak silinmemiş veya sıfırlanmamıştır.

Monetary dağılımındaki yüksek maksimum değer ve ortalama-medyan farkı, değişkenin sağa çarpık olduğunu göstermektedir.

### 2.2. Araç ve geri bildirim özetleri

Kesim tarihine kadar satın alınmış araçlar üzerinden müşteri başına araç sayısı, ortalama model yılı ve aktif garantili araç sayısı hesaplanmıştır.

- Analiz kapsamındaki araç sayısı: 4.528
- Tek aracı bulunan müşteri: 3.420
- İki aracı bulunan müşteri: 554

Geri bildirim kayıtları da müşteri bazında özetlenmiştir. Toplam 640 geri bildirim kaydının 586 farklı müşteriye ait olduğu, bu müşterilerin 562'sinin analiz popülasyonunda yer aldığı belirlenmiştir. Analiz popülasyonu dışındaki 24 müşteriye ait 26 geri bildirim kaydı ana tabloya dahil edilmemiştir.

Analiz popülasyonundaki 3.974 müşterinin 562'sinde geri bildirim kaydı bulunmaktadır.

### 2.3. Eksik değer değerlendirmesi

Ana analiz tablosunda toplam 6.824 eksik hücre bulunmaktadır. Eksikliklerin tamamı aşağıdaki iki sütundan kaynaklanmaktadır:

| Sütun | Eksik kayıt | Oran |
|---|---:|---:|
| İlk geri bildirim tarihi | 3.412 | %85,86 |
| Son geri bildirim tarihi | 3.412 | %85,86 |

Bu eksiklikler, ilgili müşterilerin gözlem döneminde geri bildirim kaydının bulunmamasından kaynaklanmaktadır. Dolayısıyla eksik tarihler yapay değerlerle doldurulmamış, mevcut durumları korunmuştur. Geri bildirim sayısı gibi sayısal özetlerde geri bildirim kaydı olmayan müşteriler 0 olarak temsil edilmiştir.

## 3. Churn hedef değişkeninin oluşturulması

Churn hedefi, müşterinin 01.07.2025–30.06.2026 tarihleri arasında yetkili servis ağına en az bir kez gelip gelmemesine göre tanımlanmıştır.

- **Churn = 0:** Hedef döneminde en az bir servis kaydı bulunan müşteri.
- **Churn = 1:** Hedef döneminde hiç servis kaydı bulunmayan müşteri.

Hedef dönemindeki 7.399 servis kaydının tamamı araçlar üzerinden müşteri kimliğiyle eşleştirilmiştir. Eşleşmeyen kayıt bulunmamaktadır.

### 3.1. Churn dağılımı

| Hedef sınıfı | Müşteri sayısı | Oran |
|---|---:|---:|
| Churn yok (0) | 3.316 | %83,44 |
| Churn var (1) | 658 | %16,56 |
| **Toplam** | **3.974** | **%100** |

Analiz popülasyonunun %16,56'sı hedef döneminde yetkili servise gelmemiştir. Bu dağılım, modelleme aşamasında sınıf dengesizliğinin dikkate alınmasını gerektirmektedir.

Hedef dönemindeki servis sayısı, churn etiketinin oluşturulması ve doğrulanması amacıyla tabloda tutulmuştur. Modelin özellikleri arasında kullanılmayacaktır.

## 4. Müşteri memnuniyeti ve servis merkezi karşılaştırması

Gözlem dönemindeki 25.061 servis kaydının 19.810'unda memnuniyet puanı bulunmaktadır. 5.251 kayıtta (%20,95) memnuniyet puanı eksiktir. Eksik puanlar doldurulmamış; karşılaştırmalar yalnızca puan bulunan kayıtlar üzerinden yapılmıştır.

Genel memnuniyet ortalaması 3,95, medyanı 4'tür.

Servis merkezleri arasında memnuniyet puanı dağılımlarını karşılaştırmak için Kruskal-Wallis testi uygulanmıştır. Test sonucunda istatistiksel olarak anlamlı farklılık görülmüştür (H=20,71; p=0,0021).

Bonferroni düzeltmeli ikili Mann-Whitney U karşılaştırmalarında yalnızca SM-Ankara1 ile SM-Maslak arasındaki fark anlamlı bulunmuştur (düzeltilmiş p=0,0198). Bununla birlikte etki büyüklüğü düşüktür (rank-biserial etki büyüklüğü yaklaşık 0,0418).

Sonuçlar servis kaydı seviyesinde hesaplanmıştır. Aynı müşteriye ait tekrarlı kayıtlar nedeniyle gözlemlerin bağımsızlığı sınırlı olabilir. Bu nedenle istatistiksel anlamlılık, tek başına iş açısından büyük bir fark veya nedensel ilişki olarak yorumlanmamalıdır.

## 5. Veri sızıntısını önleme ve modelleme kararları

Modelleme için yalnızca 30.06.2025 tarihine kadar elde edilebilecek bilgiler kullanılacaktır.

- Hedef dönemindeki servis kayıtları yalnızca churn etiketini oluşturmak için kullanılmıştır.
- Hedef dönemindeki servis sayısı model özelliklerinden çıkarılacaktır.
- CRM müşteri durumu ve son iletişim notu, kesim tarihi itibarıyla güvenilir biçimde doğrulanamadığından ana özellik setine dahil edilmemiştir.
- Geri bildirim kayıtları yalnızca gözlem dönemi içerisinde kalan veriler üzerinden değerlendirilecektir.
- Memnuniyet puanı eksiklikleri otomatik olarak doldurulmayacaktır.

## 6. Çıktılar

Bu aşamanın temel çıktısı `ciktilar/analiz/musteri_analiz_tablosu.csv` dosyasıdır.

Dosya, 3.974 müşteriyi temsil eden 18 sütunlu müşteri seviyesinde analiz tablosunu ve churn hedef değişkenini içermektedir.

Bu tablo, sonraki aşamada gerçekleştirilecek keşifsel veri analizi ve churn modellemesinin temel veri kaynağı olarak kullanılacaktır.
