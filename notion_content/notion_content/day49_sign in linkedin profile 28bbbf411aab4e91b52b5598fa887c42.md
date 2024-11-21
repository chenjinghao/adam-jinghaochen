# day49_sign in linkedin profile

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service as ChromeService
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
import os
import time

USER_EMAIL = os.environ['user_email']
PASSWORD = os.environ['password']

URL = 'https://www.linkedin.com/'

#brower stay after the code processed
options = Options()
options.add_experimental_option('detach', True)

driver = webdriver.Chrome(service=ChromeService(ChromeDriverManager().install()), options=options)

driver.get(url=URL)
time.sleep(5)

#Landing page
sign_in = driver.find_element(By.XPATH, '/html/body/nav/div/a[2]')
sign_in.click()

##SIGN IN PAGE
user_name = driver.find_element(By.XPATH, '//*[@id="username"]')
user_name.send_keys(USER_EMAIL)
password = driver.find_element(By.XPATH, '//*[@id="password"]')
password.send_keys(PASSWORD)

sign_in_button = driver.find_element(By.XPATH, '//*[@id="organic-div"]/form/div[3]/button')
sign_in_button.click()
```