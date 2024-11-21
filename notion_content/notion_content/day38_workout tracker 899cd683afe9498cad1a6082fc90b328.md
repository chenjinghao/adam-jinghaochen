# day38_workout tracker

used [Nutrition API by Nutritionix](https://www.nutritionix.com/business/api) to calculate the calories base on the input from user

used [Sheety - Turn your Google Sheet into an API](https://sheety.co/) to update relevant information to a google sheet

used os.environ to store all user name, password and token.

[https://replit.com/@chenjinghao/day38workout-tracker](https://replit.com/@chenjinghao/day38workout-tracker)

```python
import requests as re
from datetime import datetime
import os

APP_ID = os.environ['APP_ID']
API_KEY = os.environ['API_KEY']

ACTIVITY = input('Tell me which exercises you did: ')
GENDER = 'male'
WEIGHT = '85'
HEIGHT = '160'
AGE = '35'

exercise_endpoint = 'https://trackapi.nutritionix.com/v2/natural/exercise'
exercise_endpoint_config = {
    'query': ACTIVITY,
    'gender': GENDER,
    'weight_kg': WEIGHT,
    'height_cm': HEIGHT,
    'age': AGE
}

exercise_headers = {
    'x-app-id': APP_ID,
    'x-app-key': API_KEY
}

respond = re.post(url=exercise_endpoint, json=exercise_endpoint_config, headers=exercise_headers)
result = respond.json()
print(result)

exercise_type = result['exercises'][0]['user_input'].title()
print(exercise_type)
exercise_duration = result['exercises'][0]['duration_min']
exercise_calories = result['exercises'][0]['nf_calories']

curr_date = datetime.now().strftime('%Y%m%d')
print(curr_date)
curr_time = datetime.now().strftime('%X')
print(curr_time)

SHEETY_ENDPOINT = os.environ['SHEETY_ENDPOINT']
# GOOGLE_SHEET_NAME = 'workout'
sheety_config = {
    'workout': {
        'date': curr_date,
        'time': curr_time,
        'exercise': exercise_type,
        'duration': exercise_duration,
        'calories': exercise_calories
    }
}
SHEETY_TOKEN = os.environ['SHEETY_TOKEN']
SHEETY_AUTH = {
    'Authorization': SHEETY_TOKEN
}

sheety_respond = re.post(url=SHEETY_ENDPOINT, json=sheety_config, headers=SHEETY_AUTH)
print(sheety_respond.text)
```