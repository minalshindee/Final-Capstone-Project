# Final-Capstone-Project
#Run this cell once when you launch this page
import requests
import pandas as pd
import numpy as np 
import matplotlib.pylab as plt 
%matplotlib inline 
from matplotlib.pylab import rcParams
rcParams['figure.figsize'] = 15, 6
from statsmodels.tsa.arima_model import ARIMA
import sklearn.metrics as sk
import datetime as dt  # for dealing with times
from pyramid.arima import auto_arima

def fetch_crypto_min(script):
    i = 1
    script = str(script)
    while i <= 6:
        if i == 1:
            r3 = requests.get('https://min-api.cryptocompare.com/data/histominute?fsym='+script+'&tsym=USD&limit=2000&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea')
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            combined = df[1:2001]
        else:
            dflist1 = df.values.tolist()
            URL = 'https://min-api.cryptocompare.com/data/histominute?fsym='+script+'&tsym=USD&limit=2000&toTs='+str(int(dflist1[0][4]))+'&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea'
            r3 = requests.get(URL)
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            df1=df[1:2001]
            combined = pd.concat([df1, combined], axis=0)
        i = i + 1

    combined.index = [dt.datetime.fromtimestamp(x) for x in combined.time]
    combined.drop(combined.tail(1).index,inplace=True) # drop last n rows
    return combined

def fetch_crypto_hour(script):
    i = 1
    script = str(script)
    while i <= 10:
        if i == 1:
            r3 = requests.get('https://min-api.cryptocompare.com/data/histohour?fsym='+script+'&tsym=USD&limit=2000&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea')
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            combined = df[1:2001]
        else:
            dflist1 = df.values.tolist()
            URL = 'https://min-api.cryptocompare.com/data/histohour?fsym='+script+'&tsym=USD&limit=2000&toTs='+str(int(dflist1[0][4]))+'&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea'
            r3 = requests.get(URL)
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            df1=df[1:2001]
            combined = pd.concat([df1, combined], axis=0)
        i = i + 1

    combined.index = [dt.datetime.fromtimestamp(x) for x in combined.time]
    combined.drop(combined.tail(1).index,inplace=True) # drop last n rows
    return combined

def fetch_crypto_day(script):
    i = 1
    script = str(script)
    while i <= 1:
        if i == 1:
            r3 = requests.get('https://min-api.cryptocompare.com/data/histoday?fsym='+script+'&tsym=USD&limit=2000&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea')
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            combined = df[1:2001]
        else:
            dflist1 = df.values.tolist()
            URL = 'https://min-api.cryptocompare.com/data/histoday?fsym='+script+'&tsym=USD&limit=2000&toTs='+str(int(dflist1[0][4]))+'&api_key=5c279bd07792b0f8dad7ca32c8d53ac176df20141878a1f2fe136af65ab31cea'
            r3 = requests.get(URL)
            bitdata = r3.json()
            df = pd.DataFrame(bitdata['Data'])
            df1=df[1:2001]
            combined = pd.concat([df1, combined], axis=0)
        i = i + 1

    combined.index = [dt.datetime.fromtimestamp(x) for x in combined.time]
    combined.drop(combined.tail(1).index,inplace=True) # drop last n rows
    return combined

def forecast_series_min(x):
    stepwise_model = auto_arima(x.close, start_p=1, start_q=1,
                           max_p=3, max_q=3, m=12,
                           start_P=0, seasonal=False,
                           d=1, D=1, trace=True,
                           error_action='ignore',  
                           suppress_warnings=True, 
                           stepwise=True)
    future_forecast = stepwise_model.predict(n_periods=60)
    x.iloc[-1][4]
    time_list = []
    a = x.iloc[-1][4]
    i = 1
    while i <= 60:
        a = a + 60.0
        time_list.append(a)
        i = i + 1
    len(time_list)

    future_forecast_df = pd.DataFrame({'time':time_list,'forecast': future_forecast})
    future_forecast_df.index = [dt.datetime.fromtimestamp(x) for x in future_forecast_df.time]
    #plt.plot(x.close)
    print(future_forecast_df)
    plt.plot(future_forecast_df.forecast)

def forecast_series_hour(x):
    stepwise_model = auto_arima(x.close, start_p=1, start_q=1,
                           max_p=3, max_q=3, m=12,
                           start_P=0, seasonal=False,
                           d=1, D=1, trace=True,
                           error_action='ignore',  
                           suppress_warnings=True, 
                           stepwise=True)
    future_forecast = stepwise_model.predict(n_periods=24)
    x.iloc[-1][4]
    time_list = []
    a = x.iloc[-1][4]
    i = 1
    while i <= 24:
        a = a + 3600.0
        time_list.append(a)
        i = i + 1
    len(time_list)

    future_forecast_df = pd.DataFrame({'time':time_list,'forecast': future_forecast})
    future_forecast_df.index = [dt.datetime.fromtimestamp(x) for x in future_forecast_df.time]
    #plt.plot(x.close)
    print(future_forecast_df)
    plt.plot(future_forecast_df.forecast)
    
def forecast_series_day(x):
    stepwise_model = auto_arima(x.close, start_p=1, start_q=1,
                           max_p=3, max_q=3, m=12,
                           start_P=0, seasonal=False,
                           d=1, D=1, trace=True,
                           error_action='ignore',  
                           suppress_warnings=True, 
                           stepwise=True)
    future_forecast = stepwise_model.predict(n_periods=7)
    x.iloc[-1][4]
    time_list = []
    a = x.iloc[-1][4]
    i = 1
    while i <= 7:
        a = a + 86400.0
        time_list.append(a)
        i = i + 1
    len(time_list)

    future_forecast_df = pd.DataFrame({'time':time_list,'forecast': future_forecast})
    future_forecast_df.index = [dt.datetime.fromtimestamp(x) for x in future_forecast_df.time]
    #plt.plot(x.close)
    print(future_forecast_df)
    plt.plot(future_forecast_df.forecast)

def find_script(x):
    if x == '1':
        script = 'EOS'
    elif x == '2':
        script = 'TRX'
    elif x == '3':
        script = 'ETH'
    elif x == '4':
        script = 'XMR'
    elif x == '5':
        script = 'VET'
    elif x == '6':
        script = 'LTC'
    elif x == '7':
        script = 'WTC'
    elif x == '8':
        script = 'XLM'
    elif x == '9':
        script = 'OMG'
    elif x == '10':
        script = 'BNB'
    elif x == '11':
        script = 'ADA'
    elif x == '12':
        script = 'BAT'
    elif x == '13':
        script = 'LINK'
    elif x == '14':
        script = 'BCH'
    elif x == '15':
        script = 'BTC'
    elif x == '16':
        script = 'WAVES'
    else:
        script = 'ERROR'
    return script

def process_script(x,t):
    a = find_script(x)
    if a == 'ERROR':
        print('The script is not available. Please check.')
    else:
        if t == 'm':
            series = fetch_crypto_min(a)
            forecast_series_min(series)
        elif t == 'h':
            series = fetch_crypto_hour(a)
            forecast_series_hour(series)
        elif t == 'd':
            series = fetch_crypto_day(a)
            forecast_series_day(series)
        else:
            print('Please check time-value input')
