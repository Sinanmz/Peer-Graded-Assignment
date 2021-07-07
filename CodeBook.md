### This file explains all the steps that was performed in the run_analysis.R to get from the raw data to the cleaned-up data in the FinalData.txt.


1. **Reading the data and assigning variable names to them:**

```
features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))
activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")
x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")
```
These lines of code basically read the raw datasets and assign appropriate variable names to their columns.


2. **Merging the training and test datasets to create one single dataset:**

```
x <- rbind(x_train, x_test)
y <- rbind(y_train, y_test)
subject <- rbind (subject_train, subject_test)
mergeddata <- cbind(x, y, subject)
tidy <- mergeddata %>% select(subject, code, contains("mean"), contains("std"))

```
These lines of code merge the datasets and create a dataset called "mergeddata" and then by selecting the appropriate columns we get to a tidyer dataset called "tidy".


3. **Adding descriptive names to the activities and dataset columns:**

```
tidy[, 2] <- gsub("1", "walking", tidy[, 2])
tidy[, 2] <- gsub("2", "walking_upstairs", tidy[, 2])
tidy[, 2] <- gsub("3", "walking_downstairs", tidy[, 2])
tidy[, 2] <- gsub("4", "sitting", tidy[, 2])
tidy[, 2] <- gsub("5", "standing", tidy[, 2])
tidy[, 2] <- gsub("6", "laying", tidy[, 2])

names(tidy)[2] = "activity"
names(tidy)<-gsub("Acc", "Accelerometer", names(tidy))
names(tidy)<-gsub("Gyro", "Gyroscope", names(tidy))
names(tidy)<-gsub("BodyBody", "Body", names(tidy))
names(tidy)<-gsub("Mag", "Magnitude", names(tidy))
names(tidy)<-gsub("^t", "Time", names(tidy))
names(tidy)<-gsub("^f", "Frequency", names(tidy))
names(tidy)<-gsub("tBody", "TimeBody", names(tidy))
names(tidy)<-gsub("-mean()", "Mean", names(tidy), ignore.case = TRUE)
names(tidy)<-gsub("-std()", "STD", names(tidy), ignore.case = TRUE)
names(tidy)<-gsub("-freq()", "Frequency", names(tidy), ignore.case = TRUE)
names(tidy)<-gsub("angle", "Angle", names(tidy))
names(tidy)<-gsub("gravity", "Gravity", names(tidy))

```
These lines of code add descriptive names to the dataset using the gsub() function.


4. **Averaging each variable in the latest dataset and exporting the final table as a text file:**

```
FinalData <- tidy %>%
  group_by(subject, activity) %>%
  summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)

```
