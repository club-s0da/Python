```python
# WEB SCRAPING
# BeautifulSoup and Requests (a beginner package for web scraping)

from bs4 import BeautifulSoup
import requests

# Using wikipedia, LIST of LARGEST COMPANIES in the US by REVENUE:
url = 'https://en.wikipedia.org/wiki/List_of_largest_companies_in_the_United_States_by_revenue'

# use requests to request access from server and assign to a variable, page:
page = requests.get(url)

# Call BeautifulSoup to return the page's text, as well as define what it will parse:
# Assign it to a variable:
soup = BeautifulSoup(page.text, features="html.parser")

# Note there are four tables in this page, we are going for the second one.
# This returns ALL tables, EACH table be an item in a list:
soup.find_all('table')

# OUR table is (the second one), at index position 1:
soup.find_all('table')[1]  # Returning the table by its index in the list of tables
# same as:
soup.find('table', class_="wikitable sortable")  # Returning the table by referencing its class

# Assign the table to a variable:
table = soup.find('table', class_="wikitable sortable")

# Now pull all the 'th' tags from table
# Assign them a variable:
headings = table.find_all('th')

# Now LOOP through the headings:
"""
column_names = []  # Create an empty list to store the results
for columns in headings:
    column_names.append(columns.text.strip())
"""

# Rewritten as LIST COMPREHENSION:
column_names = [columns.text.strip() for columns in headings]

# Note: Will not be able to use strip() AFTER it becomes items in a list (only while you're iterating through it)

# import pandas so it's ready to go
# put column headings in to start

import pandas as pd

# Use pandas function DataFrame() to bring in column names:
# Assign the dataframe to a variable:
df = pd.DataFrame(columns=column_names)

print(df)

# Now pull in the data from the rest of the DataFrame:
# Looks like it's td tags encapsulated in tr tags:
# Looks like it's t for table and then r for rows, d for data

# Loop through our column data:
column_data = table.find_all('tr')
for row in column_data[1:]:  # Error in the index at 0, empty list, must begin at index[1]
    row_data = row.find_all('td')
    indv_row_data = [data.text.strip() for data in row_data]
    # Now we have to put these data into our dataframe ONE AT A TIME
    # Append each row to the dataframe each time it loops through
    length = len(df)
    df.loc[length] = indv_row_data

print(df)  # Checking if it's right

# Now can be analyzed further in pandas
# But we can export it to a csv:
df.to_csv(r'C:\Users\Me\Desktop\Revenues.csv', index=False)
# added: index = False to omit the first column which shows the index value
```
