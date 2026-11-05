# Veri Keşfi Raporu

## 1. Veri setlerinin genel görünümü

| Veri seti | Satır sayısı | Kolon sayısı |
|---|---:|---:|
| Müşteriler | 4.235 | 8 |
| Araçlar | 4.786 | 8 |
| Servis Kayıtları | 32.770 | 8 |
| Geri Bildirimler | 640 | 5 |

Veri setleri, veri sözlüğünde tanımlanan kolonlarla karşılaştırılmıştır.
Dört veri setinde de beklenen kolonların tamamı mevcuttur.
Tarih dönüşümleri için oluşturulan yardımcı kolonlar ham veri sözlüğünün
dışındadır ve orijinal tarih kolonları korunmuştur.

## 2. Eksik değerler ve tam tekrarlar

| Veri seti | Eksik hücre | Tam tekrar satırı |
|---|---:|---:|
| Müşteriler | 1.628 | 35 |
| Araçlar | 0 | 0 |
| Servis Kayıtları | 6.875 | 310 |
| Geri Bildirimler | 11 | 0 |

Müşteri tablosundaki eksiklikler ağırlıklı olarak son iletişim notu
(1.531; %36,15) ve doğum yılı (97; %2,29) alanlarındadır.

Servis kayıtlarında 6.875 memnuniyet puanı eksiktir (%20,98).
Bu alanın doldurulmamış olması doğrudan memnuniyetsizlik anlamına gelmez.

Müşteri tablosunda 35, servis kayıtları tablosunda 310 tam tekrar satırı
tespit edilmiştir. Tekrar eden ID gruplarının tüm kolonları aynı olduğundan
bunlar tam tekrar olarak değerlendirilmiştir. Ham veriler bu aşamada
değiştirilmemiştir.

Geri bildirim tablosunda 11 metin alanı eksiktir.

## 3. Tablo ilişkileri

- Müşteriler ve araçlar, musteri_id üzerinden ilişkilidir.
- Araçlar ve servis kayıtları, arac_id üzerinden ilişkilidir.
- Araçlar ve geri bildirimler, arac_id üzerinden ilişkilidir.

Kontrollerde eşleşmeyen müşteri, araç veya geri bildirim-servis araç
ID'si tespit edilmemiştir.

4.200 benzersiz müşteri ve 4.786 araç bulunmaktadır.
3.614 müşterinin bir, 586 müşterinin iki aracı vardır.
Bu nedenle müşteri-araç ilişkisi bire-çok yapıdadır.

Birleştirme işlemlerinde satır çoğalmasını önlemek için tabloların
önce uygun analiz seviyesinde özetlenmesi gerekmektedir.

## 4. Tarih alanlarının incelenmesi

Tarih alanları datetime türüne dönüştürülmüş ve dönüşüm hatası
tespit edilmemiştir. Orijinal metin kolonları korunmuştur.

Servis tarihi dört farklı biçimde bulunmaktadır:

| Tarih biçimi | Kayıt sayısı |
|---|---:|
| YYYY-MM-DD | 22.946 |
| YYYY-MM-DD HH:MM:SS | 1.858 |
| DD/MM/YYYY | 3.987 |
| DD.MM.YYYY | 3.979 |

## 5. Analiz dönemleri ve popülasyon

- Gözlem dönemi: 01.01.2022 – 30.06.2025
- Hedef dönemi: 01.07.2025 – 30.06.2026
- Gözlem dönemindeki servis kaydı: 25.309
- Gözlem döneminde servis alan benzersiz araç: 4.508
- Analiz popülasyonundaki benzersiz müşteri: 3.974

Toplam 4.200 müşterinin 226'si gözlem döneminde servis kaydına
sahip olmadığı için analiz popülasyonuna dahil edilmemiştir.

Hedef dönemindeki servis kayıtları churn etiketinin oluşturulmasında
kullanılacaktır. Hedef döneminden sonraki kayıtlar hedef tanımına
dahil edilmeyecektir.

## 6. İlk aşama metodolojik notlar

- Ham veriler değiştirilmemiştir.
- Tam tekrar kayıtları henüz silinmemiştir.
- Eksik memnuniyet puanları otomatik olarak doldurulmamıştır.
- Negatif servis tutarları henüz silinmemiş veya değiştirilmemiştir.
- Aynı gün gerçekleşen farklı servis kayıtları, ek kanıt olmadan
  tekrar kayıt olarak kabul edilmemiştir.
- Müşteri seviyesinde analiz için servis ve geri bildirim tabloları
  uygun seviyede özetlenmelidir.

Bu rapor, ham veri keşfi aşamasında doğrulanan bulguları içerir.
Temizlik kararları ve müşteri seviyesinde özellik üretimi sonraki
aşamalarda ele alınacaktır.