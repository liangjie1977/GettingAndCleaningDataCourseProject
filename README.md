# ReadMe

### Files
- data_tidy.txt : the submitted tidy dataset for course project Getting and Cleaning Data.
- CodeBook.md : explains the variables in the data_tidy dataset.
- run_analysis.R : the R script which doesn't the file and data operations.
- README.md : explains the data analysis process

### File operation and data analysis throught run_analysis.R
1. Download file from provided URL, then unzip it
```r
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip", "Dataset.zip")
unzip("Dataset.zip")
```

2. 
```r
#Read activity lables and features, rename the column names from "v1" and "v2" to more readable column names.
activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt")
colnames(activity_labels) <- c("activityID", "activityName")
features <- read.table("UCI HAR Dataset/features.txt")
colnames(features) <- c("featureID", "featureName")

#Read test data, activity and subject, then bind together to get test dataset.
X_test <- read.table("UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
data_test <- cbind(X_test, y_test, subject_test)

#Read train data, activity and subject, then bind together to get train dataset.
X_train <- read.table("UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
data_train <- cbind(X_train, y_train, subject_train)
```

#Merge test and train data to get a whole dataset
data_all <-rbind(data_train, data_test)

#Rename all colomn names using descriptive variable names
colnames(data_all) <- c(as.character(features$featureName), "activityID", "subject")

#Extract only the measurements on the mean and std for each measurement
#Make sure to keep activityID and subject columns
data_extracted<- data_all[, grepl("mean\\(\\)|std\\(\\)|activityID|subject", names(data_all)) ]


#Merge data_extracted with activity_labels to bring in activityName, then remove activityID from the dataframe.
#data is the result for project step 1~4.
data <- merge(data_extracted, activity_labels, by = "activityID")[-1]



#------------Step 5 ----------------------
#creating a individual tidy data set with the average of each variable for each activity and subject.

#Melt data based on activityName and subject, then cast back using mean function
data_melted <- melt(data, id = c("activityName", "subject") )
data_cast <- dcast(data_melted, activityName+subject ~ variable,fun.aggregate = mean )

#in data_cast, the variables names contain too many values incudling:
# domain : t or f (time or frenquency)
# acceleration type: body or gravity
# signal type: acc, gyro, accJerk, gyroJerk
# axis : X, Y, Z or Mag
# math method used: mean() or std()
# I reorganized column names in the same order and seperated values with "-"
data_final <- data_cast
names(data_final) <- gsub("-mean\\(\\)-X", "-X-mean\\(\\)", names(data_final))
names(data_final) <- gsub("-mean\\(\\)-Y", "-Y-mean\\(\\)", names(data_final))
names(data_final) <- gsub("-mean\\(\\)-Z", "-Z-mean\\(\\)", names(data_final))
names(data_final) <- gsub("-std\\(\\)-X", "-X-std\\(\\)", names(data_final))
names(data_final) <- gsub("-std\\(\\)-Y", "-Y-std\\(\\)", names(data_final))
names(data_final) <- gsub("-std\\(\\)-Z", "-Z-std\\(\\)", names(data_final))
names(data_final) <- gsub("Mag-", "-Mag-", names(data_final))
names(data_final) <- gsub("^t", "t-", names(data_final))
names(data_final) <- gsub("^f", "f-", names(data_final))
names(data_final) <- gsub("BodyBody", "Body", names(data_final))
names(data_final) <- gsub("-Body", "-Body-", names(data_final))
names(data_final) <- gsub("-Gravity", "-Gravity-", names(data_final))

data_melted <- melt(data_final, id = c("activityName", "subject") )

data_tidy <-separate(data_melted, col = "variable", into = c("domain", "accelerationType","signalType","axis","mathMethod"), sep = "-")



#Write data_tidy back to "data_tidy.txt"
write.table(data_tidy, "data_tidy.txt",  row.name=FALSE)
