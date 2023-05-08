# Browsing Websites and Data in HTML

# Overview

For this project, I was asked to browse the HTML script of two websites to return two deliverables. The first website (https://static.bc-edx.com/data/web/mars_news/index.html)
featured several news articles about Mars and its exploration. Using Selenium WebDriver and Beautiful Soup, I returned a list of news articles, their
titles, and summaries. The second website (https://static.bc-edx.com/data/web/mars_facts/temperature.html) featured a table of weather data on Mars,
over the course of several terrestrial and Martian years. Using the same tools, I parsed the data from the website and analyzed it to answer several
questions about trends in the Martian climate and how time passed on Mars, in comparison to Earth.

# Features

The code in both deliverables features many of the fundamentals of parsing HTML script in a Jupyter Notebook, though they are applied differently to
account for the differences between the websites -- one of which simply contains a list of news articles, and the other which contains weather data
in a table. Both pieces of code start in similar ways, setting a website path for the webdriver, then parsing the HTML script in Beautiful Soup. 

In the deliverable that deals with a news site, the code then parses each individual article, before parsing the titles and article descriptions
featured on the webpage. Lists of titles and articles are displayed under the cell in which this is done. 
  
    news_articles = soup.find_all('div', class_='list_text')
    news_title = soup.find_all('div', class_='content_title')
    news_preview = soup.find_all('div', class_='article_teaser_body')
    print(news_title)
    print(news_preview)

Next, a 'for' loop joins each article title and preview together in a list of several dictionaries for each article, as is displayed below:

    for article in news_articles:
        title_element = article.find('div', class_='content_title')
        if title_element:
            title = title_element.text.strip()
        preview_element = article.find('div', class_='article_teaser_body')
        if preview_element:
            preview = preview_element.text.strip()
        article_dict = {'title': title, 'preview': preview}
        titles_and_previews.append(article_dict)
       
The code dealing with a table of weather data is a bit more intensive, although it uses many of the same tools and loops that would be familiar
to regular Python users. First, the HTML script is parsed according to "data-rows". Knowing that this data would need to be placed into a DataFrame
for further analysis, the following 'for' loop then appended all columns and their respective data points to a list:

    # Create an empty list
    row_list = []
    # Loop through the scraped data to create a list of rows
    for row in data_rows:
        values = []
        for cell in row.find_all('td'):
            values.append(cell.text.strip())
        row_list.append(values)  

A DataFrame is created, using Pandas, and formatted with correct column names and data types to prepare the data for analysis. The analysis for 
several different types of weather are displayed below, using Matplotlib to create visualizations and several simple statistical formulas to find
the required data points. The most complicated analysis is performed in the last cell of the analysis to answer the following question: 
"How many terrestrial (earth) days are there in a Martian year?" 

To answer this question, I decided to use the 'ls' column from the DataFrame I created, since it measured the solar latitude of Mars. I assumed that
Mars would be at a certain position in relation to the sun for two or more days annually, so if I could find the first instance of a certain solar latitude
each year, I could find the end of the previous year and the start of the new Martian year. 

To begin, I created an empty list to track all of the first instances of Mars being at a solar latitude of zero degrees. Another variable, year_started, was set as a boolean, 
so that when the same solar latitude was found during the same year, it would be skipped. Each year, the terrestrial date of the first instance of the
solar latitude being zero degrees was appended to a list. Finally, using Datetime, the first object in the appended list was subtracted from the second object to
find the number of terrestrial days between the solar latitude becoming zero degrees. The final result was 687 terrestrial days. The code for this cell
is displayed below:

    ls_0_dates = []
    for index, row in mars_df.iterrows():
        # Check if this is the first row of a new terrestrial year
        if current_year != row['terrestrial_date'].year:
            current_year = row['terrestrial_date'].year
            year_started = False

        # Check if this is the first occurrence of row['ls']=0 for this year
        if row['ls'] == 0 and not year_started:
            ls_0_dates.append(row['terrestrial_date'].strftime('%Y-%m-%d'))
            year_started = True

        # Check if we've already found the first occurrence of row['ls']=0 for this year
        if year_started and row['ls'] == 0:
            continue

    print(ls_0_dates)

    #Subtract first two values of this list
    year_length = (dt.datetime.strptime(ls_0_dates[1], '%Y-%m-%d') - dt.datetime.strptime(ls_0_dates[0], '%Y-%m-%d')).days
    print("The length of a martian year is", year_length, "terrestrial days, judging by the solar latitude of Mars.")


# Instructions

Ensure that the following libraries and dependencies are installed on your local machine and in your Jupyter Notebook:

    from splinter import Browser
    from bs4 import BeautifulSoup
    import requests
    import matplotlib.pyplot as plt
    import pandas as pd
    from selenium import webdriver
    from selenium.webdriver.chrome.service import Service
    import datetime as dt
    
Otherwise, open the code in a new Jupyter Notebook and run the code.


# Acknowledgements
The data used in this code was sourced by the OSU Data Visualization and Analytics Bootcamp from:
The Mars News websiteLinks to an external site. is operated by edX Boot Camps LLC for educational purposes only. The news article titles, summaries, dates, and images were scraped from NASA's Mars NewsLinks to an external site. website in November 2022. Images are used according to the JPL Image Use PolicyLinks to an external site., courtesy NASA/JPL-Caltech.

# Author
Daniel Adamson


