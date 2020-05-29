# Telecom-Churn

Telecom operators throughout the world grapple with the problem of Subscriber Churning to Competitors. Usually, subscriber do not take such
decisions all of a sudden. Repetitive Network issues, Billing complaints usually lead to build up of frustration and this behavior also shows
up in usage pattern. In this exercise , we ask, whether it is possible to detect early signs such patterns, so that evasive action can be 
taken before the point of no return. Typically, there are two type of churns, __Revenue Based and Usage Based__. In this use case we explore
the latter and keep ourselves limited to __high values customers__.

### 1. The Dataset

The dataset has 96000 rows with 226 Features. Right at the beginning, we notice that we have only 10% positive cases ( of churn ). So this
is a highly imbalanced problem. 
Each row represents a customer and the features represent some metric for months numbered as 6,7,8 and 9. For example __Recharge Amount__ for months
6,7,8 and 9.

__High Valued Customers__

High-value customers are those, who have recharged with an amount more than or equal to 70th percentile of the average recharge amount
in the first two months (we call this the good phase).

__Class Labels__

We consider a positive class as those subscribers in Month 9, who have zero calls and data. After this, other features for Month 9 are 
irrelevant.

### 2. Derived Features

We are now left with a large number of features for months 6,7 and 8. However, we notice that many features can be merged into one.
For example Outgoing Minutes of Usage is a sum of Mobile to Mobile in own and other networks, Mobile to Fixed etc. This allowed us to derive a 
lot of features and reduce the feature count to a mere 31. We then use __RFE__ to reduce the feature count to 15. At this point we 
also calculate __Variance Inflation Factor__ and the maximum value is 2.3. This is quite reasonable.

### 3. Model Building

We start by building a baseline Logistic Regression Model on these fetaures and hit an __AUC__ of __0.86__. Our Test Set __Recall__ at this stage is __79%__.
We then moved to Ensemble Methods, starting with Random Forest. At this stage we make a few choices:

- Random Forest on the top RFE Features
- Random Forest using all the features
- Gradient Boost on the top 15 features recommended by Random Forest
- XGBoost on the same top 15 features.

With XGBoost , we were able to reach an AUC for 0.892

### 4. Conclusion

The entire exercise shows us that, if a certain feature shows a sharp decline in Month-8 compared to average of Month 6 & &, that's a
cause of concern. From a business perspective , if there is a sharp decline in the following metrics, then its a call for action.

- __Local,STD Incoming Minutes of Usage:__ It seems, if there's a sharp decline , it most probably indicates that the subscriber has moved to a new number and has shared the new number to his contacts.
- __Local, STD, Roaming Outgoing Minutes of Usage:__ Similarly, if the user has started using another operator, there's no reason why he would keep making outgoing calls.
- __Onnet-Offnet Minutes of Usage:__ A decline in above factors will obviously affect the Onnet/Offnet Usage. This is just a euphemism for saying that the subscriber has stopped using the current number.
- __ARPU:__ This is relatively simple to understand. The subscriber stopping revenue generating activity is a clear indication of him not using the services.
- __Total, Maximum Recharge and Last Date recharge Amount:__ If a subscriber carries on his usual usage pattern, there is no reason for a sharp decline here in the action phase. However, if he his recharge pattern shows a sharp decline, its a caue of concern.
- __Age on Network ( AON ):__ It seems that new users are more prone to changing the service provider. This is probably because , old users find it harder to update their contacts. That's why their stickiness is higher. This is a big insight, indicating that new users must be made to stick to their current number for a certain period to ensure their loyalty. That probably explains operators giving away free Amazon Prime/ Netflix services for new users.
- __Recahrge Habit Data:__ Please recall, this fetaure is sum of thre weights 1,2 and 3. If a subscriber recharges all three months, this will be 1+2+3=6. Whereas, if he recharges in only Jun and July, then it would be 1+2+0=5. A figure less than 2 indicates that he hasn't recharges even once in Aug. This is an indicator of the fact, that the subscriber has probably lost interest.
