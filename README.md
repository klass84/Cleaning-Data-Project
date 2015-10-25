---
# Cleaning data project
---

setwd("C:/Users/Claudiokass/Desktop/R/UCI HAR Dataset")

## Part 1: Merges the training and the test sets to create one data set

The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.
so lets take X (Training/Test set)....

```{r}
xtrain<-read.table("X_train.txt")
xtest<-read.table("X_test.txt")
x<-rbind(xtrain,xtest)
```

.... and Y data (Training/Test label)

```{r}
ytrain<-read.table("Y_train.txt")
ytest<-read.table("Y_test.txt")
y<-rbind(ytrain,ytest)
```

'train/subject_train.txt': Each row identifies the subject who
'performed the activity for each window sample.

```{r}
subtrain <- read.table("subject_train.txt")
subtest <- read.table("subject_test.txt")
sub<-rbind(subtrain,subtest)

#Group all the data togheter by column: x[10299,564], y[10299,1],sub[10299,1]

allin<-cbind(x,y,sub)
```

'features.txt': List of all features. Then names all the columns of allin data

```{r}
features<-read.table("features.txt")

for(i in 1:nrow(features)){
  colnames(allin)[i]<-as.character(features[i,2])}
```


# Part 2: Extracts only the measurements on the mean and standard deviation for each measurement.

Let's get columns in allin data with mean or sd mentioned there

```{r}
meanstdata<-allin[grep(("mean|std"),colnames(allin), ignore.case = T)]
complete<-cbind(meanstdata,y,sub)
colnames(complete)[ncol(meanstdata)+c(1,2)]<-c("Activity_id","Subject")
```

# Part 3: Uses descriptive activity names to name the activities in the data set

'activity_labels.txt': Links the activity id with activity names with their activity name.
```{r}
actlab<-read.table("activity_labels.txt")
complete<-merge(complete,actlab,by.x="Activity_id",by.y="V1",all=T)
```

# Part 4: Appropriately labels the data set with descriptive variable names.

```{r}
colnames(complete)[ncol(complete)]<-"Activity_name"
```

# Part 5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject
```{r}
Complete_Data <-aggregate(.~ Activity_name+Subject,complete,mean)
write.table(Complete_Data, file = "Complete_Data.txt",row.name=FALSE)
```