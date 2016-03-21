---
layout: post
title: "[Analytics] 타이타닉 생존자 예측"
---
## 개요
Kaggle의 Competitions는 어떤 주제에 대해 서로의 분석 및 예측 능력을 선보이는 곳이다. 그 중 101 섹션은 초심자들이 쉽게 도전할 수 있도록 주제가 비교적 간단하며 데이터셋 또한 그리 복잡하지 않다. 이번에 소개할 주제는 101 섹션에 있는 ["Titanic: Machine Learning from Diaster"](https://www.kaggle.com/c/titanic) 이며, 여러가지 도구를 활용하여 타이타닉 생존자를 예측하는 것을 목적으로 한다. 이미 이 주제에 대해 수 많은 답들이 나왔고 그 중에서 가장 많은 표를 받은 [Decision Tree Visualization & Submission
](https://www.kaggle.com/yildirimarda/titanic/titanic-test3)을 분석해보려고 한다.

## 데이터셋
kaggle에서는 train과 test 데이터셋을 제공하며 데이터셋 내에 각 변수는 다음과 같은 의미를 갖는다.

~~~ r
survival        Survival
                (0 = No; 1 = Yes)
pclass          Passenger Class
                (1 = 1st; 2 = 2nd; 3 = 3rd)
name            Name
sex             Sex
age             Age
sibsp           Number of Siblings/Spouses Aboard
parch           Number of Parents/Children Aboard
ticket          Ticket Number
fare            Passenger Fare
cabin           Cabin
embarked        Port of Embarkation
                (C = Cherbourg; Q = Queenstown; S = Southampton)
~~~

## 솔루션 분석
우선 decision tree에 사용하는 rpart 라이브러리를 로드하고 train/test 데이터셋을 읽어들인다.

~~~ r
### LIBRARY CALL ###
library("rpart")
library("rpart.plot")

### READING DATA ###
train <- read.csv("./train.csv")
test  <- read.csv("./test.csv")
test$Survived <- 0
~~~

다음으로 데이터 클린징 작업이 필요한데, train 데이터에서 곳곳에 비어있는 데이터를 채워주거나 기존 데이터에서 파생된 데이터를 얻고자 하는 작업도 한다. 원본 데이터가 항상 깨끗한 상태는 아니기 때문에 데이터 분석 전에 데이터를 정제하는 작업은 필수적이다.

~~~ r
### CLEANING DATA ###
combi <- rbind(train, test)
combi$Name <- as.character(combi$Name)
strsplit(combi$Name[1], split='[,.]')
strsplit(combi$Name[1], split='[,.]')[[1]]
strsplit(combi$Name[1], split='[,.]')[[1]][2]
combi$Title <- sapply(combi$Name, FUN=function(x) {strsplit(x, split='[,.]')[[1]][2]})
combi$Title <- sub(' ', '', combi$Title)
combi$Title[combi$PassengerId == 797] <- 'Mrs' # female doctor
combi$Title[combi$Title %in% c('Lady', 'the Countess', 'Mlle', 'Mee', 'Ms')] <- 'Miss'
combi$Title[combi$Title %in% c('Capt', 'Don', 'Major', 'Sir', 'Col', 'Jonkheer', 'Rev', 'Dr', 'Master')] <- 'Mr'
combi$Title[combi$Title %in% c('Dona')] <- 'Mrs'
combi$Title <- factor(combi$Title)

# 62, 830번째 승객은 승선지가 없어 가장 많은 사람들이 승선한 Southampton을 승선지로 넣는다.
combi$Embarked[c(62,830)] = "S"
combi$Embarked <- factor(combi$Embarked)

# 1044번째 승객은 승선료가 없어 median 값으로 넣는다.
combi$Fare[1044] <- median(combi$Fare, na.rm=TRUE)

# family_size라는 컬럼을 생성한다.
combi$family_size <- combi$SibSp + combi$Parch + 1

# 나이는 없는 경우를 위해 Pclass, Sex, SibSp, Parch, Fare, Embarked, Title, family_size를 기반으로 나이를 유추한다.
predicted_age <- rpart(Age ~ Pclass + Sex + SibSp + Parch + Fare + Embarked + Title + family_size,
                       data=combi[!is.na(combi$Age),], method="anova")
combi$Age[is.na(combi$Age)] <- predict(predicted_age, combi[is.na(combi$Age),])
~~~


