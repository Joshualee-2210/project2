import pandas as pd
import matplotlib.pyplot as plt


# Load data
pwt1001 = pd.read_stata('https://dataverse.nl/api/access/datafile/354098')

# Filter and select relevant columns
data = pwt1001.loc[pwt1001['country'].isin(['France','Germany','Canada','Italy','Japan','United Kingdom','United Status'])][['year', 'countrycode', 'rgdpna', 'rkna', 'pop', 'emp', 'avh', 'labsh', 'rtfpna']]
data = data.loc[(data['year'] >= 1995) & (data['year'] <= 2019)].dropna()

# Calculate additional variables
data['alpha'] = 1 - data['labsh'] #alpha
data['L'] = data['emp'] * data['avh']  # L

data['ln_y']= np.log(data['rgdna']/data['L'])
data['ln_k']= np.log(data['rkna']/data['L'])

# Order by year
data =data.sort_values('year')

# Group by isocode
grouped_data = data.groupby('countycode')

data['g'] = (grouped_data['ln_n'].diff()*100)
data['cd'] = data['a']*(grouped_data['ln_k'].diff()*100)
data['tg'] = data['g'] - data['cd']

# Remove missing values
data = data.dropna()

# Calculate summary statistics
summary = data.groupby('countycode').agg({'g':"mean",'tg':"mean",'cd':"mean"})

#calculate additional  summary statistics
summary['Growth Rate'] = summary['g']
summary['Capital Deepening'] = summary['cd']
summary['TFP Growth'] = summary['tg']
summary['Capital Share'] = summary['Capital Deepening']/summary['Growth Rate']
summary['TFP Share'] = summary['TFP Growth']/summary['Growth Rate']




# Print first 10 rows of data
print(summary)





