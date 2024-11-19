---
title:  "How to Scrape US Treasury Yield Curve XML Feed"
layout: post
---

The US Treasury offers multiple different ways to get daily yield curve rates, going back to 1990. While downloading csv files is easy, it’s a pain to have to redownload the data every time you want to update your research.  Thankfully, they offer an XML feed that is easy to scrape with Python.  

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
