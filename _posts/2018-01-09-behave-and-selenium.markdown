---
layout: post
title:  "Getting started with Selenium and Behave"
date:   2018-01-09 11:25:00 +0000
categories: python selenium behave frontend
---

In this tutorial we'll try to get you up and running with selenium and behave.

Make sure you have [pip][pip-org] and install run the following commands:
- `pip install selenium`
- `pip install behave`

Now let's create our project directory, in my case it's going to be `~/behave_tutorial`.
- `mkdir ~/behave_tutorial`
- `cd ~/behave_tutorial`

Behave projects most have an specific structure, so let's create the necessary files 
and folders.
- `mkdir features` 
- `mkdir steps`
- `touch environment.py`

We're going to be using google chrome, so head over to [google driver][google-driver]
and download the appropriate version for your OS.

In the environment file we're going to put everything related to behave setup and
configuration. For this tutorial, put the following content in the `environment.py` file:

{% highlight python %}
from selenium import webdriver

def before_all(context):
    context.google_url = 'https://google.com'

def before_scenario(context, scenario):
    context.driver = webdriver.Chrome(executable_path='./chromedriver')

def after_scenario(context, scenario):
    context.driver.close()

{% endhighlight %}

In the `features` folder create a file named `google_search.feature` and add 
the following content:
```
Feature: Google Search

Scenario: Search for python
    Given I am on the google search page
    When I search for "python"
    Then I see the result page
    And I see "https://www.python.org/" in the results

```

When you run `behave` it'll look for the test steps a folder named `steps`. Let's
write the implementation of our steps:

In the `steps` folder create a file named `google_search.py`.
- `touch google_search.py`

And add the following:

{% highlight python %}

from behave import step

from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait

@step('I am on the google search page')
def open_google_search_page(context):
    context.driver.get(context.google_url)

@step('I search for "{search_term}"')
def search_term(context, search_term):
    search_box = context.driver.find_element_by_name('q')
    search_box.send_keys(search_term)
    search_box.send_keys(Keys.RETURN)

@step('I see the result page')
def results_page_is_displayed(context):
    wait = WebDriverWait(context.driver, 10)
    wait.until(EC.visibility_of_element_located((By.ID, 'resultStats')))

@step('I see "{url}" in the results')
def check_result(context, url):
    locator = f"//a[@href='{url}']"
    wait = WebDriverWait(context.driver, 10)
    wait.until(EC.visibility_of_element_located((By.XPATH, locator)))

{% endhighlight %}

Finally, let's run our tests. From the `~/behave_tutorial` folder run:
- `behave`

You should see a browser opening and searching for the word "python" on google.

Thank you for reading and I hope you found it helpful!

[python-org]: https://www.python.org/downloads/
[behave-org]: http://pythonhosted.org/behave/
[pip-org]: https://pip.pypa.io/en/stable/installing/
[google-driver]: https://sites.google.com/a/chromium.org/chromedriver/downloads