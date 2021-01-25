# 5yrs TimeSeries Transition for Average Temperature of region: INDIA
Year 2010-2015

Year 2010
![TimeSeriesTimelapse2010-1 (dragged)](https://user-images.githubusercontent.com/39980788/105606446-8798b200-5dbf-11eb-9199-c1d128c81062.jpeg)

Year2015
![Timelapse2015-1 (dragged)](https://user-images.githubusercontent.com/39980788/105606471-9a12eb80-5dbf-11eb-9ccf-f91d167c79e5.jpeg)

The above images shows timeseries transition following the change in Average Temperature of Indian Region
lib used: PIL, Pandas, Numpy, Basemap


#Region of interest data generation

from netCDF4 import Dataset
import numpy as np
import pandas as pd

# reading netcdfdata
data = Dataset("<file path/>.nc")

# creating variabless to store data
lat = data.variables['lat'][:]
print('\nlat data:\n', lat)

lon= data.variables['lon'][:]
print('\nlon data:\n', lon)

time = data.variables['time']


# storing lat lon of india
lat_india = 20.5937
lon_india = 78.9629

# Square difference for deciding closest value for lati and longi
sq_dis_lat = (lat - lat_india) ** 2
sq_dis_lon = (lon - lon_india) ** 2

print("__________________________________________________________________________")

#index for min lat lon of india
min_index_lat = sq_dis_lat.argmin()  #to find index of corresponding minimum no.
print('sq_dis_lat INDEX:',min_index_lat)
print('Corr Val:',sq_dis_lat.min())

min_index_lon = sq_dis_lon.argmin()
print('sq_dis_lon INDEX:',min_index_lon)
print('Corr Val:',sq_dis_lon.min())

temp= data.variables['tave'] #tave temperature average
print('_________________ Temperature:',temp.units,temp[0,71,37],'__________________')      #[time,lat,lon] @C
#As time data is represented in minutes so: 1440th min = 1st day, 1440x2 min = 2nd day... 365 day[0-364]

print("__________________________________________________________________________")
#slicing dd-mm-yy from date string
file_date = data.variables['time'].units[14:18]
print("\nFile Year: "+file_date)

start_date = data.variables['time'].units[14:24]
print('\nStart Date: '+start_date)

end_date = data.variables['time'].units[14:18]
print('\nEnd Date: '+end_date+'-12-31')
print("__________________________________________________________________________")

#-pandas empty dataframe for 365 entries
date_range = pd.date_range(start=start_date,end=end_date)
print('Date Range:',date_range)

df = pd.DataFrame(0, columns=['Temperature'] ,index=date_range)
print(df.iloc[0])
print(df)
dt = np.arange(data.variables['time'].size)
#print(dt)

for time_index in dt:
    df.iloc[time_index] = temp[time_index,min_index_lat,min_index_lon]

df.to_csv("india_temperature.csv")


#Combining data files for transition

from netCDF4 import Dataset
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap


data = Dataset(<file path>)

lats = data.variables['lat'][:]
lons= data.variables['lon'][:]
time = data.variables['time'][:]
tave= data.variables['tave'][:]

#Map representation
mp = Basemap(projection='merc',
             llcrnrlon= 62.152595,
             llcrnrlat= 6.723604,
             urcrnrlon= 99.845418,
             urcrnrlat= 37.245480,
             resolution='i')

#mesh Grid
lon, lat =np.meshgrid(lons, lats)
x,y = mp(lon, lat)

#for all days
days = np.arange(0,365)

for i in days:
    c_scheme = mp.pcolor(x, y, np.squeeze(tave[i,:,:]), cmap='jet')

    mp.drawcoastlines()
    mp.drawstates()
    mp.drawcountries()

    cbar = mp.colorbar(c_scheme, location='left', pad='30%')
    day=i+1
    plt.title("India's Average Temperature: Day" +str(day)+'-2015')
    plt.clim(-40,40)
    plt.savefig(file path/"+str(day)+".jpg")
    plt.clf()
