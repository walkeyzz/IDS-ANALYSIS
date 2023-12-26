##---------Langkah 1 - Mengumpulkan Data---------------
ids <- read.csv("C:/Users/ASUS/Downloads/ids lama.csv", sep=";", stringsAsFactors=TRUE)

View(ids)
##---------Langkah 2 - Transformasi dan Persiapan Data---------------

str(ids)

##---------Langkah 2.1 Handle missing value------

ids[is.na(ids)] <- 0
ids[is.na(ids)]
ids <- ids[-22545, ]
ids <- as.data.frame(ids)
any(is.na(ids))
sum(is.na(ids))
colSums(is.na(ids))
testingids <- na.omit(ids)
testingids <- as.data.frame(testingids)


##--------Langkah 2.2 Normalisasi min-max----------------

summary(testingids)
head(testingids)

##visualisasi sebaran data per variabel
numeric_columns <- testingids[, sapply(testingids, is.numeric)]
numeric_columns
boxplot(numeric_columns)

##min-max normalization
max = apply(numeric_columns , 2 , max)
max
min = apply(numeric_columns, 2 , min)
min
testingids2 = as.data.frame(scale(numeric_columns, center = min, scale = max - min))
head(testingids2)
boxplot(testingids2)
summary(testingids2)
round(prop.table(table(testingids2$labels)) * 100, digits = 1)

#--------Langkah 2.3 CORRELATION------------------------------
Faktor_Berpengaruh <- cor(testingids2[c("duration", "protocol_type", "service", "flag", "src_bytes", 
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

# Ambil variabel dengan korelasi tertinggi
top_six_variables <- c("flag", "srv_count", "diff_srv_rate", "dst_host_diff_srv_rate", "dst_host_same_src_port_rate", "dst_host_srv_diff_host_rate")

# Buat model regresi hanya dengan variabel yang terpilih
model <- lm(labels ~ ., data = ids[, c(top_six_variables, "labels")])

# Tampilkan ringkasan model
summary(model)


##---------Langkah 3 - Mulai analisis k-means---------------

install.packages("factoextra")
library(factoextra)
library(cluster)


# Menghapus kolom-kolom dengan variasi nol atau sangat rendah
testingids3 <- testingids3[, apply(testingids3, 2, var) != 0]

#metode elbow-melihat cluster optimum untuk nilai k nanti
fviz_nbclust(testingids3,kmeans,method = "wss")

#metode silhoette
fviz_nbclust(testingids3,kmeans,method = "silhouette")

#metode gapstat
fviz_nbclust(testingids3,kmeans,method = "gap_stat")

#membentuk cluster (sesuaikan dengan cluster optimum)
cluster <- kmeans(testingids3,5)

#visualisasi cluster
fviz_cluster(cluster,testingids3) 

#interpretasi dan penamaan cluster
cluster$centers

k5 = kmeans(testingids3, centers = 5, nstart = 25)

final=data.frame(numeric_columns, k5$cluster)

View(final)

fviz_cluster(k3, data = testingids3)

#baru nnti tentuin sendiri nama clusternya yang 5 kategori itu


##---------------------------------------------------------------------------

#-------Jumlah kluster yang diinginkan
cluster <- 5

#-------Nomor awal kluster
c_initial <- c(0,1,2,3,4)

#-------Inisialisasi centroid
centroid <- testingids3[1:cluster,]

#-------Data frame untuk centroid awal
cent_DF <- data.frame(c = c_initial, centroid)

#-------Menampilkan Hasil
print (centroid)
print (cent_DF)

#-------Inisialisasi matriks d dan c
d <- matrix(0, nrow = nrow(testingids3), ncol = cluster)
c <- matrix(0, nrow = nrow(testingids3), ncol = 1)
d

#-------Matriks untuk centroid yang diperbarui
updCentroid <- matrix(0, nrow = nrow(centroid), ncol = ncol(testingids3))

#-------Status untuk mengontrol loop
status <- 10
#-------Iterasi awal
iter <- 0
#-------Data frame dari test3
df <- data.frame(testingids3)
updCentroid

#bikin inisial plot/plot awal pake ggplot2
initPlot <- ggplot(df,aes(srv_count, service)) +
  geom_point(size=3)+ geom_point(data=cent_DF, color=c("red","yellow", "green", "pink", "purple"), size=10)
initPlot
ggsave(initPlot, file = "IDS Test, 5 Cluster - initial.png", width = 40, height = 20, unit = "cm")

#bikin inisial plot/plot awal pake ggplot2
initPlot <- ggplot(df,aes(srv_count, service)) +
  geom_point(size=3)+ geom_point(data=cent_DF, color=c("red","yellow", "green", "pink", "purple"), size=10)
initPlot
ggsave(initPlot, file = "IDS Test, 5 Cluster - initial.png", width = 40, height = 20, unit = "cm")

while (status != 0)
{
  iter <- iter + 1
  
  fn <- paste("IDS Test,", cluster, "Cluster - iter", iter, ".png", sep = " ")
  
  # Menghitung jarak setiap data ke setiap centroid
  for (j in 1:cluster)
  {
    for (i in 1:nrow(testingids3))
    {
      d[i,j] = sqrt(sum((testingids3[i,1:ncol(testingids3)] - centroid[j,1:ncol(centroid)])^2))
    }
  }
  
  # Menentukan nomor kluster untuk setiap data
  for(i in 1:nrow(d)){ 
    c[i] <- (which(d[i,] == min(d[i,]), arr.ind = T)) - 1
  }
  
  # Plot hasil klasterisasi
  df <- data.frame(c,testingids3)
  cent_DF <- data.frame(c = c_initial, centroid)
  # gg <- merge(df,aggregate(cbind(mean.dst_host_diff_srv_rate=dst_host_diff_srv_rate, mean.V2=V2)~c , df, mean), by="c")
  gg <- merge(df, cent_DF, by="c")
  
  plot <- ggplot(gg, aes(srv_count.x, service.x, color=factor(c))) + geom_point(size=3) +
    geom_point(aes(x = srv_count.y, y = service.y),size=5) +
    geom_segment(aes(x = srv_count.y, y = service.y, xend = srv_count.x, yend = service.x))
  
  ggsave(plot, file = fn, width = 40, height = 20, units = "cm")
  
  # Menghitung centroid baru berdasarkan data yang dikelompokkan
  compare <- cbind(testingids2, c)
  
  for (i in 1:cluster)
  {
    x <- subset(compare[,1:2], compare[,3] == i-1)
    
    for(j in 1:ncol(testingids3))
    {
      updCentroid[i,j] <- mean(x[,j])
    }
  }
  
  # Memperbarui centroid saat ini
  if(all(updCentroid == centroid)){
    status = 0
  }
  else {
    st 
    } 
  }
}

#RUMUS LAIN

# Kmeans Clustering
num_clusters <- 5
kmeans_result <- kmeans(testingids, centers = num_clusters, nstart = 25)

# Menambahkan hasil clustering ke data awal
data_clustered <- cbind(data, Cluster = kmeans_result$cluster)

# Evaluasi akurasi
length(data_clustered$labels)
length(data_clustered$Cluster)

conf_matrix <- table(data_clustered$labels, data_clustered$Cluster)
cat("Confusion Matrix:\n")
print(conf_matrix)

# Hitung akurasi
accuracy <- sum(diag(conf_matrix)) / sum(conf_matrix)
cat("Accuracy:", accuracy, "\n")

# Plot hasil klasterisasi menggunakan ggplot2
ggplot(data_clustered, aes(x = srv_count, y = service, color = factor(Cluster))) +
  geom_point(size = 3) +
  ggtitle("Hasil K-Means Clustering") +
  xlab("srv_count") +
  ylab("service") +
  theme_minimal()









