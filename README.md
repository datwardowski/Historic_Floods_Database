# Extracting Historic Flood Information from News Sources: A collaboration between New Light Technologies and General Assembly's Data Science Immersive Fellows

New Light Technologies (NLT) is an organization founded in 2003 which provides comprehensive information technology solutions for clients in government, commercial, and non-profit sectors, including FEMA (the Federal Emergency Management Agency), the U.S. Census Bureau, and The World Bank.



Our team for this project is  Payal Chodha, Alicja Ligas, Beth Padera, and Dominika Twardowski.

## Problem Statement

Historically, floods have caused significant economic damage and human casualties in developing cities. To help governments better understand geographical exposure to floods, there is a need for a product that automatically compiles useful information about historic events. As a result, we chose to work on a tool that scans online news sources and extracts data about historic floods in Punjab, India.

Floods are a severely underestimated natural disaster. In fact, they account for almost half of all natural disasters. They have a detrimental impact on human quality of life (health, nutrition, livelihood) particularly in rural areas that are heavily agricultural. Nearly one third of the world’s entire population has been affected by disastrous floods between 1994 and 2013.
Floods remain a major cause of natural disasters in India. In fact, mortality rates tied to floods have actually been on the rise in India despite dropping in the rest of the world. 

This year alone, over a million people lost their homes and had to move to relief camps due to severe floods.

### Directory Links

*  [News-Sources-Gathering-Cleaning-and-Combining](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/News%20Sources%20-%20Gathering%2C%20Cleaning%2C%20Combining)
* [Data](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/data)
* [Weather](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/Weather)
* [Econ](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/Econ)

# Process Overview

As we set about the project, the first thing we did was think about the end users - governments, businesses and citizens. We tried to get in the mindset of their pain points and decision process about current floods or planning for future floods. We then came up with some big ideas, that naturally grouped into three areas - the data that we wanted to collect, the geography we wanted to cover (developing countries), and how we wanted to surface the data to our end users.  
Our process reflects these idea groups. We started with data acquisition, gathering data from online news sources, weather source, and economic data. We then embarked upon data cleaning and processing, which included Natural Language Processing to identify meaningful text, as well as Time-Series analysis on the numerical weather data.   
Then we addressed how we wanted to surface the data to our users, which might include varying levels of stakeholders. Potentially lighter users might just want to get a quick snapshot of the data, while others might want to query and extract from the entire corpus we collected. We ended up creating a SQL query tool for deep analysis, and a Tableau Public dashboard for a quick longitudinal overview.

## Data Sources

The data sources we found most helpful for the online news sources were: ReliefWeb, which aggregates distaster related new stories across the globe; India Tribune, a national paper in India; Dainick Tribune and other regional sources (which needed translation), local Punjab news sources.

Since most floods start with precipitation, we wanted to capture daily weather data for our selected region (the 22 districts in Punjab) in our allocated time span (10 years), to help us narrow down the range of dates for possible flood events. While daily historical weather data is available in the U.S. from government sources, in India daily historical weather data by district is available through the government, but for a fee. Therefore we leveraged a prior business relationship to obtain a trial API from The Weather Company to gather this data.

We also found freely available economic impact data for the 22 districts in Punjab through the Open Government Data Platform for India - which provided annual counts of population affected, lives lost, houses damaged and value, livestock lost and crop damaged and value. 
We wanted to show at least a 10-year time period in history, and since the most recent year in the economic data we collected was 2017, we decided to target the time period of 2008 - 2017. 

## Pulling Data, Cleaning, & Analysis

### Online News Data
Our main source of data was using the Python package BeautifulSoup to collect the text from the articles we found across the multiple online data sources. 
* All of the text gathered from The Dainick Tribune had to be translated from the regional dialect to English. This was done using the TextBlob package in Python. Once all of our source text was in the same language, it was combined into one CSV. The final CSV was formatted to include the article URL, publishing date, and a combination of the title and article text. This was checked for duplicate rows and missing values. Duplicate rows occurred when articles from the same source appeared in multiple searches or an article was republished. A single null value appeared for an article whose URL BeautifulSoup request did not process properly. The single null value was dropped, and any duplicated articles were dropped as well.
* We started thinking about how we might be able to extract meaningful information from the text gathered. We decided to focus on identifying sentences that gave statistical information on the affects a flood has on Punjab. To ensure the text was parsed into clean sentences, line breaks were replaced with spaces. The text was split into a sentence wherever a period was followed by anything other than a number. This method was used because some of the text we had collected had dates which separated month and day using a period. 
* Each sentence was saved into a new CSV that had three columns: URL(describing the origins of the sentence), date, and text (combintation of the the articles title with the whole text body).  This was accomplished by using Regex and Pandas. Next, we began to categorize the data by subject matter. By reading the articles we were able to recognize words that frequently appeared in sentences describing economic and social affects of a flood in Punjab. Some examples of words that we found are:  'drown', 'mortality', 'rescue', 'displace', and 'house'.
* Given our findings, we decided to group sentences based on four categories: human lives affected, deaths that occurred, agricultural impact and infrastructural devastation. This was done via a key word search. We wanted to filter through sentences regardless of punctuation and case, so we set all text to lowercase. Next, we ran the corpus through four for loops. Each for loop checked to see if a sentence contained a numeric word or  numeric value (ie. 'twenty) and also if it contained a keyword associated with one of the four categories. 
* Lives affected searched for words such as 'relief', 'wound', 'displace', or 'cost'.  We tried to identify any human expenses, losses, and help that might have come to the people affected by the flood. Deaths searched for sentences pertaining to any information to how and how many lives were lost during or due to a flood. Agriculture was an important subject to explore because Pubjab’s a large agricultural region. The state is considered the bread basket of India. Any losses to crops and livestock the state experiences due to floods, has an economic impact on the entire country. Infrastructure refers to any damages that occurred to roads, bridges, dams, or houses. This category stressed any physical structure that once damaged could affects public safety. 

### Weather Data
From The Weather Company Cleaned Historical trial API, we pulled 2008 - 2019 (Oct) daily rainfall, runoff, and soil moisture for 22 disticts (lat /long). We then leveraged time-series analysis to find the start and end dates of potential floods for the 4-month monsoon season. Days averages were compared to seasonal averages and “potential flood day” were noted if any day’s rainfall exceeded 10% of the monthly monsoon average. We also found non-monsoon extreme events exceeding 5cm of rainfall per day, and classified them as “potential flood days.” Then we put theses “potential flood days” into ranges for each district and location and summed the total precipitation and runoff over each range. We created a csv of this output which included the year, district, potential flood start/end dates, sum of precipitation, and runoff over the date range.

### Economic Data
The economic data gathered was already in tabular format as an annual timescale, so we just combined the different tables into one and merged this with the weather data table.

# Interactive Tool

## Framework
Flask is a micro web framework written in Python. It is classified as a microframework because it does not require particular tools or libraries. It has no database abstraction layer, form validation, or any other components where pre-existing third-party libraries provide common functions.

## Database Engine
SQLite is a C-language library that implements a small, fast, self-contained, and full-featured SQL database engine. SQLite is the most commonly used database engine in the world.
 * The Front end was designed mostly in HTML with some javascript.
 * The database was created in SQLite. While Flask was used to build a connection between the webapp and the datbase in SQLite.
 * The database has the final csv file, which contains location, date, URL, and topic (search keywords).
 * The tool is able to fetch records from the csv by providing a particular location (neighborhood in Punjab) and a keyword.
 * The Tableau Dashboard is also embeded in the tool. The Tableau Dashboard shows a map of the Punjab region with all the districts. The  weather, economic, and news data can all be  fetched by clicking on the map or selecting a specific keywork, region, or date range. [Direct Link to Tableau Dashboard](https://public.tableau.com/shared/3CQNSQ7MZ?:display_count=y&:origin=viz_share_link)
 
 ## Demonstration of the tool
 
  ![grab-landing-page](https://github.com/datwardowski/Historic_Floods_Database/blob/master/app-final-gif.gif)
 
## Challenges
* One of the challenges we dealt with was the difficulty in find news sources which documentated flood events a couple years back. As our searches went back in time, less information was available online relating to flood events; because of this,   our data is unevenly distributed in relation to the dates of flood events.
* Another challenge we dealt with was trying to verify the articles we did find — we did find a potentially meaningful source in the India Meteorological Department (IMD). However, the data needs to be purchased. 

## Moving forward
 * Having a paid version of a news API would have helped acquire more online data sources as well as going further back in time. 
* Expanding beyond Punjab to other regions but also continue growing the dataset about Punjab, India so it continues to be relevant and informative. This could be done by setting up a scheduled script (ie CRON) to take in all news posts on a certain schedule.
* If we had more time we would have manually read through the sentences and verify if they were categorized correctly through the keyword lists and if they were meaningful. This would help make sure the correct information is being displayed. Some of the sentences and articles were checked manually. Manually reading through all the sentences is a time and labor-intensive task since it currently has to be done by human inspection. 
* Further work could include categorizing the sentences by how important they are in the information they provide — a model could potentially be built to classify sentences as relevant vs non-relevant to the category they are in and thus show how important the information provided is. This could be another filter added to the SQL database as a way to organize the sentences (currently, the sentences are organized from most recent to older).

### Further Improvements for the Web App
 * Deploy as a stand-alone app
 * Collaborate with UX/Web-dev teams to enhance user experience
 * SQLite is not that robust but is portable, could consider porting it to other more robust engines like postgreSQL.
 
### Extracting Historic Flood Information from News Sources: [Slide-Deck](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/blob/master/Client%20Project%20-%20Flood%20.pdf)
