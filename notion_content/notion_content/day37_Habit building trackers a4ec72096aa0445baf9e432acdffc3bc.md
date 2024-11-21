# day37_Habit building trackers

Use API set up a pixela account and use Post, Put, Delete to add, update and delete datapoint

```python
import requests as re
from datetime import datetime

USER_NAME = 'adamcjh'
TOKEN = 'cchheennjjiinngghhaaoo'
GRAPH_ID = 'graph1'

pixela_endpoint = "https://pixe.la/v1/users"

#create account
user_parameter = {
    'token': 'cchheennjjiinngghhaaoo',
    'username': 'adamcjh',
    'agreeTermsOfService': 'yes',
    'notMinor': 'yes'
}

# respond = re.post(url=pixela_endpoint, json=user_parameter)
# print(respond.text)

#create a pixel graph
graph_endpoint = f"{pixela_endpoint}/{USER_NAME}/graphs"
graph_config = {
    'id': GRAPH_ID,
    'name': 'Cycling Graph',
    'unit': 'Km',
    'type': 'float',
    'color': 'ajisai'
}

headers = {
    'X-USER-Token': TOKEN
}

# respond = re.post(url=graph_endpoint, json=graph_config, headers=headers)
# print(respond.text)

#post a data point
graph_update_endpoint = f'{pixela_endpoint}/{USER_NAME}/graphs/{GRAPH_ID}'
curr_datetime = str(datetime.now())
curr_date = curr_datetime.split(' ')[0].replace('-', '')
print(curr_date)

graph_update_config = {
    'date': curr_date,
    # 'date': datetime.now().strftime('%Y%m%d'), instructor's code
    'quantity': '15.00'
}

# respond = re.post(url=graph_update_endpoint, json=graph_update_config, headers=headers)
# print(respond.text)

#update a data point

# graph_data_change_config = {
#     'quantity': '12.00'
# }
# graph_data_change = f"{pixela_endpoint}/{USER_NAME}/graphs/{GRAPH_ID}/{curr_date}"
# respond = re.put(url=graph_data_change, json=graph_data_change_config, headers=headers)
# print(respond.text)

#delete a data point
graph_data_delete = f'{pixela_endpoint}/{USER_NAME}/graphs/{GRAPH_ID}/{curr_date}'
respond = re.delete(url=graph_data_delete, headers=headers)
print(respond.text)
```