# ANALISIS DATASET NSL-KDD DENGAN K-MEANS

K-means merupakan salah satu algoritma yang bersifat unsupervised learning. K-Means memiliki fungsi untuk mengelompokkan data kedalam data cluster. Algoritma ini dapat menerima data tanpa ada label kategori. Algoritma k-means dipilih karena metode klasterisasi dipakai membagi data menjadi beberapa klaster, yang mana data dengan kemiripan tinggi dikelompokkan menjadi satu cluster dan data dengan karakteristik beda dikelompokkan ke cluster berbeda.

Tujuan K-Means dipakai di dataset ini adalah, membagi data menjadi 5 kluster

# Dataset NSL KDD

https://bit.ly/dataset-project-DAP-kel2

Dataset for Intrusion Detection System

# Langkah 1 - Mengumpulkan Data 
```
ids <- read.csv("C:/Users/ASUS/Downloads/ids lama.csv", sep=";", stringsAsFactors=TRUE)
View(ids)
```
![WhatsApp Image 2024-01-02 at 08 20 25](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/10e202ab-08be-4960-ab1a-88918057084f)

mengimport data dari file dan menampilkan dataset di rstudio, disini data terdiri dari 42 kolom dan 22.455 baris

# Melihat visualisasi distribusi data 
```
boxplot(ids)
```
![ids-sblm](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/f0b84119-f837-4fa0-8f4e-5c8a68dea4e4)

distribusi data belum baik, maka dari itu kita harus melakukan normalisasi data terlebih dahulu

# So that the absolute value of 0 is not mistaken for NA
```
ids[is.na(ids)] <- 0
```
pada dataset ini, ada beberapa kolom yang isi datanya "0", tetapi bukan data kosong dan memang nilainya 0. 
maka kita submit syntax ini untuk menginisialisasi nilai 0 bukan data NA 

# Read in data and examine structure
```
str(ids)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/205fe1af-1baa-4f44-98f7-5c7dc2254b93)

syntax ini bertujuan untuk melihat tipe dari struktur data, karena kita ingin menyesuaikan dengan k-means nanti yang mengaharuskan data bertipe "numerik"

# Custom normalization function
```
normalize <- function(x) { 
  return((x - min(x)) / (max(x) - min(x)))
}
```
menormalisasi data dengan metode max min agar rentang nilai dari dataset lebih baik

# Apply normalization to entire data frame
```
ids_norm <- as.data.frame(lapply(ids, normalize))
str(ids_norm)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/cf6cce92-e291-4b0e-8b3a-d044a496c6af)

melakukan normalisasi pada setiap elemen dari objek ids dan menyimpan hasilnya dalam bentuk data frame baru yang disebut ids_norm.

# Confirm that the range is now between zero and one
```
summary(ids_norm$labels)
```
![summaryidsnorm](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/e4529b19-5305-4ca0-8a5d-f10c26cb7429)

Kesimpulan yang dapat diambil adalah distribusi nilai dalam kolom labels memiliki variasi, dengan rata-rata sekitar 0.4630. Kuartil pertama dan kuartil ketiga yang relatif dekat menunjukkan bahwa sebagian besar data terkonsentrasi di sekitar nilai tersebut. Nilai maksimum adalah 1.0000, yang menunjukkan adanya variasi yang signifikan antara nilai minimum dan maksimum.

# Compared to the original minimum and maximum
```
boxplot(ids_norm)
```
![9c9d05fd-57a8-47b9-bd0a-16245aa52edb](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/7cd3d4fe-26f0-4894-80d6-2b77dd611d04)

data ids sudah terdistribusi normal

# Langkah 2 CORRELATION
```
Faktor_Berpengaruh <- cor(ids_norm[c("duration", "protocol_type", "service", "flag", "src_bytes", 
                                "dst_bytes", "land", "wrong_fragment", "urgent", "hot", 
                                "num_failed_logins", "logged_in", "num_compromised", 
                                "root_shell", "su_attempted", "num_root", 
                                "num_file_creations", "num_shells", "num_access_files", 
                                 "is_host_login", "is_guest_login", 
                                "count", "srv_count", "serror_rate", 
                                "srv_serror_rate", "rerror_rate", "srv_rerror_rate", 
                                "same_srv_rate", "diff_srv_rate", "srv_diff_host_rate", 
                                "dst_host_count", "dst_host_srv_count", 
                                "dst_host_same_srv_rate", "dst_host_diff_srv_rate", 
                                "dst_host_same_src_port_rate", "dst_host_srv_diff_host_rate", 
                                "dst_host_serror_rate", "dst_host_srv_serror_rate", 
                                "dst_host_rerror_rate", "dst_host_srv_rerror_rate", "labels")],
                          use = "complete.obs")

print(Faktor_Berpengaruh)
```
karena variabel dari data ids cukup banyak, maka akan dilakukan filterisasi dengan melihat korelasi terlebih dahulu

# melihat nilai korelasi antara variabel dengan kolom "labels"
```
Faktor_Berpengaruh["duration", "labels"]
Faktor_Berpengaruh["protocol_type", "labels"]
Faktor_Berpengaruh["service", "labels"]
Faktor_Berpengaruh["flag", "labels"]
Faktor_Berpengaruh["src_bytes", "labels"]
Faktor_Berpengaruh["dst_bytes", "labels"]
Faktor_Berpengaruh["land", "labels"]
Faktor_Berpengaruh["wrong_fragment", "labels"]
Faktor_Berpengaruh["urgent", "labels"]
Faktor_Berpengaruh["hot", "labels"]
Faktor_Berpengaruh["num_failed_logins", "labels"]
Faktor_Berpengaruh["logged_in", "labels"]
Faktor_Berpengaruh["num_compromised", "labels"]
Faktor_Berpengaruh["root_shell", "labels"]
Faktor_Berpengaruh["su_attempted", "labels"]
Faktor_Berpengaruh["num_root", "labels"]
Faktor_Berpengaruh["num_file_creations", "labels"]
Faktor_Berpengaruh["num_shells", "labels"]
Faktor_Berpengaruh["num_access_files", "labels"]
Faktor_Berpengaruh["is_host_login", "labels"]
Faktor_Berpengaruh["is_guest_login", "labels"]
Faktor_Berpengaruh["count", "labels"]
Faktor_Berpengaruh["srv_count", "labels"]
Faktor_Berpengaruh["serror_rate", "labels"]
Faktor_Berpengaruh["srv_serror_rate", "labels"]
Faktor_Berpengaruh["rerror_rate", "labels"]
Faktor_Berpengaruh["srv_rerror_rate", "labels"]
Faktor_Berpengaruh["same_srv_rate", "labels"]
Faktor_Berpengaruh["diff_srv_rate", "labels"]
Faktor_Berpengaruh["srv_diff_host_rate", "labels"]
Faktor_Berpengaruh["dst_host_count", "labels"]
Faktor_Berpengaruh["dst_host_srv_count", "labels"]
Faktor_Berpengaruh["dst_host_same_srv_rate", "labels"]
Faktor_Berpengaruh["dst_host_diff_srv_rate", "labels"]
Faktor_Berpengaruh["dst_host_same_src_port_rate", "labels"]
Faktor_Berpengaruh["dst_host_srv_diff_host_rate", "labels"]
Faktor_Berpengaruh["dst_host_serror_rate", "labels"]
Faktor_Berpengaruh["dst_host_srv_serror_rate", "labels"]
Faktor_Berpengaruh["dst_host_rerror_rate", "labels"]
Faktor_Berpengaruh["dst_host_srv_rerror_rate", "labels"]
```
mendapatkan nilai korelasi dari perbandingan variabel dengan kolom "labels"

# Mendapatkan daftar nama kolom dari dataset 
```
column_names <- colnames(ids_norm)
print(column_names)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/877da455-9614-42c9-acd8-4bc6bfa4066b)

# Menjadikan 6 kolom dengan nilai korelasi tertinggi menjadi satu dataset
```
selected_columns <- c("srv_count", "dst_host_diff_srv_rate", "dst_host_same_src_port_rate", "is_guest_login", "wrong_fragment", "count", "labels")
```
Agar proses analisis berjalan dengan lebih cepat, maka 6 kolom dengan tingkat korelasi tertinggi dengan label dipilih dan dijadikan satu set data baru

# Memilih kolom-kolom yang diinginkan dari dataset
```
ids_new <- ids_norm[, column_names %in% selected_columns]
```
memilih kolom-kolom tertentu dari ids_norm berdasarkan kolom-kolom yang terdapat dalam vektor selected_columns

# Menampilkan dataset baru
```
print(ids_new)
str(ids_new)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/632bc72c-8715-4a0e-af45-604ade119c99)

# Langkah 3 - Mulai analisis k-means
```
library(factoextra)
library(cluster)
```
packages ini merupakan packages yang diperlukan jika ingin menganalisis menggunakan algoritma k-means

# membentuk cluster (sesuaikan dengan cluster yg dipilih)
```
cluster <- kmeans(ids_new,5)
```
# visualisasi cluster
```
fviz_cluster(cluster,ids_new) 
```
![Rplot](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/186cef37-ede7-46d4-a898-a87d1738069b)

Ini adalah hasil visualisasi menggunakan algoritma k-means yang dibuat menjadi 5 cluster dari data ids_new

# interpretasi dan penamaan cluster
```
cluster$centers
```
`cluster$centers` adalah hasil dari algoritma klustering yang menyediakan nilai rata-rata untuk setiap atribut dalam setiap kluster. Pusat kluster ini adalah representasi numerik dari karakteristik rata-rata dari data dalam masing-masing kluster.

![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/eec392c6-7887-4caf-8563-a0de86fcd94d)

1. **Cluster 1:**
   - Rata-rata `count` dan `srv_count` cukup rendah.
   - Kemungkinan merupakan lalu lintas jaringan normal atau serangan ringan.
   - **Prediksi:** Normal atau Probes.

2. **Cluster 2:**
   - Rata-rata `count` dan `srv_count` sangat tinggi.
   - Kemungkinan merupakan serangan Denial of Service (DoS) dengan lalu lintas yang tinggi.
   - **Prediksi:** DoS.

3. **Cluster 3:**
   - Memiliki `wrong_fragment` dan `dst_host_diff_srv_rate` yang sedikit lebih tinggi.
   - Kemungkinan memiliki karakteristik serangan yang tidak umum atau variasi serangan.
   - **Prediksi:** Probes atau jenis serangan yang tidak umum.

4. **Cluster 4:**
   - Rata-rata `count` dan `srv_count` sedang.
   - Kemungkinan merupakan lalu lintas normal atau serangan ringan.
   - **Prediksi:** Normal atau Probes.

5.  **Cluster 5**
   - Rata-rata `count` dan `srv_count` tinggi.
   - Memiliki `dst_host_diff_srv_rate` yang lebih tinggi.
   - Kemungkinan merupakan serangan Remote to Local (R2L) atau jenis serangan dengan percobaan penetrasi tinggi.
   -  **Prediksi:** R2L atau jenis serangan dengan percobaan penetrasi tinggi.

```
k5 = kmeans(ids_new, centers = 5, nstart = 25)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/21cc4758-50f7-42df-8fc1-76550e82c609)

`kmeans(ids_new, centers = 5, nstart = 25)` digunakan untuk menerapkan algoritma k-means clustering pada data `ids_new` dengan tujuan membentuk 5 kluster. Parameter `nstart = 25` menunjukkan bahwa algoritma akan dijalankan sebanyak 25 kali dengan inisialisasi pusat kluster yang berbeda untuk memilih solusi terbaik.

# Disini untuk melihat sebaran tiap cluster ada berapa banyak
```
print(k5)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/2298b33e-7ee1-4434-ab63-23fbb46d5ab6)

Terdapat 5 kluster dengan ukuran masing-masing kluster adalah 14361, 517, 6213, 329, dan 1125 observasi.

Kesimpulannya, klustering telah berhasil membagi data menjadi 5 kluster dengan karakteristik yang berbeda, dan kita dapat menganalisis ciri khas masing-masing kluster berdasarkan nilai rata-rata variabel.
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/14b535b4-c460-40f1-b6dc-7e1bb3aae95b)

Rasio (between_SS / total_SS) atau disebut juga sebagai proporsi varians antara kelompok terhadap total varians, mengindikasikan seberapa baik klausterisasi berhasil memisahkan data. Pada kasus ini, nilai 68.8% menunjukkan bahwa sekitar 68.8% dari total varians dapat dijelaskan oleh perbedaan antara kelompok (kluster), sementara sisanya mungkin merupakan varians yang ada di dalam kluster itu sendiri. Semakin tinggi nilai rasio ini, semakin baik klausterisasi tersebut dalam memisahkan data ke dalam kelompok-kelompok yang berbeda.

# mengembalikan dari data normalisasi ke data sebelum normalisasi
```
final=data.frame(ids, k5$cluster)
View(final)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/deebec94-25b8-4636-abdd-bbb8ab0e12c7)

# Menggunakan fungsi dist() untuk menghitung jarak Euclidean
```
distances <- dist(cluster$centers, method = "euclidean")
print(distances)
```
![image](https://github.com/walkeyzz/IDS-ANALYSIS/assets/147698371/028d5876-55ea-4ec1-96ef-027db9b27485)

Semakin kecil jarak, semakin dekat entitas tersebut. 
