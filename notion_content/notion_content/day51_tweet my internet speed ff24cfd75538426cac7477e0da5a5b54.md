# day51_tweet my internet speed

To check my internet speed, I utilize selenium and then utilize tweepy to create a tweet containing the speedtest website's outcome. While the approach differs somewhat from what the course specified, I opted to use tweepy instead of selenium to avoid the need for human verification in case I run the code frequently.

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service as ChromeService
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
import time
import os
import tweepy

SPEEDTEST_URL = 'https://www.speedtest.net/'
###secret
api_key = os.environ['api_key']
api_key_secret = os.environ['api_key_secret']
bearer_token = os.environ['bearer_token']
access_token = os.environ['access_token']
access_token_secret = os.environ['access_token_secret']
client_id = os.environ['client_id']
client_secret = os.environ['client_secret']

# browser stay after the code processed
options = Options()
options.add_experimental_option('detach', True)

# download webdriver
driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=options)
#visit speedtest website
driver.get(url=SPEEDTEST_URL)

go_button = driver.find_element(By.CLASS_NAME, 'start-button').click()
time.sleep(60 * 1)

notification_dismiss = driver.find_element(By.XPATH, '//*[@id="container"]/div/div[3]/div/div/div/div[2]/div[3]/div[3]/div/div[8]/div/a').click()

result = {}
download_speed = driver.find_element(By.CLASS_NAME, 'download-speed').text
upload_speed = driver.find_element(By.CLASS_NAME, 'upload-speed').text
service_provider = driver.find_element(By.CLASS_NAME, 'js-data-sponsor').text
location = driver.find_element(By.CLASS_NAME, 'js-sponsor-name').text
result['download_speed'] = download_speed
result['upload_speed'] = upload_speed
result['service_provider'] = service_provider
result['location'] = location
print(result)

driver.quit()

###Post a tweet
client = tweepy.Client(consumer_key=api_key, consumer_secret=api_key_secret, access_token=access_token, access_token_secret=access_token_secret)

status = f"My current Internet speed:\n" \
         f"Download: {result['download_speed']}\n" \
         f"Upload: {result['upload_speed']}\n" \
         f"Provider: {result['service_provider']}\n" \
         f"Location: {result['location']}"

client.create_tweet(text=status)

print('success!')
```

![Untitled](Untitled%2011.png)

![Untitled](Untitled%2012.png)