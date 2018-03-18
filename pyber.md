

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import os
```


```python
#Read the files and create Dataframe 
city_file = os.path.join("raw_data","city_data.csv")
ride_file = os.path.join("raw_data","ride_data.csv")
city_df = pd.read_csv(city_file)
ride_df = pd.read_csv(ride_file)

#When looked at the file, there was one duplicate entry.
city_df = city_df.drop_duplicates("city", keep = "first")

#Merge the two dataframes on CITY.
merged_df = city_df.merge(ride_df, on = "city")
```


```python
#Group by city
by_city = merged_df.groupby("city")

#Average Fare per city
avg_fare = by_city["fare"].mean()

#Total number of rides per city
total_rides = by_city["ride_id"].count()

#Total number of drivers
total_drivers = by_city["driver_count"].mean()

#City type
city_type = city_df.set_index("city")["type"]

cityInfo = pd.DataFrame({"Average Fare":avg_fare,
                         "Number of Rides":total_rides,
                         "Number of Drivers":total_drivers,
                         "Type of city":city_type})

rural = cityInfo[cityInfo["Type of city"] == "Rural"]
suburban = cityInfo[cityInfo["Type of city"] == "Suburban"]
urban = cityInfo[cityInfo["Type of city"] == "Urban"]

colors = {'Gold':'#FFD700', 'Light Sky Blue':'#87CEFA', 'Light Coral':'#F08080'}
CityColors = {'Urban': colors['Gold'], 'Suburban': colors['Light Sky Blue'], 'Rural': colors['Light Coral']}

plt.scatter(rural['Number of Rides'], rural['Average Fare'], s = rural['Number of Drivers']*10, 
            color = CityColors['Rural'], edgecolor = 'black', label = 'Rural', alpha = .75)
plt.scatter(suburban['Number of Rides'], suburban['Average Fare'], s = suburban['Number of Drivers']*10, 
            color = CityColors['Suburban'], edgecolor = 'black', label = 'Suburban', alpha = .75)
plt.scatter(urban['Number of Rides'], urban['Average Fare'], s = urban['Number of Drivers']*10, 
            color = CityColors['Urban'], edgecolor = 'black', label = 'Urban', alpha = .75)

plt.suptitle('Pyber Ride Sharing Data')
plt.title('Note : Size of Bubble Corresponds to Number of Drivers per City')
plt.xlabel('Number of Rides per City')
plt.ylabel('Average Fare per City')

legend = plt.legend(frameon = True, edgecolor = 'Blue')
legend.legendHandles[0]._sizes = [50]
legend.legendHandles[1]._sizes = [50]
legend.legendHandles[2]._sizes = [50]

plt.grid()
plt.show()
```


![png](output_2_0.png)



```python
# % Total fares by city type.

type_df = merged_df.groupby('type')["type","fare","ride_id","driver_count"]

fare_sum = type_df.sum()['fare']
chart_labels = fare_sum.index

pie_colors = [CityColors[x] for x in chart_labels]
explode = [0 , 0, .3]
plt.pie(fare_sum, startangle = 120, colors = pie_colors, explode = explode, labels = chart_labels, autopct = "%1.1f%%", shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})
plt.title('% of Total Fares by City Type')
plt.axis('equal')
plt.show()
```


![png](output_3_0.png)



```python
# % Total rides by city type.

ride_count = type_df.count()["ride_id"]
labels = ride_count.index

pie_colors = [CityColors[x] for x in labels]
explode = [0 , 0, .3]
plt.pie(ride_count, startangle = 120, colors = pie_colors, explode = explode, labels = labels, autopct = "%1.1f%%", shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})
plt.title('% of Total rides by City Type')
plt.axis('equal')
plt.show()
```


![png](output_4_0.png)



```python
# % of Total drivers by city type.

drivers_df = city_df.groupby("type")
drivers = drivers_df.sum()["driver_count"]
lables = drivers.index

pie_colors = [CityColors[x] for x in labels]
explode = [0 , 0, .3]
plt.pie(drivers, startangle = 360, colors = pie_colors, explode = explode, labels = labels, autopct = "%1.1f%%", shadow = True, wedgeprops = {'linewidth': .5, 'edgecolor': 'black'})
plt.title('% of Total drivers by City Type')
plt.axis('equal')
plt.show()
```


![png](output_5_0.png)

