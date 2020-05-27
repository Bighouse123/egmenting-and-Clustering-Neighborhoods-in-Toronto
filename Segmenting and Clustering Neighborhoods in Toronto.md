### Peer-graded Assignment: Segmenting and Clustering Neighborhoods in Toronto Part 1


```python
#install Beautiful Soup and requests for Web Scaping
!pip install BeautifulSoup4
!pip install requests
```

    Requirement already satisfied: BeautifulSoup4 in /opt/anaconda3/lib/python3.7/site-packages (4.8.2)
    Requirement already satisfied: soupsieve>=1.2 in /opt/anaconda3/lib/python3.7/site-packages (from BeautifulSoup4) (1.9.5)
    Requirement already satisfied: requests in /opt/anaconda3/lib/python3.7/site-packages (2.22.0)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /opt/anaconda3/lib/python3.7/site-packages (from requests) (1.25.8)
    Requirement already satisfied: certifi>=2017.4.17 in /opt/anaconda3/lib/python3.7/site-packages (from requests) (2019.11.28)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /opt/anaconda3/lib/python3.7/site-packages (from requests) (3.0.4)
    Requirement already satisfied: idna<2.9,>=2.5 in /opt/anaconda3/lib/python3.7/site-packages (from requests) (2.8)



```python
from bs4 import BeautifulSoup
import requests
import pandas as pd
import numpy as np
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values
```


```python
# getting data from internet
source='https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M'
df_Canada=pd.read_html(source)
# Call the position where the table is stored
neighborhood=df_Canada[0]
# Eliminate the first row
neighborhood=neighborhood.drop([0])
# Eliminate "Not assigned", categorical values from "Borough" Column
neighborhood=neighborhood[neighborhood.Borough !='Not assigned']
# Making DataFrame
neighborhood=pd.DataFrame(neighborhood)
# Merging rows with same Postcode
neighborhood.set_index(['Postal Code','Borough'],inplace=True)
merge_result = neighborhood.groupby(level=['Postal Code','Borough'], sort=False).agg( ','.join)
# Setting the index
serial_wise=merge_result.reset_index()
serial_wise.loc[4, 'Neighborhood']='Queen\'s Park'
serial_wise.to_excel('Canada_table.xls')
df=pd.DataFrame(serial_wise)
df.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue, Humber Valley Village</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill, Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.shape
```




    (103, 3)



###  Peer-graded Assignment: Segmenting and Clustering Neighborhoods in Toronto Part 2


```python
!pip3 install geocoder
```

    Requirement already satisfied: geocoder in /opt/anaconda3/lib/python3.7/site-packages (1.38.1)
    Requirement already satisfied: click in /opt/anaconda3/lib/python3.7/site-packages (from geocoder) (7.0)
    Requirement already satisfied: future in /opt/anaconda3/lib/python3.7/site-packages (from geocoder) (0.18.2)
    Requirement already satisfied: requests in /opt/anaconda3/lib/python3.7/site-packages (from geocoder) (2.22.0)
    Requirement already satisfied: six in /opt/anaconda3/lib/python3.7/site-packages (from geocoder) (1.14.0)
    Requirement already satisfied: ratelim in /opt/anaconda3/lib/python3.7/site-packages (from geocoder) (0.1.6)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /opt/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (1.25.8)
    Requirement already satisfied: idna<2.9,>=2.5 in /opt/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (2.8)
    Requirement already satisfied: certifi>=2017.4.17 in /opt/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (2019.11.28)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /opt/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (3.0.4)
    Requirement already satisfied: decorator in /opt/anaconda3/lib/python3.7/site-packages (from ratelim->geocoder) (4.4.1)



```python
df_geo_coor = pd.read_csv("http://cocl.us/Geospatial_data")
df_geo_coor.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M1J</td>
      <td>43.744734</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1K</td>
      <td>43.727929</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M1L</td>
      <td>43.711112</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M1M</td>
      <td>43.716316</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1N</td>
      <td>43.692657</td>
      <td>-79.264848</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_toronto = pd.merge(df, df_geo_coor, how='left', left_on = 'Postal Code', right_on = 'Postal Code')
df_toronto.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue, Humber Valley Village</td>
      <td>43.667856</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.745906</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill, Woodbine Gardens</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
  </tbody>
</table>
</div>



### Peer-graded Assignment: Segmenting and Clustering Neighborhoods in Toronto Part 3

### The map of Toronto 


```python
import folium 
```


```python
address = "Toronto, ON"

geolocator = Nominatim(user_agent="toronto_explorer")
location = geolocator.geocode(address)
latitude = location.latitude
longitude = location.longitude
print('The geograpical coordinate of Toronto city are {}, {}.'.format(latitude, longitude))
```

    The geograpical coordinate of Toronto city are 43.6534817, -79.3839347.



```python
# create map of Toronto using latitude and longitude values
map_toronto = folium.Map(location=[latitude, longitude], zoom_start=9)
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgCiAgICAgICAgPHNjcmlwdD4KICAgICAgICAgICAgTF9OT19UT1VDSCA9IGZhbHNlOwogICAgICAgICAgICBMX0RJU0FCTEVfM0QgPSBmYWxzZTsKICAgICAgICA8L3NjcmlwdD4KICAgIAogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjYuMC9kaXN0L2xlYWZsZXQuanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY29kZS5qcXVlcnkuY29tL2pxdWVyeS0xLjEyLjQubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9qcy9ib290c3RyYXAubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5qcyI+PC9zY3JpcHQ+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjYuMC9kaXN0L2xlYWZsZXQuY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vYm9vdHN0cmFwLzMuMi4wL2Nzcy9ib290c3RyYXAubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9mb250LWF3ZXNvbWUvNC42LjMvY3NzL2ZvbnQtYXdlc29tZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuanMuY2xvdWRmbGFyZS5jb20vYWpheC9saWJzL0xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLzIuMC4yL2xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2Nkbi5naXRoYWNrLmNvbS9weXRob24tdmlzdWFsaXphdGlvbi9mb2xpdW0vbWFzdGVyL2ZvbGl1bS90ZW1wbGF0ZXMvbGVhZmxldC5hd2Vzb21lLnJvdGF0ZS5jc3MiLz4KICAgIDxzdHlsZT5odG1sLCBib2R5IHt3aWR0aDogMTAwJTtoZWlnaHQ6IDEwMCU7bWFyZ2luOiAwO3BhZGRpbmc6IDA7fTwvc3R5bGU+CiAgICA8c3R5bGU+I21hcCB7cG9zaXRpb246YWJzb2x1dGU7dG9wOjA7Ym90dG9tOjA7cmlnaHQ6MDtsZWZ0OjA7fTwvc3R5bGU+CiAgICAKICAgICAgICAgICAgPG1ldGEgbmFtZT0idmlld3BvcnQiIGNvbnRlbnQ9IndpZHRoPWRldmljZS13aWR0aCwKICAgICAgICAgICAgICAgIGluaXRpYWwtc2NhbGU9MS4wLCBtYXhpbXVtLXNjYWxlPTEuMCwgdXNlci1zY2FsYWJsZT1ubyIgLz4KICAgICAgICAgICAgPHN0eWxlPgogICAgICAgICAgICAgICAgI21hcF8zNmJlN2Q1Y2NhZDI0ODY3YTY4M2M3NDgwZGMyZmQ1NiB7CiAgICAgICAgICAgICAgICAgICAgcG9zaXRpb246IHJlbGF0aXZlOwogICAgICAgICAgICAgICAgICAgIHdpZHRoOiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgbGVmdDogMC4wJTsKICAgICAgICAgICAgICAgICAgICB0b3A6IDAuMCU7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgIDwvc3R5bGU+CiAgICAgICAgCjwvaGVhZD4KPGJvZHk+ICAgIAogICAgCiAgICAgICAgICAgIDxkaXYgY2xhc3M9ImZvbGl1bS1tYXAiIGlkPSJtYXBfMzZiZTdkNWNjYWQyNDg2N2E2ODNjNzQ4MGRjMmZkNTYiID48L2Rpdj4KICAgICAgICAKPC9ib2R5Pgo8c2NyaXB0PiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFwXzM2YmU3ZDVjY2FkMjQ4NjdhNjgzYzc0ODBkYzJmZDU2ID0gTC5tYXAoCiAgICAgICAgICAgICAgICAibWFwXzM2YmU3ZDVjY2FkMjQ4NjdhNjgzYzc0ODBkYzJmZDU2IiwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBjZW50ZXI6IFs0My42NTM0ODE3LCAtNzkuMzgzOTM0N10sCiAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NywKICAgICAgICAgICAgICAgICAgICB6b29tOiA5LAogICAgICAgICAgICAgICAgICAgIHpvb21Db250cm9sOiB0cnVlLAogICAgICAgICAgICAgICAgICAgIHByZWZlckNhbnZhczogZmFsc2UsCiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICk7CgogICAgICAgICAgICAKCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfYjk3ZTg5MWM1NjllNDIzMTliMmE4NDFjNzU3ZTM1NzggPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICJodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZyIsCiAgICAgICAgICAgICAgICB7ImF0dHJpYnV0aW9uIjogIkRhdGEgYnkgXHUwMDI2Y29weTsgXHUwMDNjYSBocmVmPVwiaHR0cDovL29wZW5zdHJlZXRtYXAub3JnXCJcdTAwM2VPcGVuU3RyZWV0TWFwXHUwMDNjL2FcdTAwM2UsIHVuZGVyIFx1MDAzY2EgaHJlZj1cImh0dHA6Ly93d3cub3BlbnN0cmVldG1hcC5vcmcvY29weXJpZ2h0XCJcdTAwM2VPRGJMXHUwMDNjL2FcdTAwM2UuIiwgImRldGVjdFJldGluYSI6IGZhbHNlLCAibWF4TmF0aXZlWm9vbSI6IDE4LCAibWF4Wm9vbSI6IDE4LCAibWluWm9vbSI6IDAsICJub1dyYXAiOiBmYWxzZSwgIm9wYWNpdHkiOiAxLCAic3ViZG9tYWlucyI6ICJhYmMiLCAidG1zIjogZmFsc2V9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzM2YmU3ZDVjY2FkMjQ4NjdhNjgzYzc0ODBkYzJmZDU2KTsKICAgICAgICAKPC9zY3JpcHQ+ onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
latitude = 43.6534817
longitude = -79.3839347
```

### the Borough that contain the word "Toronto


```python
neighborhoods = pd.merge(df, df_geo_coor, how='left', left_on = 'Postal Code', right_on = 'Postal Code')
neighborhoods.head(15)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue, Humber Valley Village</td>
      <td>43.667856</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.745906</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill, Woodbine Gardens</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
      <td>43.709577</td>
      <td>-79.445073</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park, Princess Gardens, Martin Grov...</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill, Port Union, Highland Creek</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.725900</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
      <td>43.695344</td>
      <td>-79.318389</td>
    </tr>
  </tbody>
</table>
</div>




```python
toronto_data= neighborhoods[neighborhoods['Borough'].str.contains('Toronto', na = False)].reset_index(drop=True)
toronto_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
  </tbody>
</table>
</div>




```python
toronto_data.shape
```




    (39, 5)



### Visualizing all the Neighbourhoods of the above data frame using Folium


```python
df3=toronto_data
```


```python
map_toronto = folium.Map(location=[43.651070,-79.347015],zoom_start=10)

for lat,lng,borough,neighborhood in zip(df3['Latitude'],df3['Longitude'],df3['Borough'],df3['Neighborhood']):
    label = '{}, {}'.format(neighborhood, borough)
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
    [lat,lng],
    radius=6,
    popup=label,
    color='Purple',
    fill=True,
    fill_color='#3186cc',
    fill_opacity=0.8,
    parse_html=False).add_to(map_toronto)
map_toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgCiAgICAgICAgPHNjcmlwdD4KICAgICAgICAgICAgTF9OT19UT1VDSCA9IGZhbHNlOwogICAgICAgICAgICBMX0RJU0FCTEVfM0QgPSBmYWxzZTsKICAgICAgICA8L3NjcmlwdD4KICAgIAogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjYuMC9kaXN0L2xlYWZsZXQuanMiPjwvc2NyaXB0PgogICAgPHNjcmlwdCBzcmM9Imh0dHBzOi8vY29kZS5qcXVlcnkuY29tL2pxdWVyeS0xLjEyLjQubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9qcy9ib290c3RyYXAubWluLmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5qcyI+PC9zY3JpcHQ+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuLmpzZGVsaXZyLm5ldC9ucG0vbGVhZmxldEAxLjYuMC9kaXN0L2xlYWZsZXQuY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vYm9vdHN0cmFwLzMuMi4wL2Nzcy9ib290c3RyYXAubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9mb250LWF3ZXNvbWUvNC42LjMvY3NzL2ZvbnQtYXdlc29tZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vY2RuanMuY2xvdWRmbGFyZS5jb20vYWpheC9saWJzL0xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLzIuMC4yL2xlYWZsZXQuYXdlc29tZS1tYXJrZXJzLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2Nkbi5naXRoYWNrLmNvbS9weXRob24tdmlzdWFsaXphdGlvbi9mb2xpdW0vbWFzdGVyL2ZvbGl1bS90ZW1wbGF0ZXMvbGVhZmxldC5hd2Vzb21lLnJvdGF0ZS5jc3MiLz4KICAgIDxzdHlsZT5odG1sLCBib2R5IHt3aWR0aDogMTAwJTtoZWlnaHQ6IDEwMCU7bWFyZ2luOiAwO3BhZGRpbmc6IDA7fTwvc3R5bGU+CiAgICA8c3R5bGU+I21hcCB7cG9zaXRpb246YWJzb2x1dGU7dG9wOjA7Ym90dG9tOjA7cmlnaHQ6MDtsZWZ0OjA7fTwvc3R5bGU+CiAgICAKICAgICAgICAgICAgPG1ldGEgbmFtZT0idmlld3BvcnQiIGNvbnRlbnQ9IndpZHRoPWRldmljZS13aWR0aCwKICAgICAgICAgICAgICAgIGluaXRpYWwtc2NhbGU9MS4wLCBtYXhpbXVtLXNjYWxlPTEuMCwgdXNlci1zY2FsYWJsZT1ubyIgLz4KICAgICAgICAgICAgPHN0eWxlPgogICAgICAgICAgICAgICAgI21hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCB7CiAgICAgICAgICAgICAgICAgICAgcG9zaXRpb246IHJlbGF0aXZlOwogICAgICAgICAgICAgICAgICAgIHdpZHRoOiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICAgICAgbGVmdDogMC4wJTsKICAgICAgICAgICAgICAgICAgICB0b3A6IDAuMCU7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgIDwvc3R5bGU+CiAgICAgICAgCjwvaGVhZD4KPGJvZHk+ICAgIAogICAgCiAgICAgICAgICAgIDxkaXYgY2xhc3M9ImZvbGl1bS1tYXAiIGlkPSJtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQiID48L2Rpdj4KICAgICAgICAKPC9ib2R5Pgo8c2NyaXB0PiAgICAKICAgIAogICAgICAgICAgICB2YXIgbWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkID0gTC5tYXAoCiAgICAgICAgICAgICAgICAibWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkIiwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBjZW50ZXI6IFs0My42NTEwNywgLTc5LjM0NzAxNV0sCiAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NywKICAgICAgICAgICAgICAgICAgICB6b29tOiAxMCwKICAgICAgICAgICAgICAgICAgICB6b29tQ29udHJvbDogdHJ1ZSwKICAgICAgICAgICAgICAgICAgICBwcmVmZXJDYW52YXM6IGZhbHNlLAogICAgICAgICAgICAgICAgfQogICAgICAgICAgICApOwoKICAgICAgICAgICAgCgogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyX2RhNDcyNTRmMDY3YTQyMmI4NDFiNjE0NDMyMWEwM2FmID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAiaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmciLAogICAgICAgICAgICAgICAgeyJhdHRyaWJ1dGlvbiI6ICJEYXRhIGJ5IFx1MDAyNmNvcHk7IFx1MDAzY2EgaHJlZj1cImh0dHA6Ly9vcGVuc3RyZWV0bWFwLm9yZ1wiXHUwMDNlT3BlblN0cmVldE1hcFx1MDAzYy9hXHUwMDNlLCB1bmRlciBcdTAwM2NhIGhyZWY9XCJodHRwOi8vd3d3Lm9wZW5zdHJlZXRtYXAub3JnL2NvcHlyaWdodFwiXHUwMDNlT0RiTFx1MDAzYy9hXHUwMDNlLiIsICJkZXRlY3RSZXRpbmEiOiBmYWxzZSwgIm1heE5hdGl2ZVpvb20iOiAxOCwgIm1heFpvb20iOiAxOCwgIm1pblpvb20iOiAwLCAibm9XcmFwIjogZmFsc2UsICJvcGFjaXR5IjogMSwgInN1YmRvbWFpbnMiOiAiYWJjIiwgInRtcyI6IGZhbHNlfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzBhYzE1OGUwNTU5NGRjNjljNWQ0YTkxMmUwODk1NjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTQyNTk5LCAtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzNmZGMyM2IxNDFiODQzZWU5NTBhYTQ5YmJlNGYyOTk3ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF83ZGM2NzQ4NjNmYmI0ODc3OGZiYjRjY2YwZWY5NmJhMyA9ICQoYDxkaXYgaWQ9Imh0bWxfN2RjNjc0ODYzZmJiNDg3NzhmYmI0Y2NmMGVmOTZiYTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJlZ2VudCBQYXJrLCBIYXJib3VyZnJvbnQsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfM2ZkYzIzYjE0MWI4NDNlZTk1MGFhNDliYmU0ZjI5OTcuc2V0Q29udGVudChodG1sXzdkYzY3NDg2M2ZiYjQ4Nzc4ZmJiNGNjZjBlZjk2YmEzKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9jMGFjMTU4ZTA1NTk0ZGM2OWM1ZDRhOTEyZTA4OTU2NS5iaW5kUG9wdXAocG9wdXBfM2ZkYzIzYjE0MWI4NDNlZTk1MGFhNDliYmU0ZjI5OTcpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzZhOThjMGQwM2EzMDQxZTg5ZDk3NGY4NjdmYTZlOTZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYyMzAxNSwgLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF84Y2NlNmUyZmQ1NWU0NTAxYWJlMTc4NDM1YmM4NmRkNiA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfNTUwZjBlM2E1ZWFhNDQ0ZWEzYWMzY2RkYjFkOTc3ODcgPSAkKGA8ZGl2IGlkPSJodG1sXzU1MGYwZTNhNWVhYTQ0NGVhM2FjM2NkZGIxZDk3Nzg3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RdWVlbiYjMzk7cyBQYXJrLCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzhjY2U2ZTJmZDU1ZTQ1MDFhYmUxNzg0MzViYzg2ZGQ2LnNldENvbnRlbnQoaHRtbF81NTBmMGUzYTVlYWE0NDRlYTNhYzNjZGRiMWQ5Nzc4Nyk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfNmE5OGMwZDAzYTMwNDFlODlkOTc0Zjg2N2ZhNmU5NmEuYmluZFBvcHVwKHBvcHVwXzhjY2U2ZTJmZDU1ZTQ1MDFhYmUxNzg0MzViYzg2ZGQ2KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80ODgyODA2YzI3YjM0NDZhYjVlM2VkODE5M2NmMGJjYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NzE2MTgsIC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2JiNTBmNjQ2MTRjOTRiZjI4NDkxZDQwMmFlZWU1MDdjID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9kZjY1OWU0ODk4MjE0MzEwYTZjNzQ2MTUwMTgxZTlmMSA9ICQoYDxkaXYgaWQ9Imh0bWxfZGY2NTllNDg5ODIxNDMxMGE2Yzc0NjE1MDE4MWU5ZjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdhcmRlbiBEaXN0cmljdCwgUnllcnNvbiwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9iYjUwZjY0NjE0Yzk0YmYyODQ5MWQ0MDJhZWVlNTA3Yy5zZXRDb250ZW50KGh0bWxfZGY2NTllNDg5ODIxNDMxMGE2Yzc0NjE1MDE4MWU5ZjEpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzQ4ODI4MDZjMjdiMzQ0NmFiNWUzZWQ4MTkzY2YwYmNiLmJpbmRQb3B1cChwb3B1cF9iYjUwZjY0NjE0Yzk0YmYyODQ5MWQ0MDJhZWVlNTA3YykKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTQ1NTljZGFkMzc0NGU1MWE0ODY3N2M0MDg2MjM5NzYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE0OTM5LCAtNzkuMzc1NDE3OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzZmNmJkNGRkNzE4NTQyMjVhYzM2Zjk1MGYyZjAwMzc0ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF81YWQzZjBiZWQ4M2Q0YzVkOTVhNjY5ZWUzNTNmMWYxNCA9ICQoYDxkaXYgaWQ9Imh0bWxfNWFkM2YwYmVkODNkNGM1ZDk1YTY2OWVlMzUzZjFmMTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duLCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzZmNmJkNGRkNzE4NTQyMjVhYzM2Zjk1MGYyZjAwMzc0LnNldENvbnRlbnQoaHRtbF81YWQzZjBiZWQ4M2Q0YzVkOTVhNjY5ZWUzNTNmMWYxNCk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfYTQ1NTljZGFkMzc0NGU1MWE0ODY3N2M0MDg2MjM5NzYuYmluZFBvcHVwKHBvcHVwXzZmNmJkNGRkNzE4NTQyMjVhYzM2Zjk1MGYyZjAwMzc0KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ZDRmYzk4N2IxN2M0NWQ4OGFkZjQxOGM2ZmFkM2U0YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3NjM1NzM5OTk5OTk5LCAtNzkuMjkzMDMxMl0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzYzZDUxODRmYzA4ODQ5MGI4MGU1YzBjMzE3NDY2MmE2ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9hM2VlN2RhMGJmMjk0N2U4YWFiM2E4ZDRkNTUwYzA1MSA9ICQoYDxkaXYgaWQ9Imh0bWxfYTNlZTdkYTBiZjI5NDdlOGFhYjNhOGQ0ZDU1MGMwNTEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBCZWFjaGVzLCBFYXN0IFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfNjNkNTE4NGZjMDg4NDkwYjgwZTVjMGMzMTc0NjYyYTYuc2V0Q29udGVudChodG1sX2EzZWU3ZGEwYmYyOTQ3ZThhYWIzYThkNGQ1NTBjMDUxKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl81ZDRmYzk4N2IxN2M0NWQ4OGFkZjQxOGM2ZmFkM2U0Yi5iaW5kUG9wdXAocG9wdXBfNjNkNTE4NGZjMDg4NDkwYjgwZTVjMGMzMTc0NjYyYTYpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NlNmZiMmJhNmY4OTQwOTBhODdiMjcxYzY2OWRmYjhkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ0NzcwNzk5OTk5OTk2LCAtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzE3Y2I4MTE1NDg0NTRkNWE5MzRkMzk0YmFjOTMxY2YzID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9mODRkYjkwOTQzMjQ0YjJhYmM2N2YxMGI5Y2UwMDVhZiA9ICQoYDxkaXYgaWQ9Imh0bWxfZjg0ZGI5MDk0MzI0NGIyYWJjNjdmMTBiOWNlMDA1YWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlcmN6eSBQYXJrLCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzE3Y2I4MTE1NDg0NTRkNWE5MzRkMzk0YmFjOTMxY2YzLnNldENvbnRlbnQoaHRtbF9mODRkYjkwOTQzMjQ0YjJhYmM2N2YxMGI5Y2UwMDVhZik7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfY2U2ZmIyYmE2Zjg5NDA5MGE4N2IyNzFjNjY5ZGZiOGQuYmluZFBvcHVwKHBvcHVwXzE3Y2I4MTE1NDg0NTRkNWE5MzRkMzk0YmFjOTMxY2YzKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82ZjQwZTFiZTk1ODQ0MzQ2YmI0MjA2YWM3NzhhYjcyYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1Nzk1MjQsIC03OS4zODczODI2XSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfZTYzZjg1N2RjZTIyNDE0ODkwNDY0ZDYxZTdmYzlkMzAgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2Y0YmUxZmViNmVmNjQxNDdiMmRhMTU3Mjg5ODA1ZDBhID0gJChgPGRpdiBpZD0iaHRtbF9mNGJlMWZlYjZlZjY0MTQ3YjJkYTE1NzI4OTgwNWQwYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2VudHJhbCBCYXkgU3RyZWV0LCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2U2M2Y4NTdkY2UyMjQxNDg5MDQ2NGQ2MWU3ZmM5ZDMwLnNldENvbnRlbnQoaHRtbF9mNGJlMWZlYjZlZjY0MTQ3YjJkYTE1NzI4OTgwNWQwYSk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfNmY0MGUxYmU5NTg0NDM0NmJiNDIwNmFjNzc4YWI3MmMuYmluZFBvcHVwKHBvcHVwX2U2M2Y4NTdkY2UyMjQxNDg5MDQ2NGQ2MWU3ZmM5ZDMwKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wOTA0ZmNlZjYzMTM0MmQ0YjljZTFkMThmMmQ0ODEyZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTU0MiwgLTc5LjQyMjU2MzddLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9lY2ZiMmE0YTYzYmM0ZDA0YWZhMGI5YWM1MTk2NDA1ZSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfZGQ4ZTRmMDA0MmZjNDEwOGJmMDNkYWViODZhNjFhMDUgPSAkKGA8ZGl2IGlkPSJodG1sX2RkOGU0ZjAwNDJmYzQxMDhiZjAzZGFlYjg2YTYxYTA1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DaHJpc3RpZSwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9lY2ZiMmE0YTYzYmM0ZDA0YWZhMGI5YWM1MTk2NDA1ZS5zZXRDb250ZW50KGh0bWxfZGQ4ZTRmMDA0MmZjNDEwOGJmMDNkYWViODZhNjFhMDUpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzA5MDRmY2VmNjMxMzQyZDRiOWNlMWQxOGYyZDQ4MTJlLmJpbmRQb3B1cChwb3B1cF9lY2ZiMmE0YTYzYmM0ZDA0YWZhMGI5YWM1MTk2NDA1ZSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYjYzYzY4ZjNkMjM3NGZmNTg0ZWU3ZDhhZWMxNjJiYTQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTA1NzEyMDAwMDAwMSwgLTc5LjM4NDU2NzVdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF83MWY1NDhjNDJmYzQ0NmU0OGY1MWMxNGQ1ODI4MTk5NSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfMGI1NjgwZWE1NDQzNDhkN2JjYTcwYTg1ZDEwODIzZjMgPSAkKGA8ZGl2IGlkPSJodG1sXzBiNTY4MGVhNTQ0MzQ4ZDdiY2E3MGE4NWQxMDgyM2YzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SaWNobW9uZCwgQWRlbGFpZGUsIEtpbmcsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfNzFmNTQ4YzQyZmM0NDZlNDhmNTFjMTRkNTgyODE5OTUuc2V0Q29udGVudChodG1sXzBiNTY4MGVhNTQ0MzQ4ZDdiY2E3MGE4NWQxMDgyM2YzKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9iNjNjNjhmM2QyMzc0ZmY1ODRlZTdkOGFlYzE2MmJhNC5iaW5kUG9wdXAocG9wdXBfNzFmNTQ4YzQyZmM0NDZlNDhmNTFjMTRkNTgyODE5OTUpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2MyMTlmZTk2Yjc3ZTQ1MWFiNGRhM2MyYjk0ZWY1OGVmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5MDA1MTAwMDAwMDEsIC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfNDEwYjFmZTg5Yjk3NDI3YjgzODZmYTRiNzM3YTM1ZjkgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzc5ZWZiZjkzNjI1MjQ3MWNhODUyZGY1MjFhOTAzNDVhID0gJChgPGRpdiBpZD0iaHRtbF83OWVmYmY5MzYyNTI0NzFjYTg1MmRmNTIxYTkwMzQ1YSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RHVmZmVyaW4sIERvdmVyY291cnQgVmlsbGFnZSwgV2VzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzQxMGIxZmU4OWI5NzQyN2I4Mzg2ZmE0YjczN2EzNWY5LnNldENvbnRlbnQoaHRtbF83OWVmYmY5MzYyNTI0NzFjYTg1MmRmNTIxYTkwMzQ1YSk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfYzIxOWZlOTZiNzdlNDUxYWI0ZGEzYzJiOTRlZjU4ZWYuYmluZFBvcHVwKHBvcHVwXzQxMGIxZmU4OWI5NzQyN2I4Mzg2ZmE0YjczN2EzNWY5KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83YzJkZGZlNmM1YjE0NTQwYTQ3YzgxOGYzNGM1NGI3YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsIC03OS4zODE3NTIyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzEwYTQ2YTk5ZTA2NjRhNjM5MjVjYjAyYmRjYjFjMWMwID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF83MGRmZDMzMzllM2M0ZTk0OWM1ZDIwYTExMDY2ZjZkNCA9ICQoYDxkaXYgaWQ9Imh0bWxfNzBkZmQzMzM5ZTNjNGU5NDljNWQyMGExMTA2NmY2ZDQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhhcmJvdXJmcm9udCBFYXN0LCBVbmlvbiBTdGF0aW9uLCBUb3JvbnRvIElzbGFuZHMsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfMTBhNDZhOTllMDY2NGE2MzkyNWNiMDJiZGNiMWMxYzAuc2V0Q29udGVudChodG1sXzcwZGZkMzMzOWUzYzRlOTQ5YzVkMjBhMTEwNjZmNmQ0KTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl83YzJkZGZlNmM1YjE0NTQwYTQ3YzgxOGYzNGM1NGI3Yi5iaW5kUG9wdXAocG9wdXBfMTBhNDZhOTllMDY2NGE2MzkyNWNiMDJiZGNiMWMxYzApCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IyMjY4NTg1NTNkNTQ3ODQ5MzU2ZjE2NzU2YzEzM2YwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3OTI2NzAwMDAwMDA2LCAtNzkuNDE5NzQ5N10sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzhkNmM4ZTMwYTI5MzRjNzdiOTQyYmI3MjQ4OGUyYTNmID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9mYTk4NjUyZmU4NmM0NGU3YjdlOGYwM2Y3MTZmZjNjNSA9ICQoYDxkaXYgaWQ9Imh0bWxfZmE5ODY1MmZlODZjNDRlN2I3ZThmMDNmNzE2ZmYzYzUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxpdHRsZSBQb3J0dWdhbCwgVHJpbml0eSwgV2VzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzhkNmM4ZTMwYTI5MzRjNzdiOTQyYmI3MjQ4OGUyYTNmLnNldENvbnRlbnQoaHRtbF9mYTk4NjUyZmU4NmM0NGU3YjdlOGYwM2Y3MTZmZjNjNSk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfYjIyNjg1ODU1M2Q1NDc4NDkzNTZmMTY3NTZjMTMzZjAuYmluZFBvcHVwKHBvcHVwXzhkNmM4ZTMwYTI5MzRjNzdiOTQyYmI3MjQ4OGUyYTNmKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jODYzNWY5OTVmZjM0YWRiYWQ4YTAwM2NhZmJhMDUyYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU1NzEsIC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9kMGM3MTVjZTM4Mzk0YTg5OWFkZDQyZmNjNWVlNGUzOCA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfZTRhYjIxZmMwM2E3NDNlNzkzOTM0M2M5MzAxOGYyYTEgPSAkKGA8ZGl2IGlkPSJodG1sX2U0YWIyMWZjMDNhNzQzZTc5MzkzNDNjOTMwMThmMmExIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgRGFuZm9ydGggV2VzdCwgUml2ZXJkYWxlLCBFYXN0IFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfZDBjNzE1Y2UzODM5NGE4OTlhZGQ0MmZjYzVlZTRlMzguc2V0Q29udGVudChodG1sX2U0YWIyMWZjMDNhNzQzZTc5MzkzNDNjOTMwMThmMmExKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9jODYzNWY5OTVmZjM0YWRiYWQ4YTAwM2NhZmJhMDUyYi5iaW5kUG9wdXAocG9wdXBfZDBjNzE1Y2UzODM5NGE4OTlhZGQ0MmZjYzVlZTRlMzgpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQwODY4ZDdkYWJhZTRmNWM4MGQ4MmYzODBhZjQ0ZDA5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3MTc2OCwgLTc5LjM4MTU3NjQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYzc4OGY0NzZlNjIxNGZkMmJkMzg4YjVjNTliZWFmNTEgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2IyMWI2M2JmODY2YTRlMjRiNTgxZjliMjkzNzA2MWQ1ID0gJChgPGRpdiBpZD0iaHRtbF9iMjFiNjNiZjg2NmE0ZTI0YjU4MWY5YjI5MzcwNjFkNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VG9yb250byBEb21pbmlvbiBDZW50cmUsIERlc2lnbiBFeGNoYW5nZSwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9jNzg4ZjQ3NmU2MjE0ZmQyYmQzODhiNWM1OWJlYWY1MS5zZXRDb250ZW50KGh0bWxfYjIxYjYzYmY4NjZhNGUyNGI1ODFmOWIyOTM3MDYxZDUpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzQwODY4ZDdkYWJhZTRmNWM4MGQ4MmYzODBhZjQ0ZDA5LmJpbmRQb3B1cChwb3B1cF9jNzg4ZjQ3NmU2MjE0ZmQyYmQzODhiNWM1OWJlYWY1MSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfY2UwNjExOGQ2MThiNDEyNDljOGNmM2IzNjE0NmFjNGYgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42MzY4NDcyLCAtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF9lYWRmMzYxN2IyZDI0M2RhYmJmNzNhNGQzNDIwZmRmZCA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfODUxODM4ZGJiMmIzNGVhZGE4NDI1YThlZDg3NjVjYTQgPSAkKGA8ZGl2IGlkPSJodG1sXzg1MTgzOGRiYjJiMzRlYWRhODQyNWE4ZWQ4NzY1Y2E0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ccm9ja3RvbiwgUGFya2RhbGUgVmlsbGFnZSwgRXhoaWJpdGlvbiBQbGFjZSwgV2VzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2VhZGYzNjE3YjJkMjQzZGFiYmY3M2E0ZDM0MjBmZGZkLnNldENvbnRlbnQoaHRtbF84NTE4MzhkYmIyYjM0ZWFkYTg0MjVhOGVkODc2NWNhNCk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfY2UwNjExOGQ2MThiNDEyNDljOGNmM2IzNjE0NmFjNGYuYmluZFBvcHVwKHBvcHVwX2VhZGYzNjE3YjJkMjQzZGFiYmY3M2E0ZDM0MjBmZGZkKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mZjAwYWE1OWY2MzE0NDk1YmY3NTdhOWYwMDJiNDlhYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2ODk5ODUsIC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzkxNjViZjkyYmFhYzQwZDc4YzkyNThlY2UxNjA3NTBiID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF82MzRiMmZjNDMyYWU0YTU5YmZhN2IwZTAyYjAzNTk0ZCA9ICQoYDxkaXYgaWQ9Imh0bWxfNjM0YjJmYzQzMmFlNGE1OWJmYTdiMGUwMmIwMzU5NGQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkluZGlhIEJhemFhciwgVGhlIEJlYWNoZXMgV2VzdCwgRWFzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzkxNjViZjkyYmFhYzQwZDc4YzkyNThlY2UxNjA3NTBiLnNldENvbnRlbnQoaHRtbF82MzRiMmZjNDMyYWU0YTU5YmZhN2IwZTAyYjAzNTk0ZCk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfZmYwMGFhNTlmNjMxNDQ5NWJmNzU3YTlmMDAyYjQ5YWMuYmluZFBvcHVwKHBvcHVwXzkxNjViZjkyYmFhYzQwZDc4YzkyNThlY2UxNjA3NTBiKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84MjljMjBiZjM2ZTI0ZjVjYmY3NzQxNDcyNDJlZTNlYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODE5ODUsIC03OS4zNzk4MTY5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2YwZmYxYjk1ZjdhMTQzNTdiNDNjY2RlYzAwY2I4N2Q3ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF82NzFiMjQzOWQ1M2M0MWI4ODhhOGZlYzkwYzllZGY3ZCA9ICQoYDxkaXYgaWQ9Imh0bWxfNjcxYjI0MzlkNTNjNDFiODg4YThmZWM5MGM5ZWRmN2QiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNvbW1lcmNlIENvdXJ0LCBWaWN0b3JpYSBIb3RlbCwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9mMGZmMWI5NWY3YTE0MzU3YjQzY2NkZWMwMGNiODdkNy5zZXRDb250ZW50KGh0bWxfNjcxYjI0MzlkNTNjNDFiODg4YThmZWM5MGM5ZWRmN2QpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzgyOWMyMGJmMzZlMjRmNWNiZjc3NDE0NzI0MmVlM2VjLmJpbmRQb3B1cChwb3B1cF9mMGZmMWI5NWY3YTE0MzU3YjQzY2NkZWMwMGNiODdkNykKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfN2FmZTU3ZmVjMDBjNDlkMGFjNDlkNmZiZjQ0MTkwMWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTk1MjU1LCAtNzkuMzQwOTIzXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYTZmMWJkYTJjZWEyNDFiNjllOWQ1ZTgxNzdlYzNlYmQgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzM1ZmYzNGJjNDVlMzRmMWM4ZDA0ODI3YzcwZGYwYjg2ID0gJChgPGRpdiBpZD0iaHRtbF8zNWZmMzRiYzQ1ZTM0ZjFjOGQwNDgyN2M3MGRmMGI4NiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3R1ZGlvIERpc3RyaWN0LCBFYXN0IFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYTZmMWJkYTJjZWEyNDFiNjllOWQ1ZTgxNzdlYzNlYmQuc2V0Q29udGVudChodG1sXzM1ZmYzNGJjNDVlMzRmMWM4ZDA0ODI3YzcwZGYwYjg2KTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl83YWZlNTdmZWMwMGM0OWQwYWM0OWQ2ZmJmNDQxOTAxYy5iaW5kUG9wdXAocG9wdXBfYTZmMWJkYTJjZWEyNDFiNjllOWQ1ZTgxNzdlYzNlYmQpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY1M2Q0N2Q2M2IwZjQ3ODJhMGM2YTM4ZTIzNDI3NmVmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzI4MDIwNSwgLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF8yNmYzYjhhYzQ1MDA0MGI5YmEyYzE4MWZhZjkxOGM1MSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfNDc0ZjhmZWJmZWMyNDhjOWJhZWQzOWFlYTJmMzAyNWYgPSAkKGA8ZGl2IGlkPSJodG1sXzQ3NGY4ZmViZmVjMjQ4YzliYWVkMzlhZWEyZjMwMjVmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXdyZW5jZSBQYXJrLCBDZW50cmFsIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfMjZmM2I4YWM0NTAwNDBiOWJhMmMxODFmYWY5MThjNTEuc2V0Q29udGVudChodG1sXzQ3NGY4ZmViZmVjMjQ4YzliYWVkMzlhZWEyZjMwMjVmKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl82NTNkNDdkNjNiMGY0NzgyYTBjNmEzOGUyMzQyNzZlZi5iaW5kUG9wdXAocG9wdXBfMjZmM2I4YWM0NTAwNDBiOWJhMmMxODFmYWY5MThjNTEpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ4MDFmNWYyMmQ3YzRjODBiZTJiNzhiMDM1OThlYjkwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExNjk0OCwgLTc5LjQxNjkzNTU5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfZWVlMjMzYTQ2NmE1NGFhMzhkNjg2ZjM0MjQzNTlhYmEgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2U4MGM3N2NhYjU0MDRkNDU5NjhlMzMxZDc3MmM4MWZlID0gJChgPGRpdiBpZD0iaHRtbF9lODBjNzdjYWI1NDA0ZDQ1OTY4ZTMzMWQ3NzJjODFmZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWxhd24sIENlbnRyYWwgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9lZWUyMzNhNDY2YTU0YWEzOGQ2ODZmMzQyNDM1OWFiYS5zZXRDb250ZW50KGh0bWxfZTgwYzc3Y2FiNTQwNGQ0NTk2OGUzMzFkNzcyYzgxZmUpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzQ4MDFmNWYyMmQ3YzRjODBiZTJiNzhiMDM1OThlYjkwLmJpbmRQb3B1cChwb3B1cF9lZWUyMzNhNDY2YTU0YWEzOGQ2ODZmMzQyNDM1OWFiYSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjAxNzFkYzg3OWZjNGM0OGJkZjY4MDFiZDZjMmVhMGIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTI3NTExLCAtNzkuMzkwMTk3NV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzgzYjYyMGQ0ZTIyYjQxMWFiZmMwZjg5ZTUwYjM5M2YxID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9iODgxYTZhZWRjNzI0Y2MyOTM4YzQzYzYwZDY5MWQ1NSA9ICQoYDxkaXYgaWQ9Imh0bWxfYjg4MWE2YWVkYzcyNGNjMjkzOGM0M2M2MGQ2OTFkNTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUgTm9ydGgsIENlbnRyYWwgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF84M2I2MjBkNGUyMmI0MTFhYmZjMGY4OWU1MGIzOTNmMS5zZXRDb250ZW50KGh0bWxfYjg4MWE2YWVkYzcyNGNjMjkzOGM0M2M2MGQ2OTFkNTUpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzIwMTcxZGM4NzlmYzRjNDhiZGY2ODAxYmQ2YzJlYTBiLmJpbmRQb3B1cChwb3B1cF84M2I2MjBkNGUyMmI0MTFhYmZjMGY4OWU1MGIzOTNmMSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDQ2MDQxODg2OGM4NDQyMmI4ZTA5YmIwMjYyNGVlZjcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LCAtNzkuNDExMzA3MjAwMDAwMDFdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF80NWI3NmZlNzkwNjI0OTVkYmQ2ZjdkMDhlNjJmYzIwNSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfZDVkN2NlMjc1NjU0NDQ1MGEwYWY4NjNjYzk4MjQ3NjEgPSAkKGA8ZGl2IGlkPSJodG1sX2Q1ZDdjZTI3NTY1NDQ0NTBhMGFmODYzY2M5ODI0NzYxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBOb3J0aCAmYW1wOyBXZXN0LCBGb3Jlc3QgSGlsbCBSb2FkIFBhcmssIENlbnRyYWwgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF80NWI3NmZlNzkwNjI0OTVkYmQ2ZjdkMDhlNjJmYzIwNS5zZXRDb250ZW50KGh0bWxfZDVkN2NlMjc1NjU0NDQ1MGEwYWY4NjNjYzk4MjQ3NjEpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2Q0NjA0MTg4NjhjODQ0MjJiOGUwOWJiMDI2MjRlZWY3LmJpbmRQb3B1cChwb3B1cF80NWI3NmZlNzkwNjI0OTVkYmQ2ZjdkMDhlNjJmYzIwNSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzFlYTkzNmNmOTRjNGIwOWIwNDk3NDNkODBiOWVjNmMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjE2MDgzLCAtNzkuNDY0NzYzMjk5OTk5OTldLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF8xYTRhNjc0MWRkMmY0NjNkOTQ1MGZlNGZlZGU3ZGJlMiA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfNTdiZWY1MmU3ZTlhNDQ2Y2I4OTNlY2VmMjdmYmIxNWQgPSAkKGA8ZGl2IGlkPSJodG1sXzU3YmVmNTJlN2U5YTQ0NmNiODkzZWNlZjI3ZmJiMTVkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IaWdoIFBhcmssIFRoZSBKdW5jdGlvbiBTb3V0aCwgV2VzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzFhNGE2NzQxZGQyZjQ2M2Q5NDUwZmU0ZmVkZTdkYmUyLnNldENvbnRlbnQoaHRtbF81N2JlZjUyZTdlOWE0NDZjYjg5M2VjZWYyN2ZiYjE1ZCk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfYzFlYTkzNmNmOTRjNGIwOWIwNDk3NDNkODBiOWVjNmMuYmluZFBvcHVwKHBvcHVwXzFhNGE2NzQxZGQyZjQ2M2Q5NDUwZmU0ZmVkZTdkYmUyKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yYTYwZWM3ZmE2ZmU0NmFiODQwMGNhOTdiNmI2ZjliYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcxNTM4MzQsIC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzMyMWE0NzgwNDYwMTQ3MWRhYTdmYTkwYzEyNmRjZTlkID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF82MzkwODBhN2QxYmQ0M2RjOGM4NTAxMDBhMjE4NzAyNyA9ICQoYDxkaXYgaWQ9Imh0bWxfNjM5MDgwYTdkMWJkNDNkYzhjODUwMTAwYTIxODcwMjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFRvcm9udG8gV2VzdCwgTGF3cmVuY2UgUGFyaywgQ2VudHJhbCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzMyMWE0NzgwNDYwMTQ3MWRhYTdmYTkwYzEyNmRjZTlkLnNldENvbnRlbnQoaHRtbF82MzkwODBhN2QxYmQ0M2RjOGM4NTAxMDBhMjE4NzAyNyk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfMmE2MGVjN2ZhNmZlNDZhYjg0MDBjYTk3YjZiNmY5YmMuYmluZFBvcHVwKHBvcHVwXzMyMWE0NzgwNDYwMTQ3MWRhYTdmYTkwYzEyNmRjZTlkKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMTQxMDgxMjRjNDc0OGNiYjA4ODNjN2Y0MWM1M2U4YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3MjcwOTcsIC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2JiY2JmNWIyM2YxNTRhNGQ4ZjJiNjc3YjYwNjc3ZWU4ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9mMjdlOGUyYTA0ODA0Y2Q1YjM1ZmYyNDI2ZGJlMDZkMCA9ICQoYDxkaXYgaWQ9Imh0bWxfZjI3ZThlMmEwNDgwNGNkNWIzNWZmMjQyNmRiZTA2ZDAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBBbm5leCwgTm9ydGggTWlkdG93biwgWW9ya3ZpbGxlLCBDZW50cmFsIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYmJjYmY1YjIzZjE1NGE0ZDhmMmI2NzdiNjA2NzdlZTguc2V0Q29udGVudChodG1sX2YyN2U4ZTJhMDQ4MDRjZDViMzVmZjI0MjZkYmUwNmQwKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9lMTQxMDgxMjRjNDc0OGNiYjA4ODNjN2Y0MWM1M2U4Yi5iaW5kUG9wdXAocG9wdXBfYmJjYmY1YjIzZjE1NGE0ZDhmMmI2NzdiNjA2NzdlZTgpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRiMjhmNjNmYzZlMzRkYTk4NGQ5YjJjNzYzZTYzYmNhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4OTU5NywgLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2E1YTc0OGNjMzZkZjRlZDM5ZGE5MmRmMzVjODQ3MzYxID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF8zMDcyZmZhM2Q5NTQ0NTU2OGQ3MWViNWQ3NjY3MTEzNCA9ICQoYDxkaXYgaWQ9Imh0bWxfMzA3MmZmYTNkOTU0NDU1NjhkNzFlYjVkNzY2NzExMzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlLCBSb25jZXN2YWxsZXMsIFdlc3QgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF9hNWE3NDhjYzM2ZGY0ZWQzOWRhOTJkZjM1Yzg0NzM2MS5zZXRDb250ZW50KGh0bWxfMzA3MmZmYTNkOTU0NDU1NjhkNzFlYjVkNzY2NzExMzQpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyXzRiMjhmNjNmYzZlMzRkYTk4NGQ5YjJjNzYzZTYzYmNhLmJpbmRQb3B1cChwb3B1cF9hNWE3NDhjYzM2ZGY0ZWQzOWRhOTJkZjM1Yzg0NzM2MSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZThhZTkyMmY4YTk0NGU5Y2JiMTgwZmI3ZTBkMTA3NzMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDQzMjQ0LCAtNzkuMzg4NzkwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzEyN2JjN2JhMTFiZDRjNzNiMGExZTFkYjk1OTlhYzVmID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF8zOWU3NTU5MWYxZDA0YWUzOGQ1NmM1MWQ4NzI4MDE4MyA9ICQoYDxkaXYgaWQ9Imh0bWxfMzllNzU1OTFmMWQwNGFlMzhkNTZjNTFkODcyODAxODMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUsIENlbnRyYWwgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF8xMjdiYzdiYTExYmQ0YzczYjBhMWUxZGI5NTk5YWM1Zi5zZXRDb250ZW50KGh0bWxfMzllNzU1OTFmMWQwNGFlMzhkNTZjNTFkODcyODAxODMpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2U4YWU5MjJmOGE5NDRlOWNiYjE4MGZiN2UwZDEwNzczLmJpbmRQb3B1cChwb3B1cF8xMjdiYzdiYTExYmQ0YzczYjBhMWUxZGI5NTk5YWM1ZikKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDJlNTI5NTVmOTZjNGYzNGFlODg3MWUzZTJiYzM1N2MgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjI2OTU2LCAtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzQxMWJhZDBiNmRmZTQxZjY4NmZkMjdmZWM5Mjg0Y2JiID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF84ZDEzNjM2ZGE0NDI0Mjc3YmI1NWQ3MzAzYzYyZTBkNiA9ICQoYDxkaXYgaWQ9Imh0bWxfOGQxMzYzNmRhNDQyNDI3N2JiNTVkNzMwM2M2MmUwZDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVuaXZlcnNpdHkgb2YgVG9yb250bywgSGFyYm9yZCwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF80MTFiYWQwYjZkZmU0MWY2ODZmZDI3ZmVjOTI4NGNiYi5zZXRDb250ZW50KGh0bWxfOGQxMzYzNmRhNDQyNDI3N2JiNTVkNzMwM2M2MmUwZDYpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2QyZTUyOTU1Zjk2YzRmMzRhZTg4NzFlM2UyYmMzNTdjLmJpbmRQb3B1cChwb3B1cF80MTFiYWQwYjZkZmU0MWY2ODZmZDI3ZmVjOTI4NGNiYikKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWIzNjllMjk2NTVlNDE5ZTgxZjM2NjcyZDBmNWJlYWEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTE1NzA2LCAtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2IxYWNhMDZmOGY4NzQ5ZDI4NDVmMjFlNjU2YWE3YmI0ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF80NzYzZDQwMzFiYTU0OGU5OWVmZGIzYjRjOWQzMmU1MiA9ICQoYDxkaXYgaWQ9Imh0bWxfNDc2M2Q0MDMxYmE1NDhlOTllZmRiM2I0YzlkMzJlNTIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSwgU3dhbnNlYSwgV2VzdCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2IxYWNhMDZmOGY4NzQ5ZDI4NDVmMjFlNjU2YWE3YmI0LnNldENvbnRlbnQoaHRtbF80NzYzZDQwMzFiYTU0OGU5OWVmZGIzYjRjOWQzMmU1Mik7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfNWIzNjllMjk2NTVlNDE5ZTgxZjM2NjcyZDBmNWJlYWEuYmluZFBvcHVwKHBvcHVwX2IxYWNhMDZmOGY4NzQ5ZDI4NDVmMjFlNjU2YWE3YmI0KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84ZDE3MTQwZWM5YmE0ZDI1ODNkNTc5MWQ1ZTBjYWE4OCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4OTU3NDMsIC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzhmYmM2ODNkMjQ1ZjQ0YTc5OGZiMGUzOTNkYjQwYTRmID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF84NWY4Njk4NzM1NTg0OTdkYjE3ZWRhZWFiOTAxNGMyNCA9ICQoYDxkaXYgaWQ9Imh0bWxfODVmODY5ODczNTU4NDk3ZGIxN2VkYWVhYjkwMTRjMjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1vb3JlIFBhcmssIFN1bW1lcmhpbGwgRWFzdCwgQ2VudHJhbCBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzhmYmM2ODNkMjQ1ZjQ0YTc5OGZiMGUzOTNkYjQwYTRmLnNldENvbnRlbnQoaHRtbF84NWY4Njk4NzM1NTg0OTdkYjE3ZWRhZWFiOTAxNGMyNCk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfOGQxNzE0MGVjOWJhNGQyNTgzZDU3OTFkNWUwY2FhODguYmluZFBvcHVwKHBvcHVwXzhmYmM2ODNkMjQ1ZjQ0YTc5OGZiMGUzOTNkYjQwYTRmKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNGY2YTk2NGNlYTI0Mzk2YjNiNGUxYzVjMTg2NjJhOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsIC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfN2VmNWYwMDZjZGQ2NGZlYzg0YzZhNTAwODUyMzkwNDEgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2VjZjZiMjM0OGMxNDRiM2ZhM2QwNTI5OTBhNDI1ZThmID0gJChgPGRpdiBpZD0iaHRtbF9lY2Y2YjIzNDhjMTQ0YjNmYTNkMDUyOTkwYTQyNWU4ZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+S2Vuc2luZ3RvbiBNYXJrZXQsIENoaW5hdG93biwgR3JhbmdlIFBhcmssIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfN2VmNWYwMDZjZGQ2NGZlYzg0YzZhNTAwODUyMzkwNDEuc2V0Q29udGVudChodG1sX2VjZjZiMjM0OGMxNDRiM2ZhM2QwNTI5OTBhNDI1ZThmKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9kNGY2YTk2NGNlYTI0Mzk2YjNiNGUxYzVjMTg2NjJhOS5iaW5kUG9wdXAocG9wdXBfN2VmNWYwMDZjZGQ2NGZlYzg0YzZhNTAwODUyMzkwNDEpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2M2NWUwMzE4NDZmYTQzZDFhZDQyZmFmM2RkNWJhNmIzID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksIC03OS40MDAwNDkzXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfMGExMTBhYzcyNWFkNDI1Nzg5MTExY2VjN2ZjMDVlZTMgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2RhNTUzZmRiZGFkMDQ1YTdiOTc5NjdhNTAwOGMwNjhjID0gJChgPGRpdiBpZD0iaHRtbF9kYTU1M2ZkYmRhZDA0NWE3Yjk3OTY3YTUwMDhjMDY4YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3VtbWVyaGlsbCBXZXN0LCBSYXRobmVsbHksIFNvdXRoIEhpbGwsIEZvcmVzdCBIaWxsIFNFLCBEZWVyIFBhcmssIENlbnRyYWwgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF8wYTExMGFjNzI1YWQ0MjU3ODkxMTFjZWM3ZmMwNWVlMy5zZXRDb250ZW50KGh0bWxfZGE1NTNmZGJkYWQwNDVhN2I5Nzk2N2E1MDA4YzA2OGMpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2M2NWUwMzE4NDZmYTQzZDFhZDQyZmFmM2RkNWJhNmIzLmJpbmRQb3B1cChwb3B1cF8wYTExMGFjNzI1YWQ0MjU3ODkxMTFjZWM3ZmMwNWVlMykKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWVjY2E4NDgzYTBmNGViZGEyZDBlNDQ4MTAzMTIxYTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Mjg5NDY3LCAtNzkuMzk0NDE5OV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2YzMWZkNGNjMzkwNDQxNmU4Y2VjNjAxY2M3N2RmOWY5ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF8zMzcwOGJjNTlmZTU0Nzg4OTAxYTFmYTAyMzc4ZDk1NyA9ICQoYDxkaXYgaWQ9Imh0bWxfMzM3MDhiYzU5ZmU1NDc4ODkwMWExZmEwMjM3OGQ5NTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNOIFRvd2VyLCBLaW5nIGFuZCBTcGFkaW5hLCBSYWlsd2F5IExhbmRzLCBIYXJib3VyZnJvbnQgV2VzdCwgQmF0aHVyc3QgUXVheSwgU291dGggTmlhZ2FyYSwgSXNsYW5kIGFpcnBvcnQsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfZjMxZmQ0Y2MzOTA0NDE2ZThjZWM2MDFjYzc3ZGY5Zjkuc2V0Q29udGVudChodG1sXzMzNzA4YmM1OWZlNTQ3ODg5MDFhMWZhMDIzNzhkOTU3KTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9lZWNjYTg0ODNhMGY0ZWJkYTJkMGU0NDgxMDMxMjFhNS5iaW5kUG9wdXAocG9wdXBfZjMxZmQ0Y2MzOTA0NDE2ZThjZWM2MDFjYzc3ZGY5ZjkpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcyODQ2M2Q1Njk5YjRkNDFhYTlkZWZjYzk5NjZhNzZiID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc5NTYyNiwgLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYTE1OWU2ZjlhMDNhNDJmNmFlZTgyMmZiMTkyOGMwYTcgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sX2U5NDY1MjMxZTc2OTRlMjhhOTEzNzg3OTc1OTM1Yjc4ID0gJChgPGRpdiBpZD0iaHRtbF9lOTQ2NTIzMWU3Njk0ZTI4YTkxMzc4Nzk3NTkzNWI3OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWRhbGUsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYTE1OWU2ZjlhMDNhNDJmNmFlZTgyMmZiMTkyOGMwYTcuc2V0Q29udGVudChodG1sX2U5NDY1MjMxZTc2OTRlMjhhOTEzNzg3OTc1OTM1Yjc4KTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl83Mjg0NjNkNTY5OWI0ZDQxYWE5ZGVmY2M5OTY2YTc2Yi5iaW5kUG9wdXAocG9wdXBfYTE1OWU2ZjlhMDNhNDJmNmFlZTgyMmZiMTkyOGMwYTcpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2NhN2ExMGI4MzM0OTQ1NDNiMWE3MTQ4ZWM0NGJlNWIyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ2NDM1MiwgLTc5LjM3NDg0NTk5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsICJjb2xvciI6ICJQdXJwbGUiLCAiZGFzaEFycmF5IjogbnVsbCwgImRhc2hPZmZzZXQiOiBudWxsLCAiZmlsbCI6IHRydWUsICJmaWxsQ29sb3IiOiAiIzMxODZjYyIsICJmaWxsT3BhY2l0eSI6IDAuOCwgImZpbGxSdWxlIjogImV2ZW5vZGQiLCAibGluZUNhcCI6ICJyb3VuZCIsICJsaW5lSm9pbiI6ICJyb3VuZCIsICJvcGFjaXR5IjogMS4wLCAicmFkaXVzIjogNiwgInN0cm9rZSI6IHRydWUsICJ3ZWlnaHQiOiAzfQogICAgICAgICAgICApLmFkZFRvKG1hcF8yZWI5ZDhmZDJjYjE0Y2UzYmEzZTY2ZTU2YjY1MmZlZCk7CiAgICAgICAgCiAgICAKICAgICAgICB2YXIgcG9wdXBfYjRhZTE0NjEwNDIxNDlhMWIyNDMyZDk5ZWU5MzQwOTQgPSBMLnBvcHVwKHsibWF4V2lkdGgiOiAiMTAwJSJ9KTsKCiAgICAgICAgCiAgICAgICAgICAgIHZhciBodG1sXzA3MDdkMzQzMzA3ODRjYWRhNWYxNGEzMzc2ZDA0MjlkID0gJChgPGRpdiBpZD0iaHRtbF8wNzA3ZDM0MzMwNzg0Y2FkYTVmMTRhMzM3NmQwNDI5ZCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3RuIEEgUE8gQm94ZXMsIERvd250b3duIFRvcm9udG88L2Rpdj5gKVswXTsKICAgICAgICAgICAgcG9wdXBfYjRhZTE0NjEwNDIxNDlhMWIyNDMyZDk5ZWU5MzQwOTQuc2V0Q29udGVudChodG1sXzA3MDdkMzQzMzA3ODRjYWRhNWYxNGEzMzc2ZDA0MjlkKTsKICAgICAgICAKCiAgICAgICAgY2lyY2xlX21hcmtlcl9jYTdhMTBiODMzNDk0NTQzYjFhNzE0OGVjNDRiZTViMi5iaW5kUG9wdXAocG9wdXBfYjRhZTE0NjEwNDIxNDlhMWIyNDMyZDk5ZWU5MzQwOTQpCiAgICAgICAgOwoKICAgICAgICAKICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2IyNmFkNWRmZjBiYTRjZWZhYjM3YjU1MzgwM2QyNzY5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY3OTY3LCAtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzc0M2VlNjU2NzJhOTQwNWE5ZjkxMTE0ZDkwODJkY2FlID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF85Y2UwZDAzZDVlYzk0YzRmODE4N2Q4ZTE2ZjIzODc4NiA9ICQoYDxkaXYgaWQ9Imh0bWxfOWNlMGQwM2Q1ZWM5NGM0ZjgxODdkOGUxNmYyMzg3ODYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duLCBDYWJiYWdldG93biwgRG93bnRvd24gVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF83NDNlZTY1NjcyYTk0MDVhOWY5MTExNGQ5MDgyZGNhZS5zZXRDb250ZW50KGh0bWxfOWNlMGQwM2Q1ZWM5NGM0ZjgxODdkOGUxNmYyMzg3ODYpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2IyNmFkNWRmZjBiYTRjZWZhYjM3YjU1MzgwM2QyNzY5LmJpbmRQb3B1cChwb3B1cF83NDNlZTY1NjcyYTk0MDVhOWY5MTExNGQ5MDgyZGNhZSkKICAgICAgICA7CgogICAgICAgIAogICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZWIzYWY3ZjFiODk3NDMwZTkwMGU3YzlmZmFhMDI1OTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg0MjkyLCAtNzkuMzgyMjgwMl0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwXzM2ZGI2ZTQ4Y2Q1NzQxNTQ4NzI0MWRjODE5NDVmOTk5ID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9iNjkwYTE1OTFmNDc0ZTI2OTNjZmM1MjQ5OWYxZWQ0MSA9ICQoYDxkaXYgaWQ9Imh0bWxfYjY5MGExNTkxZjQ3NGUyNjkzY2ZjNTI0OTlmMWVkNDEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZpcnN0IENhbmFkaWFuIFBsYWNlLCBVbmRlcmdyb3VuZCBjaXR5LCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwXzM2ZGI2ZTQ4Y2Q1NzQxNTQ4NzI0MWRjODE5NDVmOTk5LnNldENvbnRlbnQoaHRtbF9iNjkwYTE1OTFmNDc0ZTI2OTNjZmM1MjQ5OWYxZWQ0MSk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfZWIzYWY3ZjFiODk3NDMwZTkwMGU3YzlmZmFhMDI1OTMuYmluZFBvcHVwKHBvcHVwXzM2ZGI2ZTQ4Y2Q1NzQxNTQ4NzI0MWRjODE5NDVmOTk5KQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82ODBkYmM5Y2M4ZDU0ZDk2YTM5MzFkODNiNTFiZDc4ZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2NTg1OTksIC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7ImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLCAiY29sb3IiOiAiUHVycGxlIiwgImRhc2hBcnJheSI6IG51bGwsICJkYXNoT2Zmc2V0IjogbnVsbCwgImZpbGwiOiB0cnVlLCAiZmlsbENvbG9yIjogIiMzMTg2Y2MiLCAiZmlsbE9wYWNpdHkiOiAwLjgsICJmaWxsUnVsZSI6ICJldmVub2RkIiwgImxpbmVDYXAiOiAicm91bmQiLCAibGluZUpvaW4iOiAicm91bmQiLCAib3BhY2l0eSI6IDEuMCwgInJhZGl1cyI6IDYsICJzdHJva2UiOiB0cnVlLCAid2VpZ2h0IjogM30KICAgICAgICAgICAgKS5hZGRUbyhtYXBfMmViOWQ4ZmQyY2IxNGNlM2JhM2U2NmU1NmI2NTJmZWQpOwogICAgICAgIAogICAgCiAgICAgICAgdmFyIHBvcHVwX2RmYTA0MDZkNjc4OTQ1NGE5MmUxYWFiYmJhZGM2Y2FjID0gTC5wb3B1cCh7Im1heFdpZHRoIjogIjEwMCUifSk7CgogICAgICAgIAogICAgICAgICAgICB2YXIgaHRtbF9jMzI4NTk0MzEwYzQ0NmRjOGM4Yzk4Njk4YjhjNmViZSA9ICQoYDxkaXYgaWQ9Imh0bWxfYzMyODU5NDMxMGM0NDZkYzhjOGM5ODY5OGI4YzZlYmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNodXJjaCBhbmQgV2VsbGVzbGV5LCBEb3dudG93biBUb3JvbnRvPC9kaXY+YClbMF07CiAgICAgICAgICAgIHBvcHVwX2RmYTA0MDZkNjc4OTQ1NGE5MmUxYWFiYmJhZGM2Y2FjLnNldENvbnRlbnQoaHRtbF9jMzI4NTk0MzEwYzQ0NmRjOGM4Yzk4Njk4YjhjNmViZSk7CiAgICAgICAgCgogICAgICAgIGNpcmNsZV9tYXJrZXJfNjgwZGJjOWNjOGQ1NGQ5NmEzOTMxZDgzYjUxYmQ3OGQuYmluZFBvcHVwKHBvcHVwX2RmYTA0MDZkNjc4OTQ1NGE5MmUxYWFiYmJhZGM2Y2FjKQogICAgICAgIDsKCiAgICAgICAgCiAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9hMTc0MTZjMGE5ZTI0MmU2YmZjNjI0Yzk0OGNjNGJhNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Mjc0MzksIC03OS4zMjE1NThdLAogICAgICAgICAgICAgICAgeyJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwgImNvbG9yIjogIlB1cnBsZSIsICJkYXNoQXJyYXkiOiBudWxsLCAiZGFzaE9mZnNldCI6IG51bGwsICJmaWxsIjogdHJ1ZSwgImZpbGxDb2xvciI6ICIjMzE4NmNjIiwgImZpbGxPcGFjaXR5IjogMC44LCAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsICJsaW5lQ2FwIjogInJvdW5kIiwgImxpbmVKb2luIjogInJvdW5kIiwgIm9wYWNpdHkiOiAxLjAsICJyYWRpdXMiOiA2LCAic3Ryb2tlIjogdHJ1ZSwgIndlaWdodCI6IDN9CiAgICAgICAgICAgICkuYWRkVG8obWFwXzJlYjlkOGZkMmNiMTRjZTNiYTNlNjZlNTZiNjUyZmVkKTsKICAgICAgICAKICAgIAogICAgICAgIHZhciBwb3B1cF8xYjI4MWI3ZDBhNDc0MWZkOGNkZWUwNmVkYTM4NDFjMSA9IEwucG9wdXAoeyJtYXhXaWR0aCI6ICIxMDAlIn0pOwoKICAgICAgICAKICAgICAgICAgICAgdmFyIGh0bWxfYjFjYTBlY2YzZTE0NDcwMmJiZGZmMjJiYzM1ZjM4ODIgPSAkKGA8ZGl2IGlkPSJodG1sX2IxY2EwZWNmM2UxNDQ3MDJiYmRmZjIyYmMzNWYzODgyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CdXNpbmVzcyByZXBseSBtYWlsIFByb2Nlc3NpbmcgQ2VudHJlLCBTb3V0aCBDZW50cmFsIExldHRlciBQcm9jZXNzaW5nIFBsYW50IFRvcm9udG8sIEVhc3QgVG9yb250bzwvZGl2PmApWzBdOwogICAgICAgICAgICBwb3B1cF8xYjI4MWI3ZDBhNDc0MWZkOGNkZWUwNmVkYTM4NDFjMS5zZXRDb250ZW50KGh0bWxfYjFjYTBlY2YzZTE0NDcwMmJiZGZmMjJiYzM1ZjM4ODIpOwogICAgICAgIAoKICAgICAgICBjaXJjbGVfbWFya2VyX2ExNzQxNmMwYTllMjQyZTZiZmM2MjRjOTQ4Y2M0YmE1LmJpbmRQb3B1cChwb3B1cF8xYjI4MWI3ZDBhNDc0MWZkOGNkZWUwNmVkYTM4NDFjMSkKICAgICAgICA7CgogICAgICAgIAogICAgCjwvc2NyaXB0Pg== onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>


