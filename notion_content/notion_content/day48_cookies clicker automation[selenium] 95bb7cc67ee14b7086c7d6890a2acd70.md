# day48_cookies clicker automation[selenium]

- Click the cookies for 5 mins
- Every 5 seconds ask the program to check out the available option from store on the right
- Pick the highest cost item to purchase

[Cookie Clicker](http://orteil.dashnet.org/experiments/cookie/)

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service as ChromeService
import time

URL = 'http://orteil.dashnet.org/experiments/cookie/'

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()))
driver.get(url=URL)

cookies = driver.find_element(By.ID, 'cookie')

five_secs = time.time() + 5
five_mins = time.time() + 60 * 5

items = driver.find_elements(By.CSS_SELECTOR, "#store div")
item_ids = [item.get_attribute("id") for item in items]

cookies_per_second = driver.find_element(By.ID, 'cps')

while True:
    cookies.click()

    if time.time() > five_secs:

        all_price = driver.find_elements(By.CSS_SELECTOR, '#store b')
        item_price = []

        for price in all_price:
            str_price = price.text
            if str_price != '':
                cost = int(str_price.split('-')[1].strip().replace(',', ''))
                item_price.append(cost)
        # print(item_price)

        cookie_upgrades = {}
        for i in range(len(item_price)):
            cookie_upgrades[item_price[i]] = item_ids[i]
        # print(cookie_upgrades)

        money = driver.find_element(By.ID, 'money').text
        if ',' in money:
            money = money.replace(',', '')
        no_of_cookies = int(money)
        # print(no_of_cookies)

        affordable_upgrade = {}
        for cost, id in cookie_upgrades.items():
            if no_of_cookies > cost:
                affordable_upgrade[cost] = id

        # print(affordable_upgrade)

        highest_price_affordable_upgrade = max(affordable_upgrade)
        # print(highest_price_affordable_upgrade)
        to_purchase_id = affordable_upgrade[highest_price_affordable_upgrade]
        print(f'buy {to_purchase_id}')

        driver.find_element(By.ID, f'{to_purchase_id}').click()

        five_secs = time.time() + 5

    if time.time() > five_mins:
        cookies_per_second = driver.find_element(By.ID, 'cps')
        print(cookies_per_second.text)
        break
```

Demonstration: 

[20230704120634.mp4](20230704120634.mp4)

![Untitled](Untitled%2013.png)

![Untitled](Untitled%2014.png)