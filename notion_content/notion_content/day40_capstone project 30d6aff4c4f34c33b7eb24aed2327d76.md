# day40_capstone project

- main.py
    
    ```python
    # This file will need to use the DataManager,FlightSearch, FlightData, NotificationManager classes to achieve the program requirements.
    from pprint import pprint
    from data_manager import DataManager
    from flight_search import FlightSearch
    from datetime import datetime, timedelta
    from notification_manager import NotificationManager
    
    data_manager = DataManager()
    sheet_data = data_manager.get_data()
    flight_search = FlightSearch()
    notification_manager = NotificationManager()
    pprint(sheet_data)
    
    FROM_LOCATION = 'LON'
    
    for city in sheet_data:
        for key, value in city.items():
            if key == 'iataCode' and value == '':
                flight_search = FlightSearch()
                value = flight_search.get_iatacode(city_name=city['city'])['locations'][0]['code']
                print(value)
                city['iataCode'] = value
    
    data_manager.data = sheet_data
    data_manager.update_data()
    
    tmr = datetime.now() + timedelta(days=1)
    tmr = tmr.strftime('%d/%m/%Y')
    six_mths_later = datetime.now() + timedelta(days=(6 * 30))
    six_mths_later = six_mths_later.strftime('%d/%m/%Y')
    
    for destination in sheet_data:
        flight = flight_search.get_flightdata(
            origin_city_code=FROM_LOCATION,
            destination_city_code=destination['iataCode'],
            from_time=tmr,
            to_time=six_mths_later
        )
    
        if flight is None:
            continue
    
        if flight.price < destination['lowestPrice']:
            message_body = f"Only {flight.price} to fly from {flight.origin_city}-{flight.origin_airport} to {flight.destination_city}-{flight.destination_airport}, from {flight.out_date} to {flight.return_date}."
    
            if flight.stop_overs > 0:
                message_body = f'Flight has {flight.stop_overs} via {flight.via_city}.'
    
            notification_manager.notification(message_body=message_body)
    ```
    
- data_manager.py
    
    ```python
    import requests as re
    import os
    
    SHEET_GET_DATA_ENDPOINT = os.environ['SHEET_GET_DATA_ENDPOINT']
    SHEET_PUT_DATA_ENDPOINT = os.environ['SHEET_PUT_DATA_ENDPOINT']
    SHEET_AUTH = os.environ['SHEET_AUTH']
    
    class DataManager:
        def __init__(self):
            self.data = {}
            # print(self.data)
    
        def get_data(self):
            header = {
                'Authorization': SHEET_AUTH
            }
            respond = re.get(url=SHEET_GET_DATA_ENDPOINT, headers=header)
            respond.raise_for_status()
            data = respond.json()
            # pprint(data['prices'])
            self.data = data['prices']
            return self.data
    
        def update_data(self):
            header = {
                'Authorization': SHEET_AUTH
            }
            for city in self.data:
                inserted_code = {
                    'price': {
                        'iataCode': city['iataCode']
                    }
                }
                re.put(url=f"{SHEET_PUT_DATA_ENDPOINT}/{city['id']}", json=inserted_code, headers=header)
            # print(reponse)
    ```
    
- flight_search.py
    
    ```python
    import requests as re
    import os
    from pprint import pprint
    from flight_data import FlightData
    
    KIWI_ENDPOINT = 'https://api.tequila.kiwi.com/locations/query'
    TEQUILA_SEARCH_ENDPOINT = 'https://api.tequila.kiwi.com/v2/search'
    KIWI_API_KEY = os.environ['KIWI_API_KEY']
    
    class FlightSearch:
        # This class is responsible for talking to the Flight Search API.
        def __init__(self):
            self.city_code = []
    
        def get_iatacode(self, city_name):
            headers = {
                'apikey': KIWI_API_KEY
            }
            parameters = {
                'term': city_name,
                'location_types': 'city',
            }
            respond = re.get(url=KIWI_ENDPOINT, params=parameters, headers=headers)
            respond.raise_for_status()
            code = respond.json()
            pprint(code)
            return code
    
        def get_flightdata(self, origin_city_code, destination_city_code, from_time, to_time):
            headers = {
                'apikey': KIWI_API_KEY
            }
    
            parameters = {
                'fly_from': origin_city_code,
                'fly_to': destination_city_code,
                'date_from': from_time,
                'date_to': to_time,
                'nights_in_dst_from': 7,
                'nights_in_dst_to': 28,
                'flight_type': 'round',
                'one_for_city': 1,
                'max_stopovers': 0,
                'curr': 'GBP'
            }
    
            respond = re.get(
                url=TEQUILA_SEARCH_ENDPOINT,
                params=parameters,
                headers=headers
            )
    
            # pprint(respond.json())
    
            try:
                data = respond.json()["data"][0]
            except IndexError:
                parameters['max_stopovers'] = 2
    
                respond = re.get(
                    url=TEQUILA_SEARCH_ENDPOINT,
                    params=parameters,
                    headers=headers
                )
    
                data = respond.json()["data"][0]
                pprint(data)
    
                flight_data = FlightData(
                    price=data["price"],
                    origin_city=data["route"][0]["cityFrom"],
                    origin_airport=data["route"][0]["flyFrom"],
                    destination_city=data["route"][1]["cityTo"],
                    destination_airport=data["route"][1]["flyTo"],
                    out_date=data["route"][0]["local_departure"].split("T")[0],
                    return_date=data["route"][2]["local_departure"].split("T")[0],
                    stop_overs=1,
                    via_city=data["route"][0]['cityTo']
                )
                print(f'{flight_data.destination_city}: ${flight_data.price} with 2 stop via {flight_data.via_city}')
                return flight_data
            else:
                flight_data = FlightData(
                    price=data["price"],
                    origin_city=data["route"][0]["cityFrom"],
                    origin_airport=data["route"][0]["flyFrom"],
                    destination_city=data["route"][0]["cityTo"],
                    destination_airport=data["route"][0]["flyTo"],
                    out_date=data["route"][0]["local_departure"].split("T")[0],
                    return_date=data["route"][1]["local_departure"].split("T")[0]
                )
    
                print(f'{flight_data.destination_city}: ${flight_data.price}')
                return flight_data
    ```
    
- flight_data.py
    
    ```python
    class FlightData:
        def __init__(self, price, origin_city, origin_airport, destination_city, destination_airport, out_date, return_date, stop_overs=0, via_city=''):
            self.price = price
            self.origin_city = origin_city
            self.origin_airport = origin_airport
            self.destination_city = destination_city
            self.destination_airport = destination_airport
            self.out_date = out_date
            self.return_date = return_date
    
            self.stop_overs = stop_overs
            self.via_city = via_city
    ```
    
- notification_manager.py
    
    ```python
    from twilio.rest import Client
    import os
    
    ACCOUNT_SID = os.environ["ACCOUNT_SID"]
    AUTH_TOKEN = os.environ["AUTH_TOKEN"]
    
    class NotificationManager:
        # This class is responsible for sending notifications with the deal flight details.
        def __init__(self):
            self.client = Client(ACCOUNT_SID, AUTH_TOKEN)
    
        def notification(self, message_body):
            message = self.client.messages.create(
                body=message_body,
                from_="+14066292558",
                to="+6581892983"
            )
    
            print(message.status)
    ```