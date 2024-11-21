# day52 Instagram bot

A**utomatically follower chosen Instagram account’s follower**

main.py

```python
import instafollower

insta_bot = instafollower.InstaFollower()

insta_bot.login()
insta_bot.find_followers()
insta_bot.follow()
```

instafollower.py

```python
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys

import os
import time

ACC_TO_FOLLOW = 'curiocityvancouver'
INSTAGRAM_URL = 'https://www.instagram.com/'

USERNAME = os.environ['username']
PASSWORD = os.environ['password']

class InstaFollower:
    def __init__(self):
        self.options = Options()
        self.options.add_experimental_option('detach', True)
        self.driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=self.options)

    def login(self):
        self.driver.get(url=f'{INSTAGRAM_URL}accounts/login/')
        time.sleep(5)

        username = self.driver.find_element(By.NAME, 'username')
        username.send_keys(USERNAME)

        password = self.driver.find_element(By.NAME, 'password')
        password.send_keys(PASSWORD)

        time.sleep(5)

        password.send_keys(Keys.ENTER)

        time.sleep(5)

##scroll down from pop-up windows

    def find_followers(self):
        self.driver.get(url=f'{INSTAGRAM_URL}/{ACC_TO_FOLLOW}/followers')
        time.sleep(2)
        followers_window = self.driver.find_element(By.XPATH,
                                                    '/html/body/div[2]/div/div/div[3]/div/div/div[1]/div/div[2]/div/div/div/div/div[2]/div/div/div[2]')

        for i in range(20):
            self.driver.execute_script("arguments[0].scrollTop = arguments[0].scrollHeight", followers_window)
            time.sleep(2)

    def follow(self):
        all_follow_buttons = self.driver.find_elements(By.CSS_SELECTOR, 'button')
        for button in all_follow_buttons:
            if button.text == 'Follow':
                button.click()
                time.sleep(20)
```

![Untitled](Untitled%2010.png)