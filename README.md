**ABD Trafik Kazaları Şiddet Tahmini ve Analizi**
(İMAGE Burası İçin Öneri: Python ile oluşturduğunuz, ABD haritası üzerinde kaza yoğunluğunu gösteren şık bir harita görseli koyun)
***📌 Proje Özeti***

Bu proje, ABD genelindeki trafik kazalarını içeren geniş kapsamlı bir veri seti kullanılarak, kazaların şiddet derecesini (Severity 1-4) tahmin etmeyi amaçlayan bir veri madenciliği grup çalışmasıdır.

Proje kapsamında 5 kişilik ekibimiz, veri setini farklı açılardan ele almıştır. Her ekip üyesi, problemin farklı boyutlarına odaklanarak farklı öznitelik grupları (feature subsets) seçmiş ve farklı makine öğrenmesi algoritmaları ile sınıflandırma performansı ölçmüştür. Amaç, kaza şiddetini en iyi açıklayan faktörleri ve en başarılı model mimarisini belirlemektir.

***📊 Veri Seti Hakkında***
Kaynak: [Kaggle US Accidents Dataset (Linkini buraya ekleyin)]

Boyut: 7 Milyon Satır, 46 Sütun

Hedef Değişken (Target): Severity (1: Hafif, 2: Orta, 3: Ciddi, 4: Çok Ciddi/Ölümcül)

***🖼️ Proje Görselleri***
Hedef Değişken Dağılımı: 

[!alt img/hedef-degisken-dagilimi.png]

Cografi Dağılım: 
[COGRAFI DAGILIM GÖRSELİ GELECEK]

***👨‍💻 Ekip Çalışması ve Yöntemler***
Bu bölümde her üyenin odaklandığı alan, kullandığı feature setleri ve algoritmalar detaylandırılmıştır.

👤 Üye 1: Sıla - [Odak Alanı: PCA ile Boyut İndirgeme ve Ensemble Modeller]
Yaklaşım: Büyük veri seti (2 Milyon örneklem) üzerinde çalışılmış ve hesaplama maliyetini düşürmek, aynı zamanda gürültüyü azaltmak amacıyla PCA (Principal Component Analysis) tekniği uygulanarak varyansın %95'i korunmuştur. Sınıflandırma için güçlü topluluk (ensemble) algoritmaları tercih edilmiştir.

Seçilen Feature'lar:

Meteorolojik: Temperature(F), Humidity(%), Pressure(in), Visibility(mi), Wind_Speed(mph), Precipitation(in).

Zamansal (Üretilen): Hour_of_Day, Is_Weekend_Day, During_Rush_Hour (Yoğun saatler), Accident_Duration_Min.

Yol & Yapı: Signal_Stop_Present, Junction, High_Impact_Incident.

Not: Bu özellikler işlendikten sonra PCA ile temel bileşenlere dönüştürülmüştür.

Kullanılan Modeller:

Majority Class Classifier: (Baseline/Referans model olarak)

Logistic Regression: (PCA sonrası doğrusal ayırıcı performansını görmek için)

Bagging Classifier: (Decision Tree tabanlı, varyansı düşürmek için)

XGBoost: (Yüksek performanslı gradient boosting modeli)

Elde Edilen Sonuç: PCA uygulanmış veri setinde Logistic Regression ile %60.5 doğruluk (Accuracy) ve 0.58 F1-Score elde edilmiştir. Bagging ve XGBoost modelleri ile bu skorun üzerine çıkılarak, özellikle Severity 2 ve Severity 3 sınıflarının ayrımında iyileştirme sağlanmıştır.

![alt text](img\sila-xgboost-confusion.png) ![alt text](img\sila-logistic-regressin-pca.png) ![alt text](img\sila-baseline.png) ![alt text](img\sila-baseline2.png) 

Ek Not: Kod içerisinde veri temizleme (Outlier Clipping), eksik veri doldurma (Imputation) ve kategorik veriler için One-Hot Encoding işlemleri profesyonelce bir pipeline (iş akışı) şeklinde kurgulanmış.

👤 Üye 2: İlkay Özkan - [Odak Alanı: Boosting Algoritmaları ve Hiperparametre Optimizasyonu]
Yaklaşım: Veri setindeki dengesizliği ve karmaşık ilişkileri yönetmek için modern Gradient Boosting kütüphaneleri tercih edilmiştir. Özellikle kategorik değişkenleri otomatik işleyebilen CatBoost ve zayıf öğrenicileri güçlendiren AdaBoost algoritmaları üzerinde durulmuştur. GridSearchCV kullanılarak modellerin en iyi parametreleri (learning rate, depth vb.) optimize edilmiştir.

Seçilen Feature'lar:

Model, veri setindeki tüm belirleyici özellikleri kullanmış ve Feature Importance analizi ile en etkili faktörleri belirlemiştir.

Öne çıkan özellikler (Grafiklerden çıkarım): Coğrafi/Konumsal veriler ve zaman bilgileri ağırlıklı olarak model tarafından önemli bulunmuştur.

Kullanılan Modeller:

Dummy Classifier: (Başarım kıyaslaması için baz model)

AdaBoost Classifier: (Decision Tree tabanlı)

CatBoost Classifier: (Yüksek performanslı ve kategorik veri dostu boosting algoritması)

Elde Edilen Sonuç:

AdaBoost modeli optimize edilerek varsayılan ayarlara göre başarım artırılmıştır.

CatBoost modeli ile en yüksek doğruluk ve ayırt edicilik (ROC-AUC) değerlerine ulaşılmıştır. Modelin karmaşıklık matrisi (Confusion Matrix), özellikle ciddi kazaları (Severity 3 ve 4) tahmin etmede başarılı olduğunu göstermektedir.

![alt text](img\ilkay-catboost.png) ![alt text](img\ilkay-catboost-blok-grafiği.png) 

👤 Üye 3: [İsim Soyisim] - [Odak Alanı: Zaman ve Konum Analizi]
Yaklaşım: Kazanın gerçekleştiği eyalet, saat, gün ve ay gibi zamansal/mekansal veriler kullanılmıştır.

Seçilen Feature'lar: Start_Time (işlenerek Hour, Day, Month), State, City, Timezone.

Kullanılan Modeller:

K-Nearest Neighbors (KNN)

Naive Bayes

Elde Edilen Sonuç: Gece saatlerinde gerçekleşen kazaların şiddetinin gündüze göre daha yüksek olduğu saptanmıştır.

👤 Üye 4: [İsim Soyisim] - [Odak Alanı: Tüm Sayısal Veriler & PCA]
Yaklaşım: Veri setindeki tüm nümerik değerler alınıp, PCA (Principal Component Analysis) ile boyut indirgeme yapılmıştır.

Kullanılan Modeller:

XGBoost

LightGBM

Elde Edilen Sonuç: Boosting algoritmalarının bu veri setinde en hızlı ve yüksek sonucu verdiği görülmüştür.

Görsel: Modelin karmaşıklık matrisi (Confusion Matrix).

👤 Üye 5: [İsim Soyisim] - [Odak Alanı: Metin Madenciliği (Description Sütunu)]
Yaklaşım: Veri setindeki Description sütunu kullanılarak NLP (Doğal Dil İşleme) teknikleri ile kaza açıklamalarından şiddet tahmini yapılmıştır.

Kullanılan Modeller:

TF-IDF + Random Forest

Neural Networks (Basit YSA)

Elde Edilen Sonuç: Kaza açıklamalarında geçen "blocked", "closed" gibi kelimelerin yüksek şiddetli kazalarla ilişkili olduğu görülmüştür.