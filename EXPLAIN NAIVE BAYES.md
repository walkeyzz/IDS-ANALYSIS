# ANALISIS DATASET NSL-KDD DENGAN NAIVE BAYES
Naive Bayes merupakan sebuah pengklasifikasian probabilistik sederhana yang menghitung sekumpulan probabilitas dengan menjumlahkan frekuensi dan kombinasi nilai dari dataset yang diberikan.
Algoritma mengunakan teorema Bayes dan mengasumsikan semua atribut independen atau tidak saling ketergantungan yang diberikan oleh nilai pada variabel kelas.

Tujuan Naive Bayes dipakai di dataset ini adalah untuk melihat tingkat akurasi naive bayes dalam menganalisis data ini

# Langkah 1 - Mengumpulkan Data
```
ids <- read.csv("C:/Users/ASUS/Downloads/ids lama.csv", sep=";", stringsAsFactors=TRUE)
View(ids)
```
![WhatsApp Image 2024-01-02 at 08 20 25](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/10e202ab-08be-4960-ab1a-88918057084f)

mengimport data dari file dan menampilkan dataset di rstudio, disini data terdiri dari 42 kolom dan 22.455 baris

# Transformasi dan Persiapan Data
```
str(ids)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/205fe1af-1baa-4f44-98f7-5c7dc2254b93)

syntax ini bertujuan untuk melihat tipe dari struktur data

# Menangani nilai yang hilang
```
ids[is.na(ids)] <- 0
```
pada dataset ini, ada beberapa kolom yang isi datanya "0", tetapi bukan data kosong dan memang nilainya 0. maka kita submit syntax ini untuk menginisialisasi nilai 0 bukan data NA

# Merubah nama labels(serangan) dari angka menjadi nama
```
ids$labels <- factor(ids$labels, levels = c("1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", "16", "17", "18", "19", "20", "21", "22", "23", "24", "25", "26", "27", "28", "29", "30", "31", "32", "33", "34", "35", "36"), labels = c("apache2", "back", "buffer_overflow", "ftp_write", "guess_passwd", "httptunnel", "imap", "ipsweep", "land", "loadmodule", "mailbomb", "mscan", "multihop", "named", "neptune", "nmap", "normal", "perl", "phf", "pod", "portsweep", "processtable", "ps", "rootkit", "saint", "satan", "sendmail", "smurf", "snmgetattack", "snmpguess", "teardrop", "warezclient", "warezmaster", "xlock", "xsnoop", "xterm"))

ids$labels
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/e2e7e1ad-98eb-44d3-8585-34a918dbe24f)

untuk pengubahan dari angka menjadi nama label sudah disesuaikan dengan ketentuan sumber jurnal. 

1 = apache2

2 = back

dst..

# Buat kolom label baru yang 5 kategori serangan
```
ids$labels2 <- NA
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/c23df096-8eb1-4f41-b99a-d353c196223d)

kolom `labels2` dibuat NA atau kosong dahulu, nanti baru diisi dengan kategori serangan baru

# Mengelompokkan variabel sesuai dengan kategori yang diinginkan
```
ids$labels2[ids$labels %in% c("normal")] <- "normal"
ids$labels2[ids$labels %in% c("back", "land", "pod", "smurf", "teardropdrop", "apache2", "udpstorm", "processtable", "mailbomb", "neptune")] <- "Dos"
ids$labels2[ids$labels %in% c("satan", "ipsweep", "nmap", "portsweep", "mscan", "saint")] <- "Probe"
ids$labels2[ids$labels %in% c("guess_passwd", "ftp_write", "imap", "phf", "multihop", "warezmaster", "xlock", "xsnoop", "snmpguess", "snmpgetattack", "httptunnel", "sendmail", "named")] <- "R2L"
ids$labels2[ids$labels %in% c("buffer_overflow", "loadmodule", "rootkit", "perl", "sqlattack", "xterm", "ps")] <- "U2R"
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/5c7f6bd8-d003-45e7-aeeb-c7c6483d9377)

hal ini bertujuan agar kita mengkategorikan `labels` yang terdiri dari 36 kategori serangan, di kategorikan lagi jadi 5 kategori serangan dan diletakkan pada kolom `labels2`

# Memfilter data numerik
```
numeric_columns <- ids[, sapply(ids, is.numeric)]
numeric_columns
```
saat normalisasi data, diperlukan data yang numerik saja, maka dari itu, kita harus memfilternya terlebih dahulu agar tidak tergabung dengan data lainnya

# Langkah 2 - Normalisasi Data

# Visualisasi sebaran data per variabel
```
boxplot(numeric_columns)
```
![ids-sblm](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/f0b84119-f837-4fa0-8f4e-5c8a68dea4e4)

distribusi data belum baik, maka dari itu kita harus melakukan normalisasi data terlebih dahulu

# Normalisasi min-max 
```
normalize <- function(x) { 
  return((x - min(x)) / (max(x) - min(x)))
}
```
menormalisasi data dengan metode max min agar rentang nilai dari dataset lebih baik

# Terapkan normalisasi ke seluruh bingkai data frame
```
ids_norm <- as.data.frame(lapply(numeric_columns, normalize))
boxplot(ids_norm)
summary(ids_norm)
```
melakukan normalisasi pada setiap elemen dari objek ids dan menyimpan hasilnya dalam bentuk data frame baru yang disebut `ids_norm`

![9c9d05fd-57a8-47b9-bd0a-16245aa52edb](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/7cd3d4fe-26f0-4894-80d6-2b77dd611d04)

data ids sudah terdistribusi normal

![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/904b8f56-aaea-4bea-8884-87000521b349)

# Menyatukan kembali dengan kolom "labels"
ids_new <- cbind(ids[c("labels","labels2")], ids_norm)

![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/5926eaa4-f2bc-49ce-a02b-135f3e9964f2)

karena isi dari `ids_norm` tidak memiliki label, maka kita gabungkan kembali dengan label sebelumnya

# Membuat data testing dan data training
```
ids_train <- ids_new[1:15781, ]
ids_test <- ids_new[15782:22544, ]
round(prop.table(table(ids_new$labels2)) * 100, digits = 1)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/c46aee19-fab9-4312-9ef8-9830365957b5)

distribusi kategori menunjukkan bahwa sebagian besar data uji tergolong ke dalam kategori "normal" dan "Dos", sementara aktivitas anomali cenderung lebih jarang terjadi.

# Langkah 3 - Analisis Naive Bayes

# Mulai analisis naive bayes
```
library(e1071)
library(caret)
modelNB = naiveBayes(labels2~. , data=ids_train)
```
- melakukan library terhadap packages yang diperlukan dalam analisis menggunakan algoritma Naive Bayes
- membuat model naive bayes dengan mengambil label kolom `labels2` dari data `ids_train`

# Melakukan prediksi
```
prediksi = predict(modelNB,ids_test)
prediksi
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/5eea07f1-f851-4662-9af9-1b3cdfe11852)

model Naive Bayes telah melakukan prediksi kategori pada data pengujian. Distribusi prediksi menunjukkan variasi antara kategori "Dos", "normal", "Probe", "R2L", dan "U2R".

# Evaluasi
```
hasil = confusionMatrix(table(prediksi,ids_test$labels2))
hasil
```

![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/aa869571-baa4-484c-9420-13f6c5000c59)

Dari hasil confusion matrix dan statistik evaluasi model, beberapa kesimpulan yang dapat diambil adalah:

1. **Akurasi dan Kappa:**
   - Akurasi model sekitar 49.17%, yang mengindikasikan tingkat keseluruhan kebenaran prediksi.
   - Nilai Kappa sekitar 0.33, yang mengukur tingkat kesepakatan antara prediksi dan realita melebihi kebetulan.

2. **Statistik untuk Setiap Kelas:**
   - Kategori "Dos" memiliki sensitivitas (recall) yang tinggi (92.81%), menunjukkan model dapat dengan baik mengidentifikasi kategori "Dos".
   - Kategori "normal" memiliki sensitivitas yang rendah (7.94%), mungkin karena jumlah data yang kurang.
   - Kategori "Probe" memiliki tingkat sensitivitas yang tinggi (99.85%), menunjukkan model sangat baik dalam mengidentifikasi "Probe".
   - Kategori "R2L" memiliki sensitivitas sekitar 55.42%, menunjukkan performa sedang.
   - Kategori "U2R" memiliki sensitivitas sekitar 50%, menunjukkan hasil yang dapat ditingkatkan.

3. **Spesifikitas:**
   - Model memiliki tingkat spesifikitas yang tinggi untuk kategori "normal" dan "U2R", menunjukkan kemampuan untuk menghindari kesalahan positif.
   - Untuk kategori "Probe" dan "R2L", spesifikasinya cukup baik.
   - Kategori "Dos" memiliki spesifikasi sekitar 69.35%, menunjukkan tingkat kesalahan positif yang cukup tinggi.

4. **Nilai Prediktif Positif (Pos Pred Value):**
   - Kategori "normal" memiliki nilai positif prediktif tinggi (97.10%), menunjukkan keandalan model dalam memprediksi kategori tersebut.
   - Kategori "Dos" memiliki nilai positif prediktif sekitar 63%, menunjukkan tingkat ketidakpastian yang cukup tinggi.

5. **Sebaran Kelas dan Deteksi:**
   - Sebaran kelas pada data uji menunjukkan ketidakseimbangan, terutama untuk kategori "normal".
   - Model cenderung lebih baik dalam mendeteksi kategori "Dos" dan "Probe" dibandingkan dengan "R2L" dan "U2R".

6. **Kesimpulan Umum:**
   - Meskipun model memiliki sensitivitas yang tinggi untuk beberapa kategori, terdapat kebutuhan untuk meningkatkan spesifikasinya, terutama pada kategori "Dos".
   - Evaluasi lebih lanjut dan penyesuaian model mungkin diperlukan untuk meningkatkan performa dan mengatasi ketidakseimbangan kelas.
