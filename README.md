import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

!pip install quandl
import quandl

data = quandl.get("NSE/TATAGLOBAL")

plt.figure(figsize=(16,8))
plt.plot(data['Close'],label='Closing Price')

data['Open - Close'] = data['Open'] - data['Close']
data['High - Low'] = data['High'] - data['Low']
data=data.dropna()

x=data[['Open - Close', 'High - Low']]

#buy +1, sell -1
#target variable y is for classification task
y = np.where(data['Close'].shift(-1)>data['Close'],1,-1)

from sklearn.model_selection import train_test_split as tts
x_train, x_test, y_train, y_test = tts(x,y,test_size=0.25, random_state=44)

#implementation of KNN classifier
from sklearn.neighbors import KNeighborsClassifier 
from sklearn import neighbors 
from sklearn.model_selection import GridSearchCV 
from sklearn.metrics import accuracy_score 


#using gridsearch to find best parameter
params = { 'n_neighbors': [2,3,4,5,6,7,8,9,10,11,12,13,14,15]}
knn = neighbors.KNeighborsClassifier()
model = GridSearchCV(knn, params, cv=5)

#fit the model
model.fit(x_train, y_train)

#Accuracy score
accuracy_train = accuracy_score(y_train, model.predict(x_train))
accuracy_test = accuracy_score(y_test,model.predict(x_test))

print('Train_data accuracy: ', accuracy_train)
print('Test_data accuracy: ', accuracy_test)

predictions_classification = model.predict(x_test)
actual_predicted_data = pd.DataFrame({'Actual Class':y_test, 'Predicted Class': predictions_classification})

y=data['Close']

#implementation of knn regression
from sklearn.neighbors import KNeighborsRegressor
from sklearn import neighbors
import sklearn
x_train_reg, x_test_reg, y_train_reg, y_test_reg = sklearn.model_selection.train_test_split (x,y,test_size=0.25,random_state=44)

#using gridsearch to find the best parameter
params = {'n_neighbors': [2,3,4,5,6,7,8,9,10,11,12,13,14,15]}
knn_reg=neighbors.KNeighborsRegressor()
model_reg = GridSearchCV(knn_reg, params, cv=5)

#fit the model and make predictions
model_reg.fit(x_train_reg, y_train_reg)
predictions = model_reg.predict(x_test_reg)
print(predictions)

#root mean square error
rms=np.sqrt(np.mean(np.power((np.array(y_test)-np.array(predictions)), 2)))

valid  = pd.DataFrame({'Actual Close': y_test_reg, 'Predicted Close Value': predictions})

print(valid)
#plt.figure(figsize=(8,6))
#plt.plot(y_test_reg, 'g')
#plt.plot(predictions, 'r')
#plt.show()
