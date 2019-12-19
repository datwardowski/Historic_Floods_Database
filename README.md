# Extracting Historic Flood Information from News Sources: A collaboration between New Light Technologies and General Assembly's Data Science Immersive students.

New Light Technologies (NLT) is an organization founded in 2003 which provides comprehensive information technology solutions for clients in government, commercial, and non-profit sectors, including FEMA (the Federal Emergency Management Agency), the U.S. Census Bureau, and The World Bank.



Our team at General Assembly for this project is  Payal Chodha, Alicja Ligas, Beth Padera, Dominika Twardowski.

# Problem Statement

Historically, floods have caused significant economic damage and human casualties in developing cities. To help governments better understand geographical exposure to floods, there is a need for a product that automatically compiles useful information about historic events. As a result, we chose to work on a tool that scans online news sources and extracts data about historic floods in Punjab, India.

Floods are a severely underestimated natural disaster. In fact, they account for almost half of all natural disasters. They have a detrimental impact on human quality of life (health, nutrition, livelihood) particularly in rural areas that are heavily agricultural. Nearly one third of the world’s entire population has been affected by disastrous floods between 1994 and 2013.
Floods remain a major cause of natural disasters in India. In fact, mortality rates tied to floods have actually been on the rise in India despite dropping in the rest of the world. 

This year alone, over a million people lost their homes and had to move to relief camps due to severe floods.

# Directory Links

*  [News-Sources-Gathering-Cleaning-and-combining](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/News%20Sources%20-%20Gathering%2C%20Cleaning%2C%20Combining)
* [Data](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/data)
* [Weather](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/Weather)
* [Econ](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/tree/master/Econ)

# Process Overview

As we set about the project, the first thing we did was think about the end users - governments, businesses and citizens. We tried to get in the mindset of their pain points  and decision process about current floods or planning for future floods. We then came up with some big ideas, that naturally grouped into three areas - the data that we wanted to collect, the geography we wanted to cover (developing countries), and how we wanted to surface the data to our end users. Our process reflects these idea groups. We started with data acquisition, gathering data fro online news sources, weather and economic impacts of floods. We then embarked upon data cleaning and processing, which included Natural Language Processing to identify meaningful text, as well as Time-Series analysis on the numerical data. Then we addressed how we wanted to surface the data to our users, which might include varying levels of stakeholders. Potentially lighter users might just want to get a quick snapshot of the data, while others might want to query and extract from the entire corpus we collected. We ended up creating a SQL query tool for deep analysis, and a Tableau Public dashboard for a quick longitudinal overview.

# Data Sources

The data sources we found most helpful for the online news sources were: ReliefWeb, which aggregates distaster related new stories across the globe; India Tribune, a national paper in India; Dainick Tribune and other regional sources (which needed translation), local Punjab news sources.

Since all floods start with the water falling from the sky, we wanted to capture daily weather data for the 22 districts in Punjab for our time span, to help us narrow down the range of dates for possible flood events. While daily historical weather data is available in the U.S. from government sources, in India daily historical weather data by district is available through the government, but for a fee. Therefore we leveraged a prior business relationship to obtain a trial API from The Weather Company to gather this data.

We also found freely available economic impact data for the 22 districts in Punjab through the Open Government Data Platform for India - which provided annual counts of population affected, lives lost, houses damaged and value, livestock lost and crop damaged and value. 
We wanted to show at least a 10-year time period in history, and we since the most recent year in the economic data we collected was 2017, we decided to target the time period of 2008 - 2017. We ended up pulling data from 2018 through October 2019 as well.

# Pulling Data, Cleaning & Analysis

## Online News Data
Our main source of data was using the Python package BeautifulSoup to collect the text from the articles we found across the multiple online data sources. 
* All of the text gathered from The Dainick Tribune had to be translated from regional dialect to English. This was done using the TextBlob package in Python. Once all of our source text was in the same language, it was combined into one CSV. The final CSV was formatted to include the article URL, publishing date, and title and article text combined into one column, text. This was checked for duplicate rows and missing values. Duplicate rows occurred when articles from the same source appeared in multiple searches. A single null value appeared for an article whose URL BeautifulSoup request did not process properly. The single null value was dropped, and anyduplicated articles were dropped as well.
* We started thinking about how we might be able to extract meaningful information from the text gathered. We decided to focus on identifying sentences that gave statistical information on the affect a flood had on Punjab. To ensure the text was parsed into clean sentences, line breaks werereplaced with spaces. The text was split into a sentence wherever a period was followed by anything other than a number. This method was used because some of the text we had collected had dates which separated month and day using a period. 
* Other text collected did not have a space in between sentences. Each sentence was saved into a new CSV that similarly, had three columns describing the origins of the sentence: URL, date its source article was published and text. This was done using Regex and Pandas. Next, we began to categorize the data by subject matter. By reading the articles we were able to recognize words that frequently appeared in sentences describing economic and social affects a flood had on Punjab. Some examples of words that we found are: lives, damages, livestock, crops, villages etc. 
* Given our findings, we decided to group sentences based on four categories: human lives affected, deaths that occurred, agricultural impact and infrastructural devastation. This was done via a key word search.We wanted to filter through sentences regardless of punctuation, so we converted the text to lowercase. Next, we ran the corpus through four for loops. Each for loop checked to see if a sentence contained a numeric word or value and checked if it contained a key word associated with one of the four categories. Lives affected searched for words like ‘relief’, ‘aid’ and ‘cost’. 
* We tried to identify any human expenses, losses, and help that might have come to the people affected by the flood. Deaths searched for sentences pertaining to any information to how and how many lives were lost during or due to a flood. Agriculture was an important subject to explore because Pubjab’s large farming population. The state is considered the bread basket of India. Any losses to crops and livestock the state experiences due to floods, has an economic impact on the entire country. Infrastructure refers to any damages that occurred to roads, bridges,dams, sewage, houses or any physical structure that once damaged, affects public safety. 

## Weather Data
From The Weather Company Cleaned Historical trial API, we pulled 2008 - 2019 (Oct) daily rainfall, runoff, soil moisture for 22 disticts (lat /long). We then leveraged time-series analysis to find start / end dates of potential floods for 4-month monsoon season compared to seasonal averages and considered a day a “potential flood day” if any one day’s rainfall exceeded 10% of the monthly monsoon average. We also found non-monsoon extreme events exceeding 5cm of rainfall per day, and classified them as “potential flood days.”  We then put the “potential flood days” into ranges for each district and location and summed the total precipitation and runoff over the range. We created a csv of this output which included year, district, potential flood start / end dates, sum of precipitation and runoff over the date range.

## Economic Data
The economic data gathered was already in tabular format at an annual timescale, so we just combined the different tables into one and merged with the weather data table.

# Interactive Tool

## Framework
Flask is a micro web framework written in Python. It is classified as a microframework because it does not require particular tools or libraries. It has no database abstraction layer, form validation, or any other components where pre-existing third-party libraries provide common functions.

## Database Engine
SQLite is a C-language library that implements a small, fast, self-contained, high-reliability, full-featured, SQL database engine. SQLite is the most used database engine in the world.
 * The Front end was designed in HTML and some of the javascript was used.
 * The database was created in SQLite, and flask was used to build a connection between the webapp and the datbase in SQLite.
 * The database has the csv file, which has location, date, sources, topic (search keywords).
 * The tool is able to fetch the records from csv according to a particular location(neighborhood in Punjab) and a particular keyword.
 * The Tableau Dashboard is also embeded in the tool, which shows the map for weather and economic data and fetches all the text data accordingly and shows it visually. [Direct Link to Tableau Dashboard](https://public.tableau.com/shared/3CQNSQ7MZ?:display_count=y&:origin=viz_share_link)
 
 # Demonstration of the tool
 
  ![grab-landing-page](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/blob/master/app-final-gif.gif)
 
# Challenges
* Inability to find news sources documentation flood events before.
  some of the challenges presented in accomplishing this task was finding pertinent news articles published in the last ten   years. As our searches went back in time, less information was available online relating to flood events; because of this,   our data is unevenly distributed in relation to the dates which floods occurred
* Trying to verify the articles we did find— we did find a potentially meaningful source in the India Meteorological      Department (IMD). However, the data needs to be purchased. 

# Moving forward
 * Having a paid version of a news API would have helped acquire more online data sources as well as going further back in time. 
* Expanding beyond Punjab to other regions but also continue growing the dataset about punjab india so it continues to be relevant and informative.This could be done by setting up a scheduled script (ie CRON) to take in all news posts on a certain schedule.
* If we had more time we would have manually read through the sentences and verify if they were categories correctly through the keyword lists.  This would help make sure the correct information is being displayed. Spend more time to manually checking all the articles if they made sense to be initially pulled ( some sentences and articles were checked manually) This is a time and labor-intensive task since this currently has to be done  by human inspection. 
* Further work could include categorizing the sentences by how important they are in the information they provide — a model could potentially be built to classify sentences as relevant vs non-relevant to the category they are in and then therefore show how important the information they provide is. This could be another filter added to the SQL database as a way to organize how the sentences are displayed(first would be dates).

# Further Improvements for the Web App
 * Deploy as a stand-alone app
 * Collaborate with UX/Web-dev teams to enhance user experience
 * SQLite is not that robust but is portable, could consider porting it to other more robust engines like postgreSQL.
 
# Extracting Historic Flood Information from News Sources [Slide-Deck](https://git.generalassemb.ly/bethpadera-GA/CHI-DSI9-TEAM-FLOOD/blob/master/Client%20Project%20-%20Flood%20.pdf)
