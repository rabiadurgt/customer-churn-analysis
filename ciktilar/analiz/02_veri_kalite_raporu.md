
# Veri Kalite ve Temizlik Raporu

## 1. Veri Setlerinin Genel Durumu

| Veri Seti | Ham Satır | Temiz Satır | Kaldırılan Satır |
|---|---:|---:|---:|
| Müşteriler | 4235 | 4200 | 35 |
| Araçlar | 4786 | 4786 | 0 |
| Servis Kayıtları | 32770 | 32460 | 310 |
| Geri Bildirimler | 640 | 640 | 0 |
| **Toplam** | **42431** | **42086** | **345** |

Toplam 42.431 ham satırdan, önceki incelemelerde aynı içerikte
olduğu doğrulanan 345 tekrar satırı kaldırılmıştır. Böylece
42.086 satırdan oluşan temiz veri setleri elde edilmiştir.

## 2. Uygulanan Temizlik İşlemleri

- Müşteri tablosunda 35 mükerrer müşteri ID grubu incelenmiştir.
  Aynı müşteri ID'sine sahip kayıtların birebir aynı olduğu
  doğrulanmış ve tekrar eden 35 satır kaldırılmıştır.
- Servis kayıtları tablosunda 310 mükerrer kayit_id grubu
  incelenmiştir. Aynı kayıt ID'sine sahip kayıtların içeriklerinin
  aynı olduğu doğrulanmış ve tekrar eden 310 satır kaldırılmıştır.
- Araç ve geri bildirim tablolarında tam satır bazında mükerrer
  kayıt tespit edilmemiştir.
- Ham veri setleri değiştirilmemiş, temizlik işlemleri türetilmiş
  veri setleri üzerinde gerçekleştirilmiştir.
- Tekilleştirme işlemleri dışında eksik veya şüpheli değerler
  üzerinde otomatik düzeltme yapılmamıştır.

## 3. Eksik ve Şüpheli Değerlerin İncelenmesi

### 3.1. Müşteri Doğum Yılı

- Doğum yılı alanında 97 eksik değer tespit edilmiştir.
- 1890 doğum yılına sahip 9 kayıt incelenmiştir. Bu değerler
  olağan dışı bulunmuş ancak kaynak doğrulaması yapılamadığı
  için değiştirilmemiştir.
- 2021 doğum yılına sahip 9 kayıt bulunmaktadır. Bunların
  6'sı bireysel, 3'ü kurumsal müşterilere aittir.
- Veri sözlüğünde doğum yılı alanının kurumsal müşterilerde
  anlamsız olabileceği belirtilmiştir.
- Eksik ve şüpheli değerler doldurulmamış veya silinmemiştir.

### 3.2. Kilometre Alanları

Araçların ilk kilometre değerleri:
- 4.786 araç kaydında ilk kilometre alanı eksik değildir.
- 565 araçta ilk kilometre değeri 0'dır.
- Negatif ilk kilometre değeri tespit edilmemiştir.
- Sıfır kilometre değerleri, doğrulanmış hata olarak
  değerlendirilemediği için korunmuştur.

Servis kayıtlarındaki kilometre değerleri:
- Servis kayıtlarında 40 benzersiz negatif kilometre kaydı
  tespit edilmiştir.
- Negatif olmayan kilometre değerleri arasındaki kronolojik
  düşüşler ayrıca incelenmiştir. Bu kapsamda 310 kayıt,
  301 farklı araçla ilişkilendirilmiştir.
- Negatif kilometre kayıtları ile kronolojik kilometre
  düşüşleri ayrı veri kalitesi bulguları olarak ele alınmıştır.
- Şüpheli kilometre değerleri silinmemiş veya değiştirilmemiştir.

Araç ilk kilometresi ve ilk servis kilometresi karşılaştırması:
- Servis geçmişi bulunan 4.759 araç incelenmiştir.
- 4.748 araçta ilk servis kilometresi, araç tablosundaki
  ilk kilometre değerinden yüksektir.
- 11 araçta ilk servis kilometresi daha düşüktür.
- Bu 11 kaydın 10'unda negatif servis kilometresi,
  1'inde ise 3 kilometrelik fark tespit edilmiştir.
- İlgili kayıtlar otomatik olarak düzeltilmemiştir.

### 3.3. Tutar Alanları

- Tutar alanındaki negatif, sıfır ve yüksek değerler
  veri kalitesi değerlendirmesi kapsamında ele alınmıştır.
- Negatif değerler iade, düzeltme veya farklı bir iş
  süreciyle ilişkili olabileceğinden otomatik olarak silinmemiştir.
- Yüksek tutarlı değerler, yalnızca büyüklüklerine dayanılarak
  hatalı kabul edilmemiştir.
- Tutar değerlerinde otomatik silme veya düzeltme yapılmamıştır.

### 3.4. Memnuniyet Puanı

- Memnuniyet puanı alanındaki eksik değerler doldurulmamıştır.
- Eksik değerler, müşteri veya servis davranışına ilişkin
  bir puan olarak yorumlanmamıştır.
- Mevcut değerler korunmuştur.

## 4. Tarih Kontrolleri

### 4.1. Müşteri İlk Temas Tarihi

- 4.235 müşteri kaydının tamamında ilk temas tarihi bulunmaktadır.
- Tarih dönüşümü sırasında eksik veya dönüştürülemeyen
  değer tespit edilmemiştir.
- İlk temas tarihleri 25.12.2015 ile 01.01.2022
  tarihleri arasındadır.

### 4.2. İlk Temas ve Araç Satın Alma Tarihi

Müşteri ve araç tabloları, mükerrer müşteri ID'lerinin
birleştirme sonuçlarını etkilemesini önlemek amacıyla
tekilleştirilmiş müşteri tablosu kullanılarak birleştirilmiştir.

4.786 araç kaydı üzerinden yapılan karşılaştırmada:

- İlk temas tarihi satın alma tarihinden sonra olan kayıt: 2.236
- İlk temas ve satın alma tarihi aynı gün olan kayıt: 1
- İlk temas tarihi satın alma tarihinden önce olan kayıt: 2.549
- Her iki tarih alanında da eksik veya dönüştürülemeyen
  değer bulunmamıştır.

İlk temas tarihinin satın alma tarihinden sonra olması,
alanın iş anlamı kesin olarak doğrulanmadan veri hatası
olarak değerlendirilmemiştir.

Bu 2.236 kayıtta tarih farkları ayrıca incelenmiştir:
- Ortalama fark: 1.193,28 gün
- Medyan fark: 1.081 gün
- En uzun fark: 3.220 gün

Bu farklılıklar raporlanmış, ancak herhangi bir kayıt
otomatik olarak silinmemiş veya değiştirilmemiştir.

### 4.3. Satın Alma ve Garanti Bitiş Tarihi

4.786 araç kaydında yapılan kontroller sonucunda:

- Eksik satın alma tarihi: 0
- Eksik garanti bitiş tarihi: 0
- Garanti bitiş tarihi satın alma tarihinden önce olan kayıt: 0
- İki tarihin aynı olduğu kayıt: 0

İncelenen kayıtlarda kronolojik tutarsızlık tespit edilmemiştir.

### 4.4. Araç Model Yılı ve Satın Alma Tarihi

- Araç model yılı ile satın alma tarihinin yılı karşılaştırılmıştır.
- 4.786 aracın tamamında model yılı ile satın alma yılının
  eşleştiği görülmüştür.
- Bu sonuç, veri seti içindeki tarihsel tutarlılığı
  göstermektedir; bağımsız kaynak doğrulaması değildir.

## 5. Temizlik Sonrası Doğrulama

Temiz veri setleri oluşturulduktan sonra aşağıdaki kontroller
gerçekleştirilmiştir:

- Müşteri tablosunda mükerrer müşteri ID bulunmadığı
  doğrulanmıştır.
- Araç tablosunda tam satır bazında mükerrer kayıt
  bulunmadığı doğrulanmıştır.
- Servis kayıtlarında mükerrer kayit_id bulunmadığı
  doğrulanmıştır.
- Geri bildirim tablosunda tam satır bazında mükerrer
  kayıt bulunmadığı doğrulanmıştır.
- Ham veri setlerinin satır sayılarının değişmediği
  kontrol edilmiştir.
- Temiz veri setlerinin sütun isimleri ve sıralarının
  ham veri setleriyle aynı olduğu doğrulanmıştır.
- Temiz veri setleri CSV formatında kaydedilmiştir.
- Kaydedilen CSV dosyaları yeniden okunmuş; satır sayıları
  ve sütun yapıları bellek üzerindeki temiz veri setleriyle
  karşılaştırılmıştır.
- Dört veri setinin de satır sayısı ve sütun kontrolleri
  başarılı olmuştur.

## 6. Genel Değerlendirme

Veri kalitesi ve temizlik çalışması kapsamında öncelikle
mükerrer kayıtlar, eksik değerler, şüpheli kilometre
değerleri ve tarihsel tutarlılıklar incelenmiştir.

Yalnızca aynı içerikte olduğu doğrulanan mükerrer müşteri
ve servis kayıtları temiz veri setlerinden çıkarılmıştır.

Eksik değerler doldurulmamış; şüpheli kilometre, doğum yılı
ve diğer alanlardaki değerler doğrulanmadan değiştirilmemiştir.

Ham veri setleri korunmuş, temizlik işlemleri türetilmiş
veri setleri üzerinde gerçekleştirilmiştir.

Sonuç olarak 42.086 satırdan oluşan dört temiz veri seti
oluşturulmuş, CSV formatında kaydedilmiş ve yeniden okunarak
temel yapısal kontrolleri doğrulanmıştır.

Bu yaklaşım, veri kaybını önlemek ve sonraki analizlerde
veri kalitesine ilişkin belirsizlikleri görünür tutmak
amacıyla benimsenmiştir.
