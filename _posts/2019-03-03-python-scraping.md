---
title: "Python #3: Web Scraping"
date: 2019-02-01
tags: []
excerpt: "JSON, BeautifulSoup, & Selenium"
---

# Web Scraping
The [requests](http://docs.python-requests.org/en/master/) module can be used to either **get**, **post**, or **put** data to and from an online server. Commonly **get** is used to retrieve information from or about a website, this is called web scraping. Another common usage is with [APIs](https://apilist.fun/) which return pre-formatted data.  

The syntax for a simple web request is  
```python
import requests

url = r'https://www.bbc.co.uk/news'  # Can be a web address or an API
r = requests.get(url)
```
Running `r.status_code` will return the HTTP response code for the request which gives information on the performance of the request. Generally; 200=okay, 300s=redirection, 400s=error.  

### JSON
`r` is now an object that contains information about the web page at `url`. To extract this into a useful form we can use the [JSON](https://docs.python.org/3/library/json.html) module.  

```python
import json

r.text     # Returns JSON content 
response = json.loads(r.text) # Load JSON content into a dictionary
```

The dictionary `response` can then be read from and altered just like any python object. It's best to run `response.keys()` after this to see the categories of the JSON dictionary.   

#### JSON request blocks
Some websites limit the number of requests from a single IP address and will block your request if run too frequently. To get around this you must send the *user_agent* string as part of the `.gets` statement. The user_agent can be found by going to the page on chrome, then *right-click -> inspect -> Network -> click element in page -> select any element in Name list -> scroll to bottom of details*.  

```python
user_agent = r'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36'
r = requests.get(url, headers={'user-agent':user_agent})
```

### BeautifulSoup
[BeautifulSoup](https://pypi.org/project/beautifulsoup4/) is a module that can parse HTML from a scraped page to draw out specific items. 

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(r.text, 'html.parser')
headlinesoup = soup.find('h1')
# <h1 class="gs-u-vh" id="skip-to-content-link-target" tabindex="-1">BBC News Home</h1>

headlinesoup.text
# 'BBC News Home'
```

### Selenium
[Selenium](https://selenium-python.readthedocs.io/) is a *web-automation* module. It can be used to work around javascript pages that may have pop-ups, delays, or forms that must be navigated before the desired HTML is present on the page. It can also be used to search and navigate a page from within Python.  

Simple example of waiting 5 seconds before retrieving html:  
```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()  # Starts Chrome as a 'web driver'

driver.get(r'http://pythonscraping.com/pages/javascript/ajaxDemo.html') # Load page
time.sleep(5) # 5 second sleep (waiting for page to load)

r = BeautifulSoup(driver.page_source, 'html.parser') # Soup page html
r.find('div').text # Display main html content
# 'Here is some important text you want to retrieve! A button to click!'

driver.close()
```  

Example of searching for content and clicking a button:  
```python
driver = webdriver.Chrome()
url = r'https://www.bbc.co.uk'
driver.get(url)

xpath = r'//input[@id="orb-search-q"]' # xpath to an input element with id 'orb-search-q' (main search bar)

driver.find_element_by_xpath(xpath).send_keys('brexit', Keys.ENTER) # Enter 'brexit' search term and click search

xpath = r'//a[@class="more"]' # xpath to 'more' button
driver.find_element_by_xpath(xpath).click() # Click more button
time.sleep(2)
driver.find_element_by_xpath(xpath).click()  # Repeat as many times as needed

### Use BeautifulSoup to parse expanded search results ###
```