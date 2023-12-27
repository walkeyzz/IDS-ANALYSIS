# IDS-ANALYSIS
This is an assignment from the DAP Project by Keiko and Marshanda 3ITE1


# Menyiapkan Dataset
```
ids <- read.csv("C:/CCIT UI/SEMESTER 3/DAP/IDS/ids.csv", sep=";")
```

# Agar nilai mutlak 0 tidak dikira NA
```
ids[is.na(ids)] <- 0
```
# Baca data dan periksa strukturnya
```
str(ids)
```
# Fungsi normalisasi
```
normalize <- function(x) { 
  return((x - min(x)) / (max(x) - min(x)))
}
```
# Terapkan normalisasi ke seluruh data frame
```
ids_norm <- as.data.frame(lapply(ids, normalize))
str(ids_norm)
```
# Konfirmasikan bahwa rentangnya sekarang antara nol dan satu
```
summary(ids_norm$labels)
```
![WhatsApp Image 2023-12-25 at 12 04 46_436f596d](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/206c57cf-7cf4-4ed2-9b3e-5038c0ec42da)

# Dibandingkan dengan minimum dan maksimum aslinya
```
summary(ids$labels)
```
![WhatsApp Image 2023-12-25 at 12 05 08_063d12d8](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/c28556f0-ef70-4415-af8e-cf8ba9b11832)

# Create training data
```
ids_train <- ids_norm[1:"1571", ]
head(ids_train$labels)
str(ids_train)
```
![WhatsApp Image 2023-12-25 at 12 05 25_632a7ca6](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/8b8c15c3-3cf3-42bb-a53a-66ed87d8fbc4)

setelah membuat data train nya, kita harus lihat dulu isi nya apakah sudah sesuai dengan fungi head dan juga str untuk melihat keseluruhan struktur data nya

# Create testing data
```
ids_test <- ids_norm[1572:"2245", ]
head(ids_test$labels)
str(ids_test)
```
![WhatsApp Image 2023-12-25 at 12 05 43_c7667780](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/0ce4f5a0-d7f1-4425-9e12-0608612c4d91)

Setelah membuat data test nya, kita harus lihat dulu isi nya apakah sudah sesuai dengan fungi head dan juga str untuk melihat keseluruhan struktur data nya

# Standard Deviation for train and test data to labels column
```
sd(ids_train$labels)
sd(ids_test$labels)
```
Perlu untuk melihat standar deviasi dari sebuah data, agar kita tau tingkat penyebaran data terhadap rata-rata data tersebut
![WhatsApp Image 2023-12-25 at 12 06 02_42716199](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/7af822f0-3b00-4726-a893-738d0b504025)

# Training a model on the data 
# Train the neuralnet model
```
library(neuralnet)
```
Ini adalah packages dan library yang diperlukan untuk analisis ANN

# Simple ANN with only a single hidden neuron
```
set.seed(12345) # to guarantee repeatable results
ids_model <- neuralnet(formula = labels ~ srv_count + dst_host_diff_srv_rate +
                         dst_host_same_src_port_rate + is_guest_login + flag + 
                         wrong_fragment,
                       data = ids_train)
```
Fungsi set seed adalah untuk hasil yang dapat diulang (reproducible). Memastikan bahwa hasil yang dihasilkan oleh fungsi-fungsi acak di R akan selalu sama setiap kali kode dijalankan. 
ids_model <- neuralnet(...): Fungsi ini membuat model jaringan saraf (neural network) menggunakan paket neuralnet di R. Code formula yang terdapat di dalam nya bermaksud menyatakan hubungan antara variabel dependen (labels) dan variabel independen.
Intinya model ini akan belajar untuk memetakan fitur-fitur tersebut ke variabel dependen (labels) menggunakan jaringan saraf. Hasilnya disimpan dalam objek ids_model

# Visualize the network topology
```
plot(ids_model)
```
Fungsi plot untuk melihat visualisasi nya
![WhatsApp Image 2023-12-22 at 22 22 48_c8747214](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/ff1c397c-b28d-4a60-8d71-c63e213485ae)


# Evaluating model performance 
# Dapatkan hasil model
```
model_results <- compute(ids_model, ids_test[1:41])
```
Untuk melakukan prediksi menggunakan model jaringan saraf yang telah dilatih. Fungsi compute digunakan untuk melakukan perhitungan (inference) dengan menggunakan model jaringan saraf (ids_model) pada data uji (ids_test[1:41]).

# Dapatkan nilai label yang diprediksi
```
predicted_labels <- model_results$net.result
```
Untuk mengambil hasil prediksi dari model jaringan saraf yang telah kita latih pada data uji.
model_results$net.result: Code ini mengakses hasil prediksi dari objek model_results. Dalam konteks jaringan saraf, net.result biasanya berisi nilai-nilai prediksi yang dihasilkan oleh model untuk setiap sampel dalam data uji
Lalu kita simpan nilai hasil prediksi kedalam predicted_labels

# Periksa korelasi antara nilai prediksi dan nilai aktual
```
predicted_labels <- as.vector(predicted_labels)
cor(predicted_labels, ids_test$labels)
```
Kode diatas digunakan untuk memeriksa korelasi antara nilai prediksi yang dihasilkan oleh model dan nilai aktual dari data uji.
Pertama-tama kita mengonversi objek predicted_labels menjadi vektor menggunakan fungsi as.vector(), kita ingin memastikan bahwa kita memiliki vektor satu dimensi.
Fungsi cor() digunakan untuk menghitung koefisien korelasi antara dua vektor. Dalam konteks ini:
1. predicted_labels: Ini adalah vektor yang berisi nilai prediksi yang dihasilkan oleh model.
2. ids_test$labels: Ini adalah vektor yang berisi nilai aktual (ground truth) dari data uji.
   ![WhatsApp Image 2023-12-25 at 12 06 15_458d4805](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/862218c0-b354-4c9d-a005-51302eba9fde)

# Improving model performance 
# A more complex neural network topology with 5 hidden neurons
# Prepare Data
```
ids2 <- ids_norm
str(ids2)
ids2 <- as.data.frame(lapply(ids2, as.integer))
```
Kurang lebih sama dengan step sebelumnya, namun variabel ids2 saya convert ke tipe data integer, karena by default, ids_norm merupakan tipe data numerik dan model tidak mau meng computating jika tipe data bukan integer
# Prepare Data Train
```
ids_train2 <- ids2[1:"1571", ]
str(ids_train2)
```
# Prepare Data Test
```
ids_test2 <- ids2[1572:"2245", ]
str(ids_test2)
```
# ANN with 5 hidden neuron
```
set.seed(12345) # to guarantee repeatable results
ids_model2 <- neuralnet(labels ~ srv_count + dst_host_diff_srv_rate +
                          dst_host_same_src_port_rate + is_guest_login + flag + 
                          wrong_fragment,
                        data = ids_train2, hidden = 5)
```
Disini kurang lebih sama dengan yang sebelumnya cuma bedanya kita menambahkan 5 hidden layer kedalam nya. 

# plot the network
```
plot(ids_model2)
```
![WhatsApp Image 2023-12-22 at 22 22 49_f1f8ea15](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/485a456c-6bc8-4b29-87eb-084fc5d0e804)

# Mengevaluasi hasilnya seperti yang kita lakukan sebelumnya
```
model_results2 <- compute(ids_model2, ids_test[1:41])
predicted_labels2 <- model_results2$net.result
cor(predicted_labels2, ids_test$labels)
```
![WhatsApp Image 2023-12-25 at 12 06 43_2a51a55c](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/5543deb4-00ea-4f09-ae41-0d46b702c164)

# Latih model neuralnet dengan aktivasi sigmoid
```
library(neuralnet)

set.seed(12345) # to guarantee repeatable results
ids_model_sigmoid <- neuralnet(formula = labels ~ srv_count + dst_host_diff_srv_rate +
                                 dst_host_same_src_port_rate + is_guest_login + flag + 
                                 wrong_fragment,
                               data = ids_train, act.fct = "logistic")
```

Nah ini juga sebenernya sama dengan step sebelumnya, hanya beda activation function saja, disini menggunakan activation function sigmoid

# Visualisasikan topologi jaringan dengan aktivasi sigmoid
```
plot(ids_model_sigmoid)
```
![WhatsApp Image 2023-12-22 at 22 22 49_19b5e6de](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/78aac82b-e907-4cf3-b28c-3d72aef09a9c)

# Dapatkan hasil model dan prediksi nilai label untuk aktivasi sigmoid
```
model_results_sigmoid <- compute(ids_model_sigmoid, ids_test[1:41])
predicted_labels_sigmoid <- model_results_sigmoid$net.result
```
# Periksa korelasi antara nilai prediksi dan aktual untuk aktivasi sigmoid
```
cor_sigmoid <- cor(predicted_labels_sigmoid, ids_test$labels)
print(cor_sigmoid)
```
![WhatsApp Image 2023-12-25 at 12 06 55_ee4fa16c](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/5166c962-66dd-4f3a-a860-59098f7fa53d)

# Latih model neuralnet dengan aktivasi tanh
```
ids_model_tanh <- neuralnet(formula = labels ~ srv_count + dst_host_diff_srv_rate +
                              dst_host_same_src_port_rate + is_guest_login + flag + 
                              wrong_fragment,
                            data = ids_train, act.fct = "tanh")
```

# Visualisasikan topologi jaringan dengan aktivasi tanh
```
plot(ids_model_tanh)
```
![WhatsApp Image 2023-12-22 at 22 22 49_d5d63c8f](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/ef44ef7e-bcc8-4965-ae9c-4500f77b7eb3)

# Dapatkan hasil model dan prediksi nilai label untuk aktivasi tanh
```
model_results_tanh <- compute(ids_model_tanh, ids_test[1:41])
predicted_labels_tanh <- model_results_tanh$net.result
```
# Periksa korelasi antara nilai prediksi dan nilai aktual untuk aktivasi tanh
```
cor_tanh <- cor(predicted_labels_tanh, ids_test$labels)
print(cor_tanh)
```
![WhatsApp Image 2023-12-25 at 12 07 08_31b709e6](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/64cb5121-6308-4bc8-b2c7-c25599016c45)

# Latih model sigmoid dengan aktivasi ReLu
```
install.packages("sigmoid")
library("sigmoid")
```
Karena tidak ada activation function ReLu di packages neuralnet, maka kita harus install packages sigmoid dulu agar library neuralnet bisa pakai activation function ReLu

# Latih model neuralnet dengan aktivasi ReLu
```
ids_model_relu <- neuralnet(formula = labels ~ srv_count + dst_host_diff_srv_rate +
                              dst_host_same_src_port_rate + is_guest_login + flag + 
                              wrong_fragment,
                            data = ids_train, act.fct = relu)
```

# Visualisasikan topologi jaringan dengan aktivasi ReLu
```
plot(ids_model_relu)
```
# Dapatkan hasil model dan prediksi nilai label untuk aktivasi ReLu
```
model_results_relu <- compute(ids_model_relu, ids_test[1:41])
predicted_labels_relu <- model_results_relu$net.result
```
# Periksa korelasi antara nilai prediksi dan nilai aktual untuk aktivasi ReLu
```
cor_relu <- cor(predicted_labels_relu, ids_test$labels)
print(cor_relu)
```
![WhatsApp Image 2023-12-25 at 12 07 22_769bcc9c](https://github.com/walkeyzz/IDS-ANALYSIS/assets/146419451/446138ef-0965-4c76-af26-93a7b32afe8d)

