# IDS-ANALYSIS
This is an assignment from the DAP Project by Keiko and Marshanda 3ITE1

# Menyiapkan Data
```
ids <- read.csv("C:/Users/ASUS/Downloads/ids (1).csv", sep=";", stringsAsFactors=TRUE)
View(ids)
```
# Transformasi dan Persiapan Data

str(ids)

##-----handle missing value------

ids[is.na(ids)] <- 0
ids[is.na(ids)]
ids <- ids[-22545, ]
ids <- as.data.frame(ids)
any(is.na(ids))
sum(is.na(ids))
colSums(is.na(ids))
testingids <- na.omit(ids)
testingids <- as.data.frame(testingids)

##------merubah nama labels(serangan) dari angka menjadi nama-----

testingids$labels <- factor(testingids$labels, levels = c("1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", "12", "13", "14", "15", "16", "17", "18", "19", "20", "21", "22", "23", "24", "25", "26", "27", "28", "29", "30", "31", "32", "33", "34", "35", "36"), labels = c("apache2", "back", "buffer_overflow", "ftp_write", "guess_passwd", "httptunnel", "imap", "ipsweep", "land", "loadmodule", "mailbomb", "mscan", "multihop", "named", "neptune", "nmap", "normal", "perl", "phf", "pod", "portsweep", "processtable", "ps", "rootkit", "saint", "satan", "sendmail", "smurf", "snmgetattack", "snmpguess", "teardrop", "warezclient", "warezmaster", "xlock", "xsnoop", "xterm"))
testingids$labels

##-----buat kolom label baru yang 5 kategori serangan-----

testingids$labels2 <- NA

##------Mengelompokkan variabel sesuai dengan kategori yang diinginka-----

testingids$labels2[testingids$labels %in% c("normal")] <- "normal"
testingids$labels2[testingids$labels %in% c("back", "land", "pod", "smurf", "teardropdrop", "apache2", "udpstorm", "processtable", "mailbomb", "neptune")] <- "Dos"
testingids$labels2[testingids$labels %in% c("satan", "ipsweep", "nmap", "portsweep", "mscan", "saint")] <- "Probe"
testingids$labels2[testingids$labels %in% c("guess_passwd", "ftp_write", "imap", "phf", "multihop", "warezmaster", "xlock", "xsnoop", "snmpguess", "snmpgetattack", "httptunnel", "sendmail", "named")] <- "R2L"
testingids$labels2[testingids$labels %in% c("buffer_overflow", "loadmodule", "rootkit", "perl", "sqlattack", "xterm", "ps")] <- "U2R"

##-----normalisasi min-max-----

summary(testingids)
head(testingids)

##-----visualisasi sebaran data per variabel-----

numeric_columns <- testingids[, sapply(testingids, is.numeric)]
numeric_columns
boxplot(numeric_columns)

##-----min-max normalization-----

max = apply(numeric_columns , 2 , max)
max
min = apply(numeric_columns, 2 , min)
min
testingids2 = as.data.frame(scale(numeric_columns, center = min, scale = max - min))
head(testingids2)
boxplot(testingids2)
summary(testingids2)

#-----Menyatukan kembali dengan kolom "labels"-----

testingids3 <- cbind(testingids[c("labels","labels2")], testingids2)

#-----Menampilkan hasil-----

print(testingids3)
ids_train <- testingids3[1:15781, ]
ids_test <- testingids3[15782:22544, ]
round(prop.table(table(testingids3$labels2)) * 100, digits = 1)

#--------------------Langkah 3 - Analisis Naive Bayes---------------

##-----mulai analisis naive bayes-----

library(e1071)
install.packages("caret")
library(caret)
modelNB = naiveBayes(labels2~. , data=ids_train)

##-----melakukan prediksi-----

prediksi = predict(modelNB,ids_test)
prediksi

##-----evaluasi-----

hasil = confusionMatrix(table(prediksi,ids_test$labels2))
hasil
