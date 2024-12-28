```python
# WEB SCRAPING
# BeautifulSoup and Requests (a beginner package for web scraping)
# Works by getting all the html from a website
# then querying it by its tags such as classes and attributes

from bs4 import BeautifulSoup
import requests

# Assign the url you are going to use to a variable:
url = 'https://www.scrapethissite.com/pages/forms/'

# Send a get() request to the url server,
# url is going to return a response object:
# Response is: <Response [200]> <--in this case the response is 200
# These numbers are like the 404: server not found (page not found)
# print(requests.get(url)) <--use print to visualize response if u want
requests.get(url)  # get is a function of the requests library
# Assign the get request a variable:
page = requests.get(url)

# use BeautifulSoup library to get all the html from the page
# specify what you want to parse after the comma, in this case html
# 1. assign it to a variable, soup:
# 2. using page as an ARGUMENT in BeautifulSoup function
# 3. using features="html.parser" as an ARGUMENT to specify the parser in the BeautifulSoup constructor.
soup = BeautifulSoup(page.text, features="html.parser")
# BeautifulSoup(requests.get(url)).text, features="html.parser")
"""
print(soup)  # prints all the html!
print(soup.prettify())  # prints all the html a little more readable
"""

# Side notes about HTML! (hypertext markup language)
# <html>
# <head>
#     <title> Webpage Title </title>
# </head>
# <body>
#     EVERYTHING basically goes in here!
#     Tags eg: <h1><p><ul><ol>
# </body>
# </html>

# NOTE in our url, we found our table by selecting element to inspect:
# <table class="table">
# <tbody>
# <tr>
#     <th> column headings [multiple lines]</th>
# </tr>
# <tr class="team" >
#     <td class="name"> Boston Bruins </th>
#     <th class="year"> 1990 </th>
# </tr>

# Find and Find_all
# how to actually apply the html tags and classes to find things:

# find() will find VERY FIRST div tag in html:
soup.find('div')

# find_all() will find ALL instances of 'div' tag:
# will find all instances of 'div' tag WITH class='col-md-12' !!
soup.find_all('div', class_='col-md-12')
# NOTICE, it returns as a LIST!!!
# So it puts each INSTANCE of tag 'div' with class 'col-md-12' into its own LIST ITEM!

# Now let's get specific.
# Say we want to take in the FOLLOWING paragraph:
# This will put it in a list (notice the brackets),
# and it will still have the 'p' tage on it
soup.find_all('p', class_='lead')

# Now let's take it out of the list, just the sentence as a string:
# 1. Add ' .text ' to PULL text!
# 2. change function to find() NOT find_all() (cannot use .text with find_all())
soup.find('p', class_='lead').text

# Can add function, .strip(), which cleans up whitespace a little bit:
soup.find('p', class_='lead').text.strip()

# So let's say we wanted to just get the content, Team Name, from the page:
# it has a tag of, 'th':
soup.find_all('th')
soup.find('th').text.strip()
# print(soup.find('th').text.strip())

# Scraping Data from a REAL WEBSITE and using PANDAS dataframe!
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

# Now we want to pull the table headings, note they all have 'th' tags:
# Now pull all the 'th' tags from table
# Assign them a variable:
headings = table.find_all('th')  # notice that headings IS a list!

# Now LOOP through the headings, to get the headings you want!
# The regular for loop:
"""
column_names = []  # Create an empty list to store the results
for columns in headings:
    column_names.append(columns.text.strip())
"""

# Rewritten as LIST COMPREHENSION:
column_names = [columns.text.strip() for columns in headings]

# Note: Will not be able to use strip() AFTER it becomes items in a list (only while you're iterating through it)

# import pandas so it's ready to go
# we'll put our column headings in to start
import pandas as pd

# Use pandas function DataFrame() to bring in column names:
# pd.DataFrame(columns = column_names)

# Assign the dataframe to a variable:
df = pd.DataFrame(columns=column_names)

print(df)

# Now we need to pull in the data from the rest of the DataFrame:
# Looks like it's td tags encapsulated in tr tags:
# Looks like it's t for table and then r for rows, d for data
# Remember to always pull from the variable you assigned to table!

# Let's loop through our column data:
column_data = table.find_all('tr')
for row in column_data[1:]:  # Error in the index at 0, empty list, must begin at index[1]
    row_data = row.find_all('td')
    indv_row_data = [data.text.strip() for data in row_data]
    # Now we have to put these data into our dataframe ONE AT A TIME
    # That's why we are doing it INSIDE the LOOP!
    # We essentially want it to append each row to the dataframe each time it loops through
    # Go through this again!!
    length = len(df)
    df.loc[length] = indv_row_data

print(df)  # Checking if it's right

# Now can be analyzed further in pandas
# But we can export it to a csv:
df.to_csv(r'C:\Users\Anuca\Desktop\Revenues.csv', index=False)
# added: index = False to omit the first column which shows the index value
```
