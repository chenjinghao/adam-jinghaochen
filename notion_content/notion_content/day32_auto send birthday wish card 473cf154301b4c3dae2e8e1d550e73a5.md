# day32_auto send birthday wish card

```python
##################### Hard Starting Project ######################

# 1. Update the birthdays.csv with your friends & family's details. 
# HINT: Make sure one of the entries matches today's date for testing purposes.
#done

# 2. Check if today matches a birthday in the birthdays.csv
# HINT 1: Only the month and day matter. 
# HINT 2: You could create a dictionary from birthdays.csv that looks like this:
# birthdays_dict = {
#     (month, day): data_row
# }
#HINT 3: Then you could compare and see if today's month/day matches one of the keys in birthday_dict like this:
# if (today_month, today_day) in birthdays_dict:

# 3. If step 2 is true, pick a random letter from letter templates and replace the [NAME] with the person's actual name from birthdays.csv
# HINT: https://www.w3schools.com/python/ref_string_replace.asp

# 4. Send the letter generated in step 3 to that person's email address.
# HINT: Gmail(smtp.gmail.com), Yahoo(smtp.mail.yahoo.com), Hotmail(smtp.live.com), Outlook(smtp-mail.outlook.com)

import pandas as pd
import smtplib
import datetime as dt
import random

record = pd.read_csv("birthdays.csv")
record_dict = pd.DataFrame.to_dict(record, orient='records')

curr_datetime = dt.datetime.now()
curr_month = curr_datetime.month
curr_day = curr_datetime.day

my_email = 'jinghao.c@gmail.com'
password = "iacnjijlcghdeejfajgjoiahuehbhaioh" #password generated from gmail

list_letter_templates = ["letter_1.txt", "letter_2.txt", "letter_3.txt"]

for record in record_dict:
    if record['month'] == curr_month and record['day'] == curr_day:
        print(record)
        random_template_txt = random.choice(list_letter_templates)
        with open(f"letter_templates/{random_template_txt}") as file:
            template= file.read()
            ready_to_send_template = template.replace("[NAME]", record['name'])
            print(ready_to_send_template)

        with smtplib.SMTP("smtp.gmail.com") as connection:
            connection.starttls()
            connection.login(user=my_email, password=password)
            connection.sendmail(from_addr=my_email,
                                to_addrs=record['email'],
                                msg=f"Subject:Happy Birthday!\n\n {ready_to_send_template}")
```

Result: 

![Untitled](Untitled%2018.png)

[main.py](main.py)

[birthdays.csv](birthdays.csv)

[letter_3.txt](letter_3.txt)

[letter_1.txt](letter_1.txt)

[letter_2.txt](letter_2.txt)