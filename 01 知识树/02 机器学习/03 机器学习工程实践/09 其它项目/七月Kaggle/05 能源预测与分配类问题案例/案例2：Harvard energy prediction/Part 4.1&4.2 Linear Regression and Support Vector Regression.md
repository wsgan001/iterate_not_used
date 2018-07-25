
#4 Prediction Using Different Machine Learning Methods

## 4.1 Linear Regression


In this notebook, we'll train a Linear Regression model for predicting building energy consumption based on historical enregy data, several weather variables, hour of the day, day of the week, weekends and holidays. 

To do this, we'll fit the model to daily and hourly energy and weather data from 2012-01-01 to 2014-10-31. 


```
# special IPython command to prepare the notebook for matplotlib
%matplotlib inline 
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
pd.options.display.mpl_style = 'default'
```


```
from sklearn.linear_model import LinearRegression
dailyElectricity = pd.read_excel('Data/dailyElectricityWithFeatures.xlsx')
dailyElectricity = dailyElectricity.drop('startDay', 1).drop('endDay', 1)

dailyChilledWater = pd.read_excel('Data/dailyChilledWaterWithFeatures.xlsx')
dailyChilledWater = dailyChilledWater.drop('startDay', 1).drop('endDay', 1)

dailySteam = pd.read_excel('Data/dailySteamWithFeatures.xlsx')
dailySteam = dailySteam.drop('startDay', 1).drop('endDay', 1)

hourlyElectricity = pd.read_excel('Data/hourlyElectricityWithFeatures.xlsx')
hourlyElectricity = hourlyElectricity.drop('startTime', 1).drop('endTime', 1)

hourlyChilledWater = pd.read_excel('Data/hourlyChilledWaterWithFeatures.xlsx')
hourlyChilledWater = hourlyChilledWater.drop('startTime', 1).drop('endTime', 1)

hourlySteam = pd.read_excel('Data/hourlySteamWithFeatures.xlsx')
hourlySteam = hourlySteam.drop('startTime', 1).drop('endTime', 1)

#display a dataframe
dailyElectricity.head()
```




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>electricity-kWh</th>
      <th>RH-%</th>
      <th>T-C</th>
      <th>Tdew-C</th>
      <th>pressure-mbar</th>
      <th>solarRadiation-W/m2</th>
      <th>windDirection</th>
      <th>windSpeed-m/s</th>
      <th>humidityRatio-kg/kg</th>
      <th>coolingDegrees</th>
      <th>heatingDegrees</th>
      <th>dehumidification</th>
      <th>occupancy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2012-01-01</th>
      <td> 2800.244977</td>
      <td> 76.652174</td>
      <td> 7.173913</td>
      <td>  3.073913</td>
      <td> 1004.956522</td>
      <td> 95.260870</td>
      <td> 236.086957</td>
      <td> 4.118361</td>
      <td> 0.004796</td>
      <td> 0.086957</td>
      <td>  7.826087</td>
      <td> 0</td>
      <td> 0.0</td>
    </tr>
    <tr>
      <th>2012-01-02</th>
      <td> 3168.974047</td>
      <td> 55.958333</td>
      <td> 5.833333</td>
      <td> -2.937500</td>
      <td>  994.625000</td>
      <td> 87.333333</td>
      <td> 253.750000</td>
      <td> 5.914357</td>
      <td> 0.003415</td>
      <td> 0.000000</td>
      <td>  9.166667</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-03</th>
      <td> 5194.533376</td>
      <td> 42.500000</td>
      <td>-3.208333</td>
      <td>-12.975000</td>
      <td> 1002.125000</td>
      <td> 95.708333</td>
      <td> 302.916667</td>
      <td> 6.250005</td>
      <td> 0.001327</td>
      <td> 0.000000</td>
      <td> 18.208333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-04</th>
      <td> 5354.861935</td>
      <td> 41.541667</td>
      <td>-7.083333</td>
      <td>-16.958333</td>
      <td> 1008.250000</td>
      <td> 98.750000</td>
      <td> 286.666667</td>
      <td> 5.127319</td>
      <td> 0.000890</td>
      <td> 0.000000</td>
      <td> 22.083333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-05</th>
      <td> 5496.223993</td>
      <td> 46.916667</td>
      <td>-0.583333</td>
      <td> -9.866667</td>
      <td> 1002.041667</td>
      <td> 90.750000</td>
      <td> 258.333333</td>
      <td> 5.162041</td>
      <td> 0.001746</td>
      <td> 0.000000</td>
      <td> 15.583333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
  </tbody>
</table>
</div>



## Daily Predictions

Adding new features to the dataframe: weekday, day of the year and week of the year.


```
def addDailyTimeFeatures(df):
    df['weekday'] = df.index.weekday
    df['day'] = df.index.dayofyear
    df['week'] = df.index.weekofyear
    return df
    
dailyElectricity = addDailyTimeFeatures(dailyElectricity)
dailyChilledWater = addDailyTimeFeatures(dailyChilledWater)
dailySteam = addDailyTimeFeatures(dailySteam)
```

### Daily Electricity Prediction


```
df_elect = dailyElectricity[['weekday', 'day', 'week', 'occupancy', 'electricity-kWh']]

elect_train = pd.DataFrame(data=df_elect, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
elect_test = pd.DataFrame(data=df_elect, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_elect_train = elect_train.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)
XX_elect_test = elect_test.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)

YY_elect_train = elect_train['electricity-kWh']
YY_elect_test = elect_test['electricity-kWh']
```


```
lr_elect = LinearRegression()
lr_elect.fit(XX_elect_train,YY_elect_train)

y_lr = lr_elect.predict(XX_elect_test)

print "The test score R2: ", lr_elect.score(XX_elect_test, YY_elect_test)

print "The Linear Regression coefficients are"
pd.DataFrame(zip(XX_elect_train.columns, lr_elect.coef_), columns = ['elect_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.608937488563
    The Linear Regression coefficients are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>elect_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>   weekday</td>
      <td> -125.392163</td>
    </tr>
    <tr>
      <th>1</th>
      <td>       day</td>
      <td>    0.550121</td>
    </tr>
    <tr>
      <th>2</th>
      <td>      week</td>
      <td>  -11.553215</td>
    </tr>
    <tr>
      <th>3</th>
      <td> occupancy</td>
      <td> 2830.298384</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot observed and Predicted electricity value 
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_elect_test.index, YY_elect_test, label='Observed', color='k')
plt.plot(XX_elect_test.index, y_lr, label='Predicted', color='g')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x32b713c8>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_10_1.png)



```
#Plot Observed vs. Linear Regression predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_elect_test, YY_elect_test, c='k')
plt.scatter(YY_elect_test, y_lr, c='g')
plt.xlabel('Observed Elec. Usage (kWh)')
plt.ylabel("Predicted Elec. Usage (kWh): $\hat{Y}_i$")
plt.title("Energy vs Predicted Elec.: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x365fab00>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_11_1.png)


### Daily Chilled Water Prediction


```
chilledw_train = pd.DataFrame(data=dailyChilledWater, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
chilledw_test = pd.DataFrame(data=dailyChilledWater, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_chilledw_train = chilledw_train.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)
XX_chilledw_test = chilledw_test.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)

YY_chilledw_train = chilledw_train['chilledWater-TonDays']
YY_chilledw_test = chilledw_test['chilledWater-TonDays']
```


```
lr_chilledw = LinearRegression()
lr_chilledw.fit(XX_chilledw_train,YY_chilledw_train)

print "The test score R2: ", lr_chilledw.score(XX_chilledw_test, YY_chilledw_test)

print "The Linear Regression coefficients are"
pd.DataFrame(zip(XX_chilledw_train.columns, lr_chilledw.coef_), columns = ['chilledw_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.830709188732
    The Linear Regression coefficients are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>chilledw_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0 </th>
      <td>                RH-%</td>
      <td>    0.464299</td>
    </tr>
    <tr>
      <th>1 </th>
      <td>                 T-C</td>
      <td>    6.062113</td>
    </tr>
    <tr>
      <th>2 </th>
      <td>              Tdew-C</td>
      <td>   -2.486768</td>
    </tr>
    <tr>
      <th>3 </th>
      <td>       pressure-mbar</td>
      <td>   -0.095268</td>
    </tr>
    <tr>
      <th>4 </th>
      <td> solarRadiation-W/m2</td>
      <td>    0.042885</td>
    </tr>
    <tr>
      <th>5 </th>
      <td>       windDirection</td>
      <td>   -0.025036</td>
    </tr>
    <tr>
      <th>6 </th>
      <td>       windSpeed-m/s</td>
      <td>   -1.166902</td>
    </tr>
    <tr>
      <th>7 </th>
      <td> humidityRatio-kg/kg</td>
      <td> 1673.166705</td>
    </tr>
    <tr>
      <th>8 </th>
      <td>      coolingDegrees</td>
      <td>    2.853128</td>
    </tr>
    <tr>
      <th>9 </th>
      <td>      heatingDegrees</td>
      <td>    4.421394</td>
    </tr>
    <tr>
      <th>10</th>
      <td>    dehumidification</td>
      <td> 2999.125771</td>
    </tr>
    <tr>
      <th>11</th>
      <td>           occupancy</td>
      <td>    0.571356</td>
    </tr>
    <tr>
      <th>12</th>
      <td>             weekday</td>
      <td>   -2.461900</td>
    </tr>
    <tr>
      <th>13</th>
      <td>                 day</td>
      <td>   -0.010718</td>
    </tr>
    <tr>
      <th>14</th>
      <td>                week</td>
      <td>    0.122757</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot observed and predicted electricity value 
y_lr = lr_chilledw.predict(XX_chilledw_test)
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_chilledw_test.index, YY_chilledw_test, label='Observed', color='k')
plt.plot(XX_chilledw_test.index, y_lr, label='Predicted', color='r')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x34131400>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_15_1.png)



```
#Plot observed vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_chilledw_test, YY_chilledw_test, c='k')
plt.scatter(YY_chilledw_test, y_lr, c='r')
plt.xlabel('Observed Chilled Water Usage (TonDays)')
plt.ylabel("Predicted Chilled Water Usage (TonDays): $\hat{Y}_i$")
plt.title("Observed vs Predicted Chilled Water: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x31a16c88>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_16_1.png)


### Daily Steam Prediction


```
steam_train = pd.DataFrame(data=dailySteam, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
steam_test = pd.DataFrame(data=dailySteam, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_steam_train = steam_train.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)
XX_steam_test = steam_test.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)

YY_steam_train = steam_train['steam-LBS']
YY_steam_test = steam_test['steam-LBS']
```


```
lr_steam = LinearRegression()
lr_steam.fit(XX_steam_train,YY_steam_train)

print "The test score R2: ", lr_steam.score(XX_steam_test, YY_steam_test)

print "The Linear Regression coefficients are"
pd.DataFrame(zip(XX_steam_train.columns, lr_steam.coef_), columns = ['steam_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.942276415896
    The Linear Regression coefficients are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>steam_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0 </th>
      <td>                RH-%</td>
      <td>     66.535470</td>
    </tr>
    <tr>
      <th>1 </th>
      <td>                 T-C</td>
      <td>    458.096751</td>
    </tr>
    <tr>
      <th>2 </th>
      <td>              Tdew-C</td>
      <td>   -951.521615</td>
    </tr>
    <tr>
      <th>3 </th>
      <td>       pressure-mbar</td>
      <td>    -30.891470</td>
    </tr>
    <tr>
      <th>4 </th>
      <td> solarRadiation-W/m2</td>
      <td>    -18.446292</td>
    </tr>
    <tr>
      <th>5 </th>
      <td>       windDirection</td>
      <td>     -7.828922</td>
    </tr>
    <tr>
      <th>6 </th>
      <td>       windSpeed-m/s</td>
      <td>    251.824413</td>
    </tr>
    <tr>
      <th>7 </th>
      <td> humidityRatio-kg/kg</td>
      <td> 857001.445663</td>
    </tr>
    <tr>
      <th>8 </th>
      <td>      coolingDegrees</td>
      <td>    -99.989152</td>
    </tr>
    <tr>
      <th>9 </th>
      <td>      heatingDegrees</td>
      <td>   1794.351286</td>
    </tr>
    <tr>
      <th>10</th>
      <td>    dehumidification</td>
      <td>-482120.622688</td>
    </tr>
    <tr>
      <th>11</th>
      <td>           occupancy</td>
      <td>   3150.501909</td>
    </tr>
    <tr>
      <th>12</th>
      <td>             weekday</td>
      <td>   -531.583401</td>
    </tr>
    <tr>
      <th>13</th>
      <td>                 day</td>
      <td>     -1.499061</td>
    </tr>
    <tr>
      <th>14</th>
      <td>                week</td>
      <td>    -43.000664</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot observed and predicted electricity value 
y_lr = lr_steam.predict(XX_steam_test)
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_steam_test.index, YY_steam_test, label='Observed', color='k')
plt.plot(XX_steam_test.index, y_lr, label='Predicted', color='g')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x35b66d68>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_20_1.png)



```
#Plot actual vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_steam_test, YY_steam_test, c='k')
plt.scatter(YY_steam_test, y_lr, c='g')
plt.xlabel('Observed Steam Usage (LBS)')
plt.ylabel("Predicted Steam Usage (LBS): $\hat{Y}_i$")
plt.title("Observed vs Predicted Steam: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x3429a630>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_21_1.png)


## Hourly Prediction

Adding new features to the dataframe: hour, weekday, day of the year and week of the year.


```
def addHourlyTimeFeatures(df):
    df['hour'] = df.index.hour
    df['weekday'] = df.index.weekday
    df['day'] = df.index.dayofyear
    df['week'] = df.index.weekofyear    
    return df

hourlyElectricity = addHourlyTimeFeatures(hourlyElectricity)
```

### Hourly Electricity Prediction


```
df_hourlyelect = hourlyElectricity[['hour', 'weekday', 'day', 'week', 'cosHour', 
                                                      'occupancy', 'electricity-kWh']]

hourlyelect_train = pd.DataFrame(data=df_hourlyelect, index=np.arange('2014-01-01 00:00:00', '2014-10-01 00:00:00', dtype='datetime64[h]')).dropna()
hourlyelect_test = pd.DataFrame(data=df_hourlyelect, index=np.arange('2014-10-01 00:00:00', '2014-11-01 00:00:00', dtype='datetime64[h]')).dropna()

XX_hourlyelect_train = hourlyelect_train.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)
XX_hourlyelect_test = hourlyelect_test.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)

YY_hourlyelect_train = hourlyelect_train['electricity-kWh']
YY_hourlyelect_test = hourlyelect_test['electricity-kWh']
```


```
lr_hourlyelect = LinearRegression()
lr_hourlyelect.fit(XX_hourlyelect_train,YY_hourlyelect_train)

y_hourlyelect_lr = lr_hourlyelect.predict(XX_hourlyelect_test)

print "The test score R2: ", lr_hourlyelect.score(XX_hourlyelect_test, YY_hourlyelect_test)

print "The Linear Regression coefficients are"
pd.DataFrame(zip(XX_hourlyelect_train.columns, lr_hourlyelect.coef_), columns = ['hourlyelect_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.714713369958
    The Linear Regression coefficients are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hourlyelect_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>      hour</td>
      <td>  -0.287362</td>
    </tr>
    <tr>
      <th>1</th>
      <td>   weekday</td>
      <td>  -6.995868</td>
    </tr>
    <tr>
      <th>2</th>
      <td>       day</td>
      <td>  -0.309981</td>
    </tr>
    <tr>
      <th>3</th>
      <td>      week</td>
      <td>   0.955127</td>
    </tr>
    <tr>
      <th>4</th>
      <td>   cosHour</td>
      <td> -81.049080</td>
    </tr>
    <tr>
      <th>5</th>
      <td> occupancy</td>
      <td> 114.803110</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot observed and Predicted electricity value 
fig = plt.figure(figsize=(25,7))
plt.scatter(XX_hourlyelect_test.index, YY_hourlyelect_test, label='Observed', color='k')
plt.plot(XX_hourlyelect_test.index, y_hourlyelect_lr, label='Predicted', color='r')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x3416b710>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_28_1.png)



```
#Plot Observed vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_hourlyelect_test, YY_hourlyelect_test, c='k')
plt.scatter(YY_hourlyelect_test, y_hourlyelect_lr, c='r')
plt.xlabel('Observed Elec. Usage (kWh)')
plt.ylabel("Predicted Elec. Usage (kWh): $\hat{Y}_i$")
plt.title("Energy vs Predicted Elec.: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x33ea2fd0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_29_1.png)


### Hourly Chilled Water Prediction


```
hourlychilledw_train = pd.DataFrame(data=hourlyChilledWater, index=np.arange('2014-01-01 00:00:00', '2014-09-01 00:00:00', dtype='datetime64[h]')).dropna()
hourlychilledw_test = pd.DataFrame(data=hourlyChilledWater, index=np.arange('2014-09-01 00:00:00', '2014-11-01 00:00:00', dtype='datetime64[h]')).dropna()

XX_hourlychilledw_train = hourlychilledw_train.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)
XX_hourlychilledw_test = hourlychilledw_test.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)

YY_hourlychilledw_train = hourlychilledw_train['chilledWater-TonDays']
YY_hourlychilledw_test = hourlychilledw_test['chilledWater-TonDays']
```


```
lr_hourlychilledw = LinearRegression()
lr_hourlychilledw.fit(XX_hourlychilledw_train,YY_hourlychilledw_train)

y_hourlychilledw_lr = lr_hourlychilledw.predict(XX_hourlychilledw_test)

print "The test score R2: ", lr_hourlychilledw.score(XX_hourlychilledw_test, YY_hourlychilledw_test)

print "The Linear Regression coefficients are"
pd.DataFrame(zip(XX_hourlychilledw_train.columns, lr_hourlychilledw.coef_), columns = ['hourlychilledw_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.709930521875
    The Linear Regression coefficients are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hourlychilledw_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0 </th>
      <td>                RH-%</td>
      <td>  -0.028198</td>
    </tr>
    <tr>
      <th>1 </th>
      <td>                 T-C</td>
      <td>   0.459533</td>
    </tr>
    <tr>
      <th>2 </th>
      <td>              Tdew-C</td>
      <td>   0.166999</td>
    </tr>
    <tr>
      <th>3 </th>
      <td>       pressure-mbar</td>
      <td>  -0.007099</td>
    </tr>
    <tr>
      <th>4 </th>
      <td> solarRadiation-W/m2</td>
      <td>   0.001003</td>
    </tr>
    <tr>
      <th>5 </th>
      <td>       windDirection</td>
      <td>  -0.000382</td>
    </tr>
    <tr>
      <th>6 </th>
      <td>       windSpeed-m/s</td>
      <td>   0.004837</td>
    </tr>
    <tr>
      <th>7 </th>
      <td> humidityRatio-kg/kg</td>
      <td> -91.425425</td>
    </tr>
    <tr>
      <th>8 </th>
      <td>      coolingDegrees</td>
      <td>  -0.172407</td>
    </tr>
    <tr>
      <th>9 </th>
      <td>      heatingDegrees</td>
      <td>   0.603195</td>
    </tr>
    <tr>
      <th>10</th>
      <td>    dehumidification</td>
      <td> 226.397306</td>
    </tr>
    <tr>
      <th>11</th>
      <td>           occupancy</td>
      <td>   0.483000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>             cosHour</td>
      <td>  -0.562715</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot Observed and Predicted electricity value 
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_hourlychilledw_test.index, YY_hourlychilledw_test, label='Observed', color='k')
plt.plot(XX_hourlychilledw_test.index, y_hourlychilledw_lr, label='Predicted', color='g')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x36eeeda0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_33_1.png)



```
#Plot Observed vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_hourlychilledw_test, YY_hourlychilledw_test, c='k')
plt.scatter(YY_hourlychilledw_test, y_hourlychilledw_lr, c='g')
plt.xlabel('Observed Chilled Water Usage (TonDays)')
plt.ylabel("Predicted Chilled Water Usage (TonDays): $\hat{Y}_i$")
plt.title("Observed vs Predicted Chilled Water: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x390844e0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_34_1.png)


### Hourly Steam Prediction


```
hourlysteam_train = pd.DataFrame(data=hourlySteam, index=np.arange('2012-01-01 00:00:00', '2014-02-01 00:00:00', dtype='datetime64[h]')).dropna()
hourlysteam_test = pd.DataFrame(data=hourlySteam, index=np.arange('2014-02-01 00:00:00', '2014-11-01 00:00:00', dtype='datetime64[h]')).dropna()

XX_hourlysteam_train = hourlysteam_train.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)
XX_hourlysteam_test = hourlysteam_test.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)

YY_hourlysteam_train = hourlysteam_train['steam-LBS']
YY_hourlysteam_test = hourlysteam_test['steam-LBS']
```


```
lr_hourlysteam = LinearRegression()
lr_hourlysteam.fit(XX_hourlysteam_train,YY_hourlysteam_train)

y_hourlysteam_lr = lr_hourlysteam.predict(XX_hourlysteam_test)

print "The test score R2: ", lr_hourlysteam.score(XX_hourlysteam_test, YY_hourlysteam_test)

print "The coefficients Linear Regression are"
pd.DataFrame(zip(XX_hourlysteam_train.columns, lr_hourlysteam.coef_), columns = ['hourlysteam_features', 'linearRegr_Coefficients'])
```

    The test score R2:  0.764295430491
    The coefficients Linear Regression are
    




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hourlysteam_features</th>
      <th>linearRegr_Coefficients</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0 </th>
      <td>                RH-%</td>
      <td>     5.367666</td>
    </tr>
    <tr>
      <th>1 </th>
      <td>                 T-C</td>
      <td>     8.577206</td>
    </tr>
    <tr>
      <th>2 </th>
      <td>              Tdew-C</td>
      <td>   -54.743326</td>
    </tr>
    <tr>
      <th>3 </th>
      <td>       pressure-mbar</td>
      <td>    -0.279591</td>
    </tr>
    <tr>
      <th>4 </th>
      <td> solarRadiation-W/m2</td>
      <td>     0.138138</td>
    </tr>
    <tr>
      <th>5 </th>
      <td>       windDirection</td>
      <td>     0.041451</td>
    </tr>
    <tr>
      <th>6 </th>
      <td>       windSpeed-m/s</td>
      <td>    13.943372</td>
    </tr>
    <tr>
      <th>7 </th>
      <td> humidityRatio-kg/kg</td>
      <td> 75847.104324</td>
    </tr>
    <tr>
      <th>8 </th>
      <td>      coolingDegrees</td>
      <td>   -31.597421</td>
    </tr>
    <tr>
      <th>9 </th>
      <td>      heatingDegrees</td>
      <td>    57.903822</td>
    </tr>
    <tr>
      <th>10</th>
      <td>    dehumidification</td>
      <td> -8088.312347</td>
    </tr>
    <tr>
      <th>11</th>
      <td>           occupancy</td>
      <td>   131.534596</td>
    </tr>
    <tr>
      <th>12</th>
      <td>             cosHour</td>
      <td>  -343.896782</td>
    </tr>
  </tbody>
</table>
</div>




```
#Plot Observed and Predicted value 
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_hourlysteam_test.index, YY_hourlysteam_test, label='Observed', color='k')
plt.plot(XX_hourlysteam_test.index, y_hourlysteam_lr, label='Predicted', color='r')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x3ad09438>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_38_1.png)



```
#Plot Observed vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_hourlysteam_test, YY_hourlysteam_test, c='k')
plt.scatter(YY_hourlysteam_test, y_hourlysteam_lr, c='r')
plt.xlabel('Observed Steam Usage (LBS)')
plt.ylabel("Predicted Steam Usage (LBS): $\hat{Y}_i$")
plt.title("Observed vs Predicted Steam: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x3a6659b0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_39_1.png)


## 4.2 Support Vector Regression and Cross Validation


In this notebook, we'll train a Support Vector Regression(SVR) model for predicting building energy consumption based on historical energy data, several weather variables, hour of the day, day of the week, weekends and holidays. 

To do this, we'll fit the model to daily and hourly energy and weather data from 2012-01-01 to 2014-10-31 and compute the average squared residuals from predictions.

During the design time, we've used cross-validation to fine tune the SVR parameters. And since SVR take too much time to compute, in this final notebook we'll set the parameters to their optimal value that were found with cross validation. We'll still show the range of parameters that was provided as input to cross validation.



```
# special IPython command to prepare the notebook for matplotlib
%matplotlib inline 
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.svm import SVR
from sklearn.grid_search import GridSearchCV
from sklearn import cross_validation
from sklearn import grid_search

pd.options.display.mpl_style = 'default'
```


```
dailyElectricity = pd.read_excel('Data/dailyElectricityWithFeatures.xlsx')
dailyElectricity = dailyElectricity.drop('startDay', 1).drop('endDay', 1)

dailyChilledWater = pd.read_excel('Data/dailyChilledWaterWithFeatures.xlsx')
dailyChilledWater = dailyChilledWater.drop('startDay', 1).drop('endDay', 1)

dailySteam = pd.read_excel('Data/dailySteamWithFeatures.xlsx')
dailySteam = dailySteam.drop('startDay', 1).drop('endDay', 1)

hourlyElectricity = pd.read_excel('Data/hourlyElectricityWithFeatures.xlsx')
hourlyElectricity = hourlyElectricity.drop('startTime', 1).drop('endTime', 1)

hourlyChilledWater = pd.read_excel('Data/hourlyChilledWaterWithFeatures.xlsx')
hourlyChilledWater = hourlyChilledWater.drop('startTime', 1).drop('endTime', 1)

hourlySteam = pd.read_excel('Data/hourlySteamWithFeatures.xlsx')
hourlySteam = hourlySteam.drop('startTime', 1).drop('endTime', 1)

#display one dataframe
dailyElectricity.head()
```




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>electricity-kWh</th>
      <th>RH-%</th>
      <th>T-C</th>
      <th>Tdew-C</th>
      <th>pressure-mbar</th>
      <th>solarRadiation-W/m2</th>
      <th>windDirection</th>
      <th>windSpeed-m/s</th>
      <th>humidityRatio-kg/kg</th>
      <th>coolingDegrees</th>
      <th>heatingDegrees</th>
      <th>dehumidification</th>
      <th>occupancy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2012-01-01</th>
      <td> 2800.244977</td>
      <td> 76.652174</td>
      <td> 7.173913</td>
      <td>  3.073913</td>
      <td> 1004.956522</td>
      <td> 95.260870</td>
      <td> 236.086957</td>
      <td> 4.118361</td>
      <td> 0.004796</td>
      <td> 0.086957</td>
      <td>  7.826087</td>
      <td> 0</td>
      <td> 0.0</td>
    </tr>
    <tr>
      <th>2012-01-02</th>
      <td> 3168.974047</td>
      <td> 55.958333</td>
      <td> 5.833333</td>
      <td> -2.937500</td>
      <td>  994.625000</td>
      <td> 87.333333</td>
      <td> 253.750000</td>
      <td> 5.914357</td>
      <td> 0.003415</td>
      <td> 0.000000</td>
      <td>  9.166667</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-03</th>
      <td> 5194.533376</td>
      <td> 42.500000</td>
      <td>-3.208333</td>
      <td>-12.975000</td>
      <td> 1002.125000</td>
      <td> 95.708333</td>
      <td> 302.916667</td>
      <td> 6.250005</td>
      <td> 0.001327</td>
      <td> 0.000000</td>
      <td> 18.208333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-04</th>
      <td> 5354.861935</td>
      <td> 41.541667</td>
      <td>-7.083333</td>
      <td>-16.958333</td>
      <td> 1008.250000</td>
      <td> 98.750000</td>
      <td> 286.666667</td>
      <td> 5.127319</td>
      <td> 0.000890</td>
      <td> 0.000000</td>
      <td> 22.083333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
    <tr>
      <th>2012-01-05</th>
      <td> 5496.223993</td>
      <td> 46.916667</td>
      <td>-0.583333</td>
      <td> -9.866667</td>
      <td> 1002.041667</td>
      <td> 90.750000</td>
      <td> 258.333333</td>
      <td> 5.162041</td>
      <td> 0.001746</td>
      <td> 0.000000</td>
      <td> 15.583333</td>
      <td> 0</td>
      <td> 0.3</td>
    </tr>
  </tbody>
</table>
</div>



## Daily Prediction

Adding new features to the dataframe: weekday, day of the year and week of the year.


```
def addDailyTimeFeatures(df):
    df['weekday'] = df.index.weekday
    df['day'] = df.index.dayofyear
    df['week'] = df.index.weekofyear
    return df
    
dailyElectricity = addDailyTimeFeatures(dailyElectricity)
dailyChilledWater = addDailyTimeFeatures(dailyChilledWater)
dailySteam = addDailyTimeFeatures(dailySteam)
```

### Daily Electricity Prediction


```
df_elect = dailyElectricity[['weekday', 'day', 'week', 'occupancy', 'electricity-kWh']]

elect_train = pd.DataFrame(data=df_elect, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
elect_test = pd.DataFrame(data=df_elect, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_elect_train = elect_train.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)
XX_elect_test = elect_test.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)

YY_elect_train = elect_train['electricity-kWh']
YY_elect_test = elect_test['electricity-kWh']

```


```
# Input parameters ranges to cross validation 
gamma_range = [0.01, 0.001, 0.0001]
epsilon_range = [x * 0.1 for x in range(0, 2)]
C_range = range(1, 2500, 500)

# To speed up, we first find the optimal paratemers for C and gamma and then set them directly in the method call
tuned_parameters = [{
    'kernel': ['rbf', 'linear'],
#    'C': C_range,
#    'gamma': gamma_range,
    'epsilon': epsilon_range}]

# search for the best parameters with crossvalidation.
svr_elect = GridSearchCV(SVR(C=2000, gamma=0.01), param_grid = tuned_parameters, verbose = 0)

# Fit regression model
y_elect = svr_elect.fit(XX_elect_train, YY_elect_train).predict(XX_elect_test)

print 'Optimum parameters C=2000 and gamma=0.01 for SVR'
print 'Optimum parameters epsilon and kernel for SVR: ', svr_elect.best_params_

print "The test score R2 for SVR: ", svr_elect.score(XX_elect_test, YY_elect_test)

print("SVR mean squared error: %.2f"
      % np.mean((YY_elect_test - svr_elect.predict(XX_elect_test)) ** 2))
```

    Optimum parameters C=2000 and gamma=0.01 for SVR
    Optimum parameters epsilon and kernel for SVR:  {'epsilon': 0.1, 'kernel': 'rbf'}
    The test score R2 for RBF:  0.691753544427
    RBF mean squared error: 391582.21
    


```
#Plot time series of observed and predicted electricity demand over the testing period.
fig = plt.figure(figsize=(15,7))
plt.scatter(XX_elect_test.index, YY_elect_test, c='k', label='Observed')
plt.plot(XX_elect_test.index, y_elect, c='r', label='Predicted')
plt.xlabel('data')
plt.ylabel('target')
plt.title('Support Vector Regression')
plt.legend()
plt.show()
```


![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_50_0.png)



```
#Plot Observed vs. predicted usage.
fig = plt.figure(figsize=(6,6))
plt.scatter(YY_elect_test, YY_elect_test, c='k')
plt.scatter(YY_elect_test, y_elect, c='r')
plt.xlabel('Observed Elec. Usage (kWh)')
plt.ylabel("Predicted Elec. Usage (kWh): $\hat{Y}_i$")
plt.title("Energy vs Predicted Energy: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x1b5679e8>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_51_1.png)


### Daily Chilled Water Prediction


```

chilledw_train = pd.DataFrame(data=dailyChilledWater, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
chilledw_test = pd.DataFrame(data=dailyChilledWater, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_chilledw_train = chilledw_train.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)
XX_chilledw_test = chilledw_test.drop('chilledWater-TonDays', axis = 1).reset_index().drop('index', axis = 1)

YY_chilledw_train = chilledw_train['chilledWater-TonDays']
YY_chilledw_test = chilledw_test['chilledWater-TonDays']
```


```
# Optimal parameters for the SVR regressor
gamma_range = [0.1, 0.01, 0.001, 0.0001]
epsilon_range = [x * 0.1 for x in range(0, 3)]
C_range = range(1, 5, 2)

# To speed up, we first find the optimal paratemers for C and gamma and then set them directly in the method call
tuned_parameters = [{
    'kernel': ['rbf', 'linear'],
#    'C': C_range,
#    'gamma': gamma_range,
    'epsilon': epsilon_range}]

# search for the best parameters with crossvalidation.
svr_chilledw = GridSearchCV(SVR(C=3, gamma=0.0001), param_grid = tuned_parameters, verbose = 0)

# Fit regression model
y_chilledw = svr_chilledw.fit(XX_chilledw_train, YY_chilledw_train).predict(XX_chilledw_test)

print 'Optimum parameters C=3 and gamma=0.1 for SVR'
print 'Optimum epsilon and kernel: ', svr_chilledw.best_params_

print "The test score R2 for SVR: ", svr_chilledw.score(XX_chilledw_test, YY_chilledw_test)

print("SVR mean squared error: %.2f"
      % np.mean((YY_chilledw_test - svr_chilledw.predict(XX_chilledw_test)) ** 2))
```

    Optimum parameters C=3 and gamma=0.1 for SVR
    Optimum epsilon and kernel:  {'epsilon': 0.1, 'kernel': 'linear'}
    The test score R2 for SVR:  0.764904375769
    SVR mean squared error: 443.92
    


```
#Plot observed and predicted Chilled Water values with SVR
fig = plt.figure(figsize=(15,10))
plt.scatter(XX_chilledw_test.index, YY_chilledw_test, c='k', label='Observed')
plt.plot(XX_chilledw_test.index, y_chilledw, c='g', label='Predicted')
plt.xlabel('data')
plt.ylabel('target')
plt.title('Support Vector Regression')
plt.legend()
plt.show()
```


![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_55_0.png)



```
#Plot observed vs predicted energy usage
fig = plt.figure(figsize=(6,6))
plt.scatter(YY_chilledw_test, YY_chilledw_test, c='k')
plt.scatter(YY_chilledw_test, y_chilledw, c='g')
plt.xlabel('Observed Chilled Water Usage (TonDays)')
plt.ylabel("Predicted Chilled Water Usage (TonDays): $\hat{Y}_i$")
plt.title("Observed vs Predicted Energy: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x1b9e7588>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_56_1.png)


### Daily Steam Prediction


```
steam_train = pd.DataFrame(data=dailySteam, index=np.arange('2012-01', '2013-07', dtype='datetime64[D]')).dropna()
steam_test = pd.DataFrame(data=dailySteam, index=np.arange('2013-07', '2014-11', dtype='datetime64[D]')).dropna()

XX_steam_train = steam_train.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)
XX_steam_test = steam_test.drop('steam-LBS', axis = 1).reset_index().drop('index', axis = 1)

YY_steam_train = steam_train['steam-LBS']
YY_steam_test = steam_test['steam-LBS']
```


```
# Optimal parameters for the SVR regressor
gamma_range = [0.1, 0.01, 0.001, 0.0001]
epsilon_range = [x * 0.1 for x in range(0, 3)]
C_range = range(1, 500, 50)

tuned_parameters = [{
    'kernel': ['rbf', 'linear'],
#    'C': C_range,
    'gamma': gamma_range,
    'epsilon': epsilon_range}]

# search for the best parameters with crossvalidation.
svr_steam = GridSearchCV(SVR(C=50), param_grid = tuned_parameters, verbose = 0)

# Fit regression model
y_steam = svr_steam.fit(XX_steam_train, YY_steam_train).predict(XX_steam_test)

print 'Optimum parameters C=50 for SVR'
print 'Optimum epsilon, gamma and kernel: ', svr_steam.best_params_

print "The test score R2 for SVR: ", svr_steam.score(XX_steam_test, YY_steam_test)

print("SVR mean squared error: %.2f"
      % np.mean((YY_steam_test - svr_steam.predict(XX_steam_test)) ** 2))
```

    Optimum parameters C=50 for SVR
    Optimum epsilon, gamma and kernel:  {'epsilon': 0.2, 'gamma': 0.1, 'kernel': 'linear'}
    The test score R2 for SVR:  0.938467924325
    SVR mean squared error: 20632451.77
    


```
#Plot observed and predicted Steam values 
fig,ax = plt.subplots(1, 1,figsize=(20,10))
plt.scatter(XX_steam_test.index, YY_steam_test, c='k', label='Observed')
plt.plot(XX_steam_test.index, y_steam, c='r', label='Predicted')
plt.xlabel('data')
plt.ylabel('target')
plt.title('Support Vector Regression')
plt.legend()
plt.show()
```


![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_60_0.png)



```
#Plot Observed vs Predicted 
fig = plt.figure(figsize=(6,6))
plt.scatter(YY_steam_test, YY_steam_test, c='k')
plt.scatter(YY_steam_test, y_steam, c='r')
plt.xlabel('Observed Steam Usage (LBS)')
plt.ylabel("Predicted Steam Usage (LBS): $\hat{Y}_i$")
plt.title("Observed vs Predicted Energy: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x1b37b1d0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_61_1.png)


## Hourly Prediction

### Hourly Electricity Prediction

Adding new features to the dataframe: hour, weekday, day of the year and week of the year.


```
def addHourlyTimeFeatures(df):
    df['hour'] = df.index.hour
    df['weekday'] = df.index.weekday
    df['day'] = df.index.dayofyear
    df['week'] = df.index.weekofyear    
    return df

hourlyElectricity = addHourlyTimeFeatures(hourlyElectricity)
```


```
df_hourlyelect = hourlyElectricity[['hour', 'weekday', 'day', 'week', 'cosHour', 
                                                      'occupancy', 'electricity-kWh']]

hourlyelect_train = pd.DataFrame(data=df_hourlyelect, index=np.arange('2014-01-01 00:00:00', '2014-10-01 00:00:00', dtype='datetime64[h]')).dropna()
hourlyelect_test = pd.DataFrame(data=df_hourlyelect, index=np.arange('2014-10-01 00:00:00', '2014-11-01 00:00:00', dtype='datetime64[h]')).dropna()

XX_hourlyelect_train = hourlyelect_train.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)
XX_hourlyelect_test = hourlyelect_test.drop('electricity-kWh', axis = 1).reset_index().drop('index', axis = 1)

YY_hourlyelect_train = hourlyelect_train['electricity-kWh']
YY_hourlyelect_test = hourlyelect_test['electricity-kWh']
```


```
# Optimal parameters for the SVR regressor
gamma_range = [0.01, 0.001, 0.0001]
epsilon_range = [x * 0.1 for x in range(0, 2)]
C_range = range(1, 5, 1)

tuned_parameters = [{
    'kernel': ['rbf', 'linear'],
#    'C': C_range,
#    'gamma': gamma_range,
    'epsilon': epsilon_range}]

# search for the best parameters with crossvalidation.
svr_hourlyelect = GridSearchCV(SVR(C=1, gamma=0.01), param_grid = tuned_parameters, verbose = 0)

# Fit regression model
y_hourlyelect = svr_hourlyelect.fit(XX_hourlyelect_train, YY_hourlyelect_train).predict(XX_hourlyelect_test)

print 'Optimum parameters C=1 and gamma=0.01 for SVR'
print 'Optimum epsilon and kernel for SVR: ', svr_hourlyelect.best_params_

print "The test score R2: ", svr_hourlyelect.score(XX_hourlyelect_test, YY_hourlyelect_test)

print("SVR mean squared error: %.2f"
      % np.mean((YY_hourlyelect_test - svr_hourlyelect.predict(XX_hourlyelect_test)) ** 2))
```

    Optimum parameters C=1 and gamma=0.01 for SVR
    Optimum epsilon and kernel for SVR:  {'epsilon': 0.1, 'kernel': 'linear'}
    The test score R2:  0.747282383852
    SVR mean squared error: 1561.24
    


```
#Plot observed and predicted electricity value 
fig = plt.figure(figsize=(20,10))
plt.scatter(XX_hourlyelect_test.index, YY_hourlyelect_test, label='Observed', color='k')
plt.plot(XX_hourlyelect_test.index, y_hourlyelect, label='Predicted', color='g')
plt.legend(loc='upper right')
```




    <matplotlib.legend.Legend at 0x1f4a6ef0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_68_1.png)



```
#Plot Observed vs. Predicted usage.
fig = plt.figure(figsize=(6,6))
plt.plot(YY_hourlyelect_test, YY_hourlyelect_test, c='k')
plt.scatter(YY_hourlyelect_test, y_hourlyelect, c='g')
plt.xlabel('Observed Elec. Usage (kWh)')
plt.ylabel("Predicted Elec. Usage (kWh): $\hat{Y}_i$")
plt.title("Observed vs Predicted Elec.: $Y_i$ vs $\hat{Y}_i$")
```




    <matplotlib.text.Text at 0x210e82b0>




![png](Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_files/Part%204.1%264.2%20Linear%20Regression%20and%20Support%20Vector%20Regression_69_1.png)



```

```
