# Yapay Zeka Kullanım Günlüğü

## 1. Kullanılan araçlar ve amaçları

- **ChatGPT:** Analiz sürecinin planlanması, Python kodlarının gözden geçirilmesi, hata ayıklama, modelleme yaklaşımının değerlendirilmesi ve bulguların raporlaştırılması aşamalarında kullanıldı.
- **Claude:** Alternatif çözüm yaklaşımlarını değerlendirmek, teknik kararları farklı bir bakış açısıyla gözden geçirmek ve analiz sonuçlarının tutarlılığını kontrol etmek amacıyla kullanıldı.
- **Gemini 3.5 Flash Lite API:** 200 müşteri geri bildiriminin duygu analizi ve çok etiketli konu sınıflandırması için kullanıldı. Model çıktıları yapılandırılmış JSON formatında alındı ve CSV dosyasına aktarıldı.

## 2. En işe yarayan iki istem

**Churn modelinin değerlendirilmesi ve iyileştirilmesi**

> Müşteri terk tahmini probleminde aşağıdaki veri yapısını, hedef değişkeni ve model sonuçlarını incele. Özelliklerin hedef değişken açısından veri sızıntısı oluşturup oluşturmadığını değerlendir. Sınıf dengesizliğini dikkate alarak DummyClassifier gibi bir başlangıç modeliyle karşılaştırma yap. Accuracy değerini tek başına yeterli kabul etme; özellikle terk sınıfı için precision, recall, F1-score, ROC-AUC ve PR-AUC metriklerini birlikte yorumla. Farklı karar eşiklerinin yanlış pozitif ve yanlış negatif sayıları üzerindeki etkisini değerlendir. Sonuçları iş açısından yorumla ve modelin üretimde kullanılabilmesi için gerekli ek kontrolleri belirt. Yalnızca paylaşılan veri ve sonuçlara dayan; hesaplanmamış metrik veya bulgu üretme.

**İstem 2 – Altın veri seti sonuçlarına göre sınıflandırma kurallarının iyileştirilmesi**

> Sınıflandırma sırasında aşağıdaki kategori ayrımlarına dikkat et: Personel kategorisi yalnızca çalışan davranışlarını değil, iletişim kuramama, telefonla ulaşamama ve bilgilendirme eksikliğini de kapsar. Yedek araç sağlanması, yedek_parça kategorisine dahil edilmemeli; başka uygun bir konu yoksa diğer olarak etiketlenmelidir. Süre kategorisi yalnızca açıkça belirtilen bekleme, gecikme veya işlem süresi durumlarında kullanılmalıdır. Tekrar servise gitmek zorunda kalmak tek başına süre etiketi için yeterli değildir. Bir geri bildirim birden fazla konuyu içeriyorsa desteklenen tüm kategorileri seç. Diğer kategorisini yalnızca diğer kategorilerden hiçbiri uygun olmadığında kullan. Metinde bulunmayan bilgileri varsayma ve çıktı formatını değiştirme.

## 3. Yapay zekanın yanıldığı örnek

Gemini, **“Yedek araç verilmesi büyük kolaylık oldu.”** geri bildirimini `yedek_parça` olarak sınıflandırdı. Ancak yedek araç, yedek parça kategorisine girmez; bu ifade `diğer` olarak etiketlenmelidir.

Bu hatayı, rastgele seçilen 40 geri bildirimin manuel olarak etiketlendiği altın veri setiyle karşılaştırma yaparken fark ettim. Hata türünü belirledikten sonra istemde yedek araç ve yedek parça ayrımını açıkça tanımladım. Aynı 25 örnek üzerindeki karşılaştırmada konu sınıflandırmasının mikro F1 skoru 0,8750'ten 0,9512'ye, tam eşleşme oranı ise %72'den %84'e yükseldi. Ancak ikinci karşılaştırmanın yalnızca 25 ortak örneğe dayandığı dikkate alınmalıdır.

## 4. Bilinçli olarak yapay zekâya devretmediğim işler

Veri setindeki aykırı değerlerin nasıl ele alınacağı, analiz popülasyonunun belirlenmesi ve özelliklerin tahmin tarihine uygunluğunun kontrolü gibi kritik veri hazırlama kararlarını kendim verdim. Özellikle hedef dönemine ait bilgilerin modele sızmasını önlemek için özellikleri tek tek değerlendirdim. Ayrıca 40 geri bildirimi manuel olarak etiketleyerek LLM çıktıları için bağımsız bir altın veri seti oluşturdum ve modelin sınıflandırma hatalarını bu veri seti üzerinden denetledim. Nihai model seçimi, performans metriklerinin iş açısından yorumlanması ve aksiyon önerilerinin oluşturulması aşamalarında da yapay zekâ çıktısını doğrudan kabul etmek yerine verilerle doğruladım. Bu işleri kendim yürütmemin nedeni, analitik kararların izlenebilirliğini sağlamak ve doğrulanmamış yapay zekâ çıktılarının iş kararlarını yönlendirmesini önlemekti.