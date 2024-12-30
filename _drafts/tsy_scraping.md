---
title:  "How to Scrape US Treasury Yield Curve XML Feed"
layout: post
---

The US Treasury offers multiple different ways to get daily yield curve rates, going back to 1990. While downloading csv files is easy, it’s a pain to have to redownload the data every time you want to update your research.  Thankfully, they offer an XML feed that is easy to scrape with Python. Web scraping / working with XML feeds isn't something I typically do, and so this was a fun and easy project to work on to better understand how to do this.  
Below are the two functions that I use to pull in the yield curve data. The first is a helper function, that pulls in yield curve data for a given year. The second function is the main function, where the user inputs the beginning and end date for when he or she wants the yield curve data. This function then uses the helper function to return yield curve data for the years specified in the given time period, and then filters down to the relevant dates.   
The code is fairly self-explanatory, but I wanted to give a general overview of what is going on. The helper function (get_yc) first makes the url by concatenating the base url part with the specified year (which must be greater than 1990), and then reads in the data using BeautifulSoup. I then initialize a list to store the data in. Next, I find all entries that are tagged “entry.” Each entry has a date, and then a set of tags that store the record of the yield curve for a given maturity. Not every year has every maturity (for example, 1990 doesn’t have record of 1 month rate) and so I insert NA if it’s not found. I append this to the list I initialized at the start, and then once the for loop goes through each date for the year, I convert the list into a Pandas data frame, which is what I return.  
The main function (hist_tsy_yield) lets the user input start and end dates, and it returns a data frame of daily yield curve data between those dates. It uses the helper function to pull in yield curve data for the years specified, and then filters down to the user specified time frame. I also have logic in here for converting the rates, which are stored as text, to numeric and handle the NA’s appropriately. I also use the dateutil package to parse dates correctly so that the user can use whatever format they want.  
While I’m using this just for the Par Yield Curve, the Treasury offers XML feeds for Daily Treasury Bill Rates (available from 2002 onwards), Daily Treasury Long-Term Rates (2000), Daily Treasury Par Real Yield Curve Rates	(2003) and Daily Treasury Real Long-Term Rates (2000). These XML feeds follow a similar structure and so this code should be easy to update for those other time series.  
Overall, while analyzing the yield curve data is more interesting, I did find this to be a fun exercise to work on because I had never scraped any data before. It gave me a different appreciation for programming, and for Python itself. Typically I use R, but I couldn’t really figure out how to do it with the XML packages available. BeautifulSoup on the other hand made it quite easy. In the future, I want to use this to do analysis on the yield curve using PCA and Vector Autoregressions. I could have done that by downloading bunch of csv’s, but that wouldn’t be as clean. Hopefully this is useful.

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

def get_yc(year):
    if year < 1990:
        print("Choose Year greater than 1990.")
        return None

    # pull in data, read namespace
    url = f"https://home.treasury.gov/resource-center/data-chart-center/interest-rates/pages/xml?data=daily_treasury_yield_curve&field_tdr_date_value={year}"
    response = requests.get(url)
    xml_data = BeautifulSoup(response.content, 'xml')

    all_data = []  # list to hold all the row data

    #  get each bond yield, insert 'n/a' if not found
    entries = xml_data.find_all('entry')
    for entry in entries:
        date = entry.find('d:NEW_DATE').text[:10]
        data = {'Date': date}

        for tag in ['BC_1MONTH', 'BC_2MONTH', 'BC_3MONTH', 'BC_4MONTH', 'BC_6MONTH', 'BC_1YEAR', 'BC_2YEAR', 'BC_3YEAR', 'BC_5YEAR', 'BC_7YEAR', 'BC_10YEAR', 'BC_20YEAR', 'BC_30YEAR']:
            result = entry.find(tag)
            data[tag] = result.text if result else pd.NA

        all_data.append(data)

    # convert to DataFrame
    yc_df = pd.DataFrame(all_data)
    return yc_df
```

```python
from dateutil import parser


def hist_tsy_yield(start_date, end_date):

  # confirm that start_date and end_date are dates
  try:
    parser.parse(start_date)
    pass
  except ValueError:
    return "Provide Valid Date"

  try:
    parser.parse(end_date)
    pass
  except ValueError:
    return "Provide Valid Date"

  # check that start_date is before end_date

  if parser.parse(start_date) > parser.parse(end_date):
    return "Start date needs to be older than end date"

  # get years

  start_year = parser.parse(start_date).year
  end_year = parser.parse(end_date).year

  years = list(range(start_year, end_year+1))

  year_df = pd.DataFrame()

  for i in years:
      df = get_yc(i)
      year_df = pd.concat([year_df, df], ignore_index=True)

  # filter data to between selected dates
  fmt_start_date = parser.parse(start_date).strftime("%Y-%m-%d")
  fmt_end_date = parser.parse(end_date).strftime("%Y-%m-%d")

  filter_df = year_df[(year_df['Date'] >= fmt_start_date) & (year_df['Date'] <= fmt_end_date)]

  # convert the rest of the columns to numeric, coerce errors to NaN
  for column in filter_df.columns[1:]:
      filter_df[column] = pd.to_numeric(filter_df[column], errors='coerce')

  return filter_df.reset_index(drop=True)
```

