# Borusan Otomotiv | Müşteri Churn Analizi

## Proje Hakkında

Bu proje, Borusan Otomotiv vaka çalışması kapsamında yetkili servis müşterilerinin müşteri kaybı (churn) riskini analiz etmek, churn tahmini için bir makine öğrenmesi modeli geliştirmek ve müşteri geri bildirimlerini LLM tabanlı yöntemlerle incelemek amacıyla hazırlanmıştır.

Çalışma üç ana bölümden oluşmaktadır:

1. **Veri Analizi:** Veri setlerinin incelenmesi, temizlenmesi, müşteri bazlı analiz tablosunun oluşturulması ve keşifsel veri analizi (EDA).
2. **Churn Tahmini:** Müşteri davranışlarından elde edilen özelliklerle churn tahmin modeli geliştirilmesi ve model performansının değerlendirilmesi.
3. **Geri Bildirim Analizi:** Müşteri yorumlarının LLM kullanılarak duygu ve konu bazında sınıflandırılması, sonuçların manuel etiketlenmiş bir altın set üzerinden değerlendirilmesi.

## Proje Klasör Yapısı

```text
borusan_automotive_case_study/
│
├── veri/                       # Orijinal veri dosyaları
│
├── notebooks/                  # Analiz ve modelleme notebook'ları
│   ├── 01_veri_kesfi.ipynb
│   ├── 02_veri_temizligi.ipynb
│   ├── 03_musteri_analiz_tablosu.ipynb
│   ├── 04_eda_ve_is_analizi.ipynb
│   ├── 05_churn_modelleme.ipynb
│   └── 06_llm_metin_analizi.ipynb
│
├── ciktilar/
│   ├── analiz/                  # Analiz tabloları ve model çıktıları
│   └── temiz_veriler/           # Temizlenmiş veri setleri
│
├── rapor/
│   ├── RAPOR.md                 # Vaka çalışması raporu
│   └── AI_KULLANIM_GUNLUGU.md  # Yapay zekâ kullanım günlüğü
│
├── .env                         # API anahtarı gibi ortam değişkenleri
├── .gitignore
├── requirements.txt             # Python bağımlılıkları
└── README.md
```

## Kullanılan Teknolojiler ve Kütüphaneler

- **Programlama dili:** Python
- **Geliştirme ortamı:** Visual Studio Code (VS Code)
- **Kod ve analiz ortamı:** Jupyter Notebook (`.ipynb`)
- **Veri analizi:** Pandas, NumPy
- **Veri görselleştirme:** Matplotlib, Seaborn
- **Makine öğrenmesi:** Scikit-learn (Random Forest, Gradient Boosting)
- **İstatistiksel analiz:** SciPy
- **Model kaydetme ve yükleme:** Joblib
- **Notebook ortamı:** Jupyter, IPykernel
- **Dosya ve ortam yönetimi:** OpenPyXL, Python-dotenv
- **Test:** Pytest
- **LLM tabanlı metin analizi:** Google Gemini API (`google-genai`)

## Çalıştırma ve Kurulum

### 1. Projeyi açın

Proje klasörüne terminal üzerinden geçin:

```powershell
cd borusan_automotive_case_study
```

### 2. Sanal ortam oluşturun ve etkinleştirin

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Gerekli kütüphaneleri yükleyin

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. API ayarlarını yapılandırın

LLM analizinin yeniden çalıştırılması için gerekli API anahtarını `.env` dosyasında, notebook içerisinde kullanılan ortam değişkeni adıyla tanımlayın.

API anahtarlarını kaynak koduna veya rapora eklemeyin.

### 5. Notebook'ları çalıştırın

`notebooks/` klasöründeki notebook'ları aşağıdaki sırayla çalıştırın:

1. `01_veri_kesfi.ipynb`
2. `02_veri_temizligi.ipynb`
3. `03_musteri_analiz_tablosu.ipynb`
4. `04_eda_ve_is_analizi.ipynb`
5. `05_churn_modelleme.ipynb`
6. `06_llm_metin_analizi.ipynb`

Notebook'lar, veri hazırlama aşamasından modelleme ve LLM tabanlı metin analizine doğru ilerleyen bir iş akışını takip eder.

**Not:** Notebook'ların çalıştırılabilmesi için gerekli veri dosyalarının ve bağımlılıkların mevcut olması gerekir. LLM adımının yeniden çalıştırılması ayrıca geçerli bir API anahtarı gerektirir.

## Çıktılar

Üretilen analiz sonuçları `ciktilar/analiz/`, temizlenmiş veri setleri ise `ciktilar/temiz_veriler/` klasöründe bulunmaktadır.

Öne çıkan çıktılar:

- Müşteri analiz tablosu ve keşifsel analiz sonuçları
- Churn modelleme ve performans değerlendirme çıktıları
- LLM tarafından etiketlenen müşteri geri bildirimleri (`llm_etiketleri.csv`)
- Manuel etiketlenmiş değerlendirme veri seti (`altin_set.csv`)

## Rapor

Analiz yöntemleri, bulgular, model performansı, iş önerileri ve değerlendirmeler `rapor/RAPOR.md` dosyasında sunulmuştur. Yapay zekâ araçlarının kullanımına ilişkin ayrıntılar `rapor/AI_KULLANIM_GUNLUGU.md` dosyasında yer almaktadır.