# day50_Tinder website swipe profiles automatically

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from webdriver_manager.chrome import ChromeDriverManager
import time
import os

LOGIN_EMAIL = os.environ['login_email']
LOGIN_PASSWORD = os.environ['login_password']

URL = 'https://tinder.com/'

##windows stay open
options = Options()
options.add_experimental_option('detach', True)

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=options)

driver.get(url=URL)
time.sleep(5)

main_page = driver.current_window_handle

##log in
log_in_button = driver.find_element(By.LINK_TEXT, 'Log in')
log_in_button.click()
time.sleep(5)

login_google_button = driver.find_element(By.TAG_NAME, 'iframe')
login_google_button.click()

#to deal with google login pop up window
for handle in driver.window_handles:
    if handle != main_page:
        google_login_page = handle

driver.switch_to.window(google_login_page)

#enter email
email = driver.find_element(By.XPATH, '//*[@id="identifierId"]')
email.send_keys(LOGIN_EMAIL)
next_button = driver.find_element(By.XPATH, '//*[@id="identifierNext"]/div/button')
next_button.click()
time.sleep(3)
#enter password
password = driver.find_element(By.XPATH, '//*[@id="password"]/div[1]/div/div[1]/input')
password.send_keys(LOGIN_PASSWORD)
next_button_password = driver.find_element(By.XPATH, '//*[@id="passwordNext"]/div/button')
next_button_password.click()
time.sleep(5)

#go back to main windows
driver.switch_to.window(main_page)

#
location_allow_button = driver.find_element(By.XPATH, '//*[@aria-label="Allow"]')
location_allow_button.click()
time.sleep(5)

notification_enable_button = driver.find_element(By.XPATH, '//*[@aria-label="Enable"]')
notification_enable_button.click()
time.sleep(5)

##control the keyboard to swipe left to dislike
#change to swipe right to like
keyboard = ActionChains(driver)
for n in range(20):
    keyboard.key_down(Keys.ARROW_LEFT).perform()
    print('key down')
    keyboard.key_up(Keys.ARROW_LEFT).perform()
    print('key up')
    time.sleep(2)
```

Possible add feature: 

- Collect data from each profile.