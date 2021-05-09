# Github-code
library(dplyr)


X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
Y_train <- read.table("./UCI HAR Dataset/train/Y_train.txt")
Sub_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")


X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
Y_test <- read.table("./UCI HAR Dataset/test/Y_test.txt")
Sub_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")

variable_names <- read.table("./UCI HAR Dataset/features.txt")
activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")

#1. Merging

X_total <- rbind(X_train, X_test)
Y_total <- rbind(Y_train, Y_test)
Sub_total <- rbind(Sub_train, Sub_test)

#2. Filtering Variables

selected_variable <- variable_names[grep("mean|std",variable_names[,2]),]
selected_variable[,1]
X_total <- X_total[,selected_variable[,1]]


#3. Labelling Activities
colnames(Y_total) <- "activity"
Y_total$activitylabel <- factor(Y_total$activity, labels = as.character(activity_labels[,2]))
activitylabel <- Y_total[,-1]
  

#4. Labelling Variables

colnames(X_total) <- variable_names[selected_variable[,1],2]

#5. Average for each subject and activity

colnames(Sub_total) <- "subject"
total <- cbind(X_total, activitylabel, Sub_total)
total <- total[,-80]
total_mean <- total %>% 
  group_by(activitylabel, subject) %>%
  summarize_each(funs(mean))

write.table(total_mean, file = "./UCI HAR Dataset/tidydata.txt", row.names = FALSE, col.names = TRUE)
