setwd("C:/Users/Hand 07/Desktop/Curso/Curso_3/Week_4")

arquivo <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

download.file(arquivo, destfile = "./arquivozip.zip", method="curl")

unzip(zipfile = "./arquivozip.zip", exdir = "./Pasta1")


path_rf <- file.path("./Pasta1" , "UCI HAR Dataset")

files<-list.files(path_rf, recursive=TRUE)


features <- read.csv("./Pasta1/UCI HAR Dataset/features.txt", header = FALSE, sep = ' ')

features <- as.character(features[,2])


data.test.x <- read.table("./Pasta1/UCI HAR Dataset/test/X_test.txt")
data.test.y <- read.table("./Pasta1/UCI HAR Dataset/test/y_test.txt")
data.test.subject <- read.table("./Pasta1/UCI HAR Dataset/test/subject_test.txt")

data.test <-  data.frame(data.test.subject, data.test.y, data.test.x)
names(data.test) <- c(c('subject', 'activity'), features)


data.train.x <- read.table("./Pasta1/UCI HAR Dataset/train/X_train.txt")
data.train.y <- read.table("./Pasta1/UCI HAR Dataset/train/y_train.txt")
data.train.subject <- read.table("./Pasta1/UCI HAR Dataset/train/subject_train.txt")

data.train <-  data.frame(data.train.subject, data.train.y, data.train.x)
names(data.train) <- c(c('subject', 'activity'), features)

# Merge
data.trainetest <- rbind (data.train, data.test)


# Mean and std selection
media_desvpad <- grep('mean|std', features)

data.myexercise <- data.trainetest[,c(1,2,media_desvpad  + 2)]

#Name

activity.names <- read.table("./Pasta1/UCI HAR Dataset/activity_labels.txt", header = FALSE)
activity.names <- as.character(activity.names[,2])
data.myexercise$activity <- activity.names[data.myexercise$activity]



# Name for eaach activity
newname <- names(data.myexercise)
newname <- gsub("[(][)]", "", newname)
newname <- gsub("^t", "TimeDomain_", newname)
newname <- gsub("^f", "FrequencyDomain_", newname)
newname <- gsub("Acc", "Accelerometer", newname)
newname <- gsub("Gyro", "Gyroscope", newname)
newname <- gsub("Mag", "Magnitude", newname)
newname <- gsub("-mean-", "_Mean_", newname)
newname<- gsub("-std-", "_StandardDeviation_",newname)
newname <- gsub("-", "_", newname)
names(data.myexercise) <- newname



data.tidy <- aggregate(data.myexercise[,3:81], by = list(activity = data.myexercise$activity, subject = data.myexercise$subject),FUN = mean)
write.table(x = data.tidy, file = "data_tidy.txt", row.names = FALSE)




