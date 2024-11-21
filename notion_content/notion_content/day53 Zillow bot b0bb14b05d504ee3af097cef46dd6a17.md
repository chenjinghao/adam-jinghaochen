# day53 Zillow bot

1. Scrape listings data from Zillow by using Beatifulsoup.
2. Store them in google sheet Selenium.

```python
import time

from bs4 import BeautifulSoup
import requests as re
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options

GOOGLE_FORM = 'https://docs.google.com/forms/d/e/1FAIpQLSewacRBihNljBci4s5zS4VJ91QrIYE0CzMaLJ_NUx_Csy_MRQ/viewform?usp=sf_link'
ZILLOW_URL = 'https://www.zillow.com/homes/for_rent/1-_beds/?searchQueryState=%7B%22pagination%22%3A%7B%7D%2C%22usersSearchTerm%22%3Anull%2C%22mapBounds%22%3A%7B%22west%22%3A-122.56276167822266%2C%22east%22%3A-122.30389632177734%2C%22south%22%3A37.69261345230467%2C%22north%22%3A37.857877098316834%7D%2C%22isMapVisible%22%3Atrue%2C%22filterState%22%3A%7B%22fr%22%3A%7B%22value%22%3Atrue%7D%2C%22fsba%22%3A%7B%22value%22%3Afalse%7D%2C%22fsbo%22%3A%7B%22value%22%3Afalse%7D%2C%22nc%22%3A%7B%22value%22%3Afalse%7D%2C%22cmsn%22%3A%7B%22value%22%3Afalse%7D%2C%22auc%22%3A%7B%22value%22%3Afalse%7D%2C%22fore%22%3A%7B%22value%22%3Afalse%7D%2C%22pmf%22%3A%7B%22value%22%3Afalse%7D%2C%22pf%22%3A%7B%22value%22%3Afalse%7D%2C%22mp%22%3A%7B%22max%22%3A3000%7D%2C%22price%22%3A%7B%22max%22%3A872627%7D%2C%22beds%22%3A%7B%22min%22%3A1%7D%7D%2C%22isListVisible%22%3Atrue%2C%22mapZoom%22%3A12%7D'

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36 Edg/114.0.1823.67',
    'accept-language': 'en-US,en;q=0.9'
}

respond = re.get(url=ZILLOW_URL, headers=headers)
respond.raise_for_status()

zillow_search_page = respond.text

soup = BeautifulSoup(zillow_search_page, 'html.parser')

all_addresses = soup.find_all('address', {'data-test': 'property-card-addr'})
addresses = []
for address in all_addresses:
    addresses.append(address.text)
print(addresses)

all_prices = soup.find_all('span', {'data-test': 'property-card-price'})
prices = []
for price in all_prices:
    int_price = int(price.text[1:6].replace(',',''))
    prices.append(int_price)
print(prices)

all_urls = soup.find_all('a', {'data-test': 'property-card-link'})
links = []
for url in all_urls:
    links.append(url.get('href'))
for i in range(len(links)):
    if links[i][0:2] == '/a':
        links[i] = 'https://www.zillow.com' + links[i]
print(links)

# browser stay after the code processed
options = Options()
options.add_experimental_option('detach', True)

# download webdriver
driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=options)
#visit speedtest website
driver.get(url=GOOGLE_FORM)

for i in range(len(addresses)):
    ans_address = driver.find_element(By.XPATH, '/html/body/div/div[2]/form/div[2]/div/div[2]/div[1]/div/div/div[2]/div/div[1]/div/div[1]/input')
    ans_address.send_keys(addresses[i])
    ans_price = driver.find_element(By.XPATH, '/html/body/div/div[2]/form/div[2]/div/div[2]/div[2]/div/div/div[2]/div/div[1]/div/div[1]/input')
    ans_price.send_keys(prices[i])
    ans_link = driver.find_element(By.XPATH, '/html/body/div/div[2]/form/div[2]/div/div[2]/div[3]/div/div/div[2]/div/div[1]/div/div[1]/input')
    ans_link.send_keys(links[i])
    driver.find_element(By.XPATH, '/html/body/div/div[2]/form/div[2]/div/div[3]/div[1]/div[1]/div/span/span').click()
    time.sleep(3)
    driver.find_element(By.LINK_TEXT, 'Submit another response').click()
    time.sleep(2)
driver.close()
```

![Untitled](Untitled%208.png)

![Untitled](Untitled%209.png)