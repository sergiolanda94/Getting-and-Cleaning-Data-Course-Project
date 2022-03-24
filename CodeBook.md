 #In this code book I describe the variables, the data, and any transformations or work that 

#you performed to clean up the data

 
 #In this first part I created the variables I used to clean up the data
 
 #I also assigned names to the columns of each variable for easier cleaning used in step 2

features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))

activity <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))


subjectTest <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

xtest <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)

ytest <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")



subjectTrain <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")

xtrain <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)

ytrain <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")


#Step 1: Merge the training and the test sets to create one data set.

#For merging the data sets I created three variables and rbinded them

#Finally I Merged all three of the variables I created

x <- rbind(xtrain, xtest)

y <- rbind(ytrain, ytest)

Subject <- rbind(subjectTrain, subjectTest)

MergedData <- cbind(Subject, x, y)

#Step 2: Extract only the measurements on the mean and standard deviation for each measurement.

#Here I create the "CleanData" dataset in which I used the names I assigned to the columns for easier analysis.

CleanData <- MergedData %>% select(subject, code, contains("mean"), contains("std"))


#Step 3: Use descriptive activity names to name the activities in the data set.

CleanData$code <- activities[CleanData$code, 2]


#Step 4: Appropriately label the data set with descriptive variable names.

#I changed the names of the data sets for more descriptive names.

names(CleanData)[2] = "activity"

names(CleanData)<-gsub("Acc", "Accelerometer", names(CleanData))

names(CleanData)<-gsub("Gyro", "Gyroscope", names(CleanData))

names(CleanData)<-gsub("BodyBody", "Body", names(CleanData))

names(CleanData)<-gsub("Mag", "Magnitude", names(CleanData))

names(CleanData)<-gsub("^t", "Time", names(CleanData))

names(CleanData)<-gsub("^f", "Frequency", names(CleanData))

names(CleanData)<-gsub("tBody", "TimeBody", names(CleanData))

names(CleanData)<-gsub("-mean()", "Mean", names(CleanData), ignore.case = TRUE)

names(CleanData)<-gsub("-std()", "STD", names(CleanData), ignore.case = TRUE)

names(CleanData)<-gsub("-freq()", "Frequency", names(CleanData), ignore.case = TRUE)

names(CleanData)<-gsub("angle", "Angle", names(CleanData))

names(CleanData)<-gsub("gravity", "Gravity", names(CleanData))



#Step 5: From the data set in step 4, create a second, independent tidy data set with the average of each variable for each activity and each subject.

#Using dplyr I made just that

AvgData <- CleanData %>%
  group_by(subject, activity) %>%
  summarise_all(funs(mean))
