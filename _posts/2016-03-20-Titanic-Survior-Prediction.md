---
layout: post
title: "[Analytics] 타이타닉 생존자 예측"
---
## 개요
Kaggle의 Competitions는 어떤 주제에 대해 서로의 분석 및 예측 능력을 선보이는 곳이다. 그 중 101 섹션은 초심자들이 쉽게 도전할 수 있도록 주제가 비교적 간단하며 데이터셋 또한 그리 복잡하지 않다. 이번에 소개할 주제는 101 섹션에 있는 ["Titanic: Machine Learning from Diaster"](https://www.kaggle.com/c/titanic) 이며, 여러가지 도구를 활용하여 타이타닉 생존자를 예측하는 것을 목적으로 한다. 이미 이 주제에 대해 수 많은 답들이 나왔고 그 중에서 가장 많은 표를 받은 [Decision Tree Visualization & Submission
](https://www.kaggle.com/yildirimarda/titanic/titanic-test3)을 분석해보려고 한다.

## 데이터셋
kaggle에서는 train과 test 데이터셋을 제공하며 데이터셋 내에 각 변수는 다음과 같은 의미를 갖는다.
```
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
```


