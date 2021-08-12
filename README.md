# PDGA_Course_Analysis

## Title: 
The Wonderful World of Disc Golf

-------------------------------------------------------------------------


## Overview:

Hello and welcome to my capstone. I built this to help everyday users find and learn more about local courses in their areas. This sport is a great way to spend time with friends, family, or spend time alone with your thoughts. It is a great sport that gets you away from distractions and having fun no matter how good or bad you might be. If you have every thought of trying out this sport I hope this tool can help you find some answers and give you the necessary information needed to start having some fun.

-------------------------------------------------------------------------

## Technologies Used:

Python

PDGA - API

Google Slides

PowerBI

In collecting the data and making sure it would work I used Python to pull API data, Google slides to build the presentation, and PowerBI to create the dashboard. I needed to add visualations on PowerBI to get the cluster map to work properly. I downloaded MapBox to complete this task. I speak more on this later on.

------------------------------------------------------------------

## Instructions on pulling the API Data:

When first starting you will need to obtain permission from the PDGA contact center to obtain their API's. This link will take you to their site and you just need to follow the instructions (https://www.pdga.com/dev). Luckily although there are multiple API's that you can choose from you only need permission from the PDGA once and you gain access to them all. When you have received access and created your account with a username and password you will need to keep those written down for python. When in Python you will need to log in using the instruction from the PDGA website to know what paramaters and endpoint to pull. I have added examples of this code below. Again all of this is done in Python. Also worth noting this was all done in the geospatial portion of Anaconda.

## Imports:
import requests

import pandas as pd

import geopandas as gpd

import matplotlib.pyplot as plt

import folium

from folium.plugins import MarkerCluster

from folium.plugins import FastMarkerCluster

from shapely.geometry import Point

from pandas.io.json import json_normalize

%matplotlib inline

## Log in code:
endpoint_1 = 'https://api.pdga.com/services/json/user/login'
params = {'username':'your_username','password':'your_password'}
response = requests.post(endpoint_1, data = params)
response

If you get the <Response 200> code then you are logged in and good to go. If not then double check you have everything typed in correctly and that the endpoint is correct.

Ater this you will want to pass the code - res = response.json() and then the code - print(res). This will give you your sessid and session_name. You will need these for the next part as your headers to pull the info. You will get new ID's everytime you run the print(res) code so try t watch out for that as you will need to update furture code or you might get stopped.

## Pull Data from API with Parameters:

The next set of code I am adding below is how to pull from specifics from the API based on parameters. It is worth noting that the initial pull limit per set is only 10 however you can up this to 200 as I have done below. My params are set to only look at players in the US and to showcase 200 at a time.

endpoint_2 = 'https://api.pdga.com/services/json/course'
headers = {'Cookie':'SSESSf1f85588bb869a1781d21eec9fef1bff=3Y21Sx3qx3ujLmrbZHqc18099V0D2iMpn5QX3za7PIM'}
params = {'country':['US'],'limit':[200]}
response = requests.get(endpoint_2, headers = headers, params = params)
res = response.json()

This is great however we have only 200 responses with this request and there are over 7,000 courses in total. Due to the limitations of the API a for loop was needed to pull them all and create a dataframe that could be used. THe below code was ussed to do just this. 

all_items = []

main_df = pd.DataFrame()

for i in range(0,36):
    url = 'https://api.pdga.com/services/json/course'
    offset = i * 200
    params = {'country':['US'],'limit':[200],'offset':[offset]}
    response = requests.get(url, headers=headers, params=params).json()
    all_items.append(response)
    df = pd.json_normalize(response['courses'])
    main_df = main_df.append(df)
main_df

What the above code will do is loop through and take 200 courses at a time and append the next 200 courses to the previous set until it has pulled them all into a new clean dataframe. This is accomploshed through the offset portion of the code. In a typical range for loop you would be selecting the number of pages to pull from and build together. Instead this is saying to start at index 0 and then complete the below for loop 36 times and append the new set of 200 to the previosuly obtained one. Your index will be 36 sets of 0 to 199. You can reset index after if you want and this will fix that issue. After this I then made the dataframe a CSV file for easy use in PowerBI. 

This process was used to obtain all of the relevant data used in my presentation and even a little extra as far as player stats and locations.

------------------------------------------------------------------

## Cluster Mapping in Python

I attempted to create a user friendly cluster map in Python however in every attemp to create this tool my system would crash or take well over 30 minutes to pull up just over half of the ping points created. My options were to scope back my project or find another way of making this happen. Even though this path is not viable I have left the code used to build this visual as well as a standard point graph but do not recommend running either one other than to see that it works and at the same time does not. I have also attached the HTML's built to try and find a faster way to bring up the data and still had the same issues.

------------------------------------------------------------------

## PowerBI

In PowerBI I built out the user friendly dashboard that shows on the Mapbox visualization. USing this tool you can put in latitude and longitude info to set up the cluster map but it still needs something to count and cluster. In building this out I used the course ID as a counter. After this is built you can change out the radius but this tool will not let you dive deeper into where each ping represents. The best way to use the map is to utilize the slicer on the right and select your city and state. Doing this will show all courses in those areas. You can also see the parks at multiple locations by holding control and clicking on the different parks, cities, and states. At the bottom of the states slicer are two multi row card visualizations. The top one will show you the name and address of the course or courses selected. The bottom one will give you a breakdown of known park/course details. When selecting the courses if multiple are selected it will go alphabetical in the multi row cards. So please watch out for that.

## Closing Thoughts

Thank you for your interest in my capstone. If you have any quetions or want to know more about my work please feel free to connect with me on linkedin @ https://www.linkedin.com/in/scott-moore-a033a013b/