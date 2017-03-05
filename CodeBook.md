#load packages
library("dplyr")
library("data.table")


# assign the weblink for data to variable url
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

#download the zip file into current working directory

download.file(url, destfile = "Dataset.zip")

#Unizip the downloaded dataset

unzip("Dataset.zip")

#set path to unzipped folder

setwd("~./UCI HAR Dataset")

#Load train data
data_train <- read.table("~./UCI HAR Dataset/train/X_train.txt")
activity_train <- read.table("~./UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("~./UCI HAR Dataset/train/subject_train.txt")


#Load and view test data
data_test <- read.table("~./UCI HAR Dataset/test/X_test.txt")
activity_test <- read.table("~./UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("~./UCI HAR Dataset/test/subject_test.txt")

#Load and view features data
features <- read.table("~./UCI HAR Dataset/features.txt")
features <- as.character(features[,2])

#Merge the  test and train datasets by rows
all_data <- rbind(data_train, data_test)
all_activity_data <- rbind(activity_train, activity_test)
all_subject_data <- rbind(subject_train, subject_test)

#read activity lables

activity_lables <- read.table("~./UCI HAR Dataset/activity_labels.txt")

#Set Names
names(all_activity_data) <- paste("Activity_Num")
names(all_subject_data) <- paste("Subject")
colnames(all_data) <- features
colnames(activity_lables) <- c("activity_num", "activity_name")

#Merge Columns

data <- cbind(cbind(all_subject_data,all_activity_data),all_data)

#Remove duplicate column names
data <- data[ , !duplicated(colnames(data))]

#Select data 

select(data,matches("mean\\(\\)|std\\(\\)"))

#Rename columns with descriptive names
names(data)<-gsub("^t", "time", names(data))
names(data)<-gsub("^f", "frequency", names(data))
names(data)<-gsub("Acc", "Accelerometer", names(data))
names(data)<-gsub("Gyro", "Gyroscope", names(data))
names(data)<-gsub("Mag", "Magnitude", names(data))
names(data)<-gsub("BodyBody", "Body", names(data))

#write tidy dataset to tidy_data.txt

write.table(data, file = "tidy_data.txt",row.name=FALSE)
