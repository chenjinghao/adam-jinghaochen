# day47_Amazon price alarm

```python
import requests
from pprint import pprint
from bs4 import BeautifulSoup
import lxml
import smtplib
import os
from email.mime.text import MIMEText

##secret
EMAIL_ADDR = os.environ['user_email_addr']
EMAIL_PWD = os.environ['user_email_pwd']

##user input
URL ='https://www.amazon.com/dp/B075CYMYK6?psc=1&ref_=cm_sw_r_cp_ud_ct_FM9M699VKHTT47YD50Q6'
user_email = 'jinghao_chen@hotmail.com' #for test
price_alarm = 100

headers = {
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.0.0 Safari/537.36 Edg/114.0.1823.67',
    'accept-language': 'en-US,en;q=0.9'
}

##code
respond = requests.get(url=URL, headers=headers)
respond.raise_for_status()
website = respond.text
# pprint(website)

###making a beautiful soup
soup = BeautifulSoup(website, 'lxml')

##get the price
dollors = soup.find(name='span', class_='a-price-whole')
dollors = dollors.get_text()
# print(dollors)
cents = soup.find(name='span', class_='a-price-fraction')
cents = cents.get_text()
# print(cents)
full_price = float(dollors + cents)
# print(full_price)
# print(type(full_price))

##get product title
product_title = soup.find(name='span', id='productTitle')
product_title= product_title.get_text()
print(product_title)

###price alarm
if full_price < price_alarm:
    ###email notification
    with smtplib.SMTP("smtp.gmail.com") as connection:
        message = MIMEText(f'{product_title}\nhas drop below your preferred price {price_alarm}.\nVisit Amazon: {URL}')
        message['Subject'] = 'Amazon Price Alter'
        message['Content-Type'] = 'text/plain; charset=utf-8'

        connection.starttls()
        connection.login(user=EMAIL_ADDR, password=EMAIL_PWD)
        connection.sendmail(from_addr=EMAIL_ADDR,
                            to_addrs=user_email,
                            msg=message.as_string())
        ## email contain clickable link
```

The product: 

[Instant Pot Duo Plus 9-in-1 Electric Pressure Cooker, Slow Cooker, Rice Cooker, Steamer, Sauté, Yogurt Maker, Warmer & Sterilizer, Includes Free App with over 1900 Recipes, Stainless Steel, 3 Quart](https://www.amazon.com/dp/B075CYMYK6?ref_=cm_sw_r_cp_ud_ct_FM9M699VKHTT47YD50Q6&th=1)

Email notification: 

![Untitled](Untitled%2015.png)