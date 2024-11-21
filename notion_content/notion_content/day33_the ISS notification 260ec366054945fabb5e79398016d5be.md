# day33_the ISS notification

```python
import requests
from datetime import datetime
import smtplib
import time

while True:
    MY_LAT = 51.507351  # Your latitude
    MY_LONG = -0.127758  # Your longitude

    response = requests.get(url="http://api.open-notify.org/iss-now.json")
    response.raise_for_status()
    data = response.json()

    iss_latitude = float(data["iss_position"]["latitude"])
    iss_longitude = float(data["iss_position"]["longitude"])

    # Your position is within +5 or -5 degrees of the ISS position.

    parameters = {
        "lat": MY_LAT,
        "lng": MY_LONG,
        "formatted": 0,
    }

    response = requests.get("https://api.sunrise-sunset.org/json", params=parameters)
    response.raise_for_status()
    data = response.json()
    sunrise = int(data["results"]["sunrise"].split("T")[1].split(":")[0])
    sunset = int(data["results"]["sunset"].split("T")[1].split(":")[0])

    time_now = datetime.now().hour

    my_email = 'jinghao.c@gmail.com'
    password = "my gmail app password"
    receiving_email = 'jinghao_chen@hotmail.com'

    if MY_LAT - 5 <= iss_latitude <= MY_LAT + 5 and MY_LONG - 5 <= iss_longitude <= MY_LONG + 5:
        if sunset <= time_now or time_now <= sunrise:
            with smtplib.SMTP("smtp.gmail.com") as connection:
                connection.starttls()
                connection.login(user=my_email, password=password)
                connection.sendmail(from_addr=my_email,
                                    to_addrs=receiving_email,
                                    msg="Subject:Look up!\n\n You may spot the ISS.")
    time.sleep(60)
```

- Retrieve the ISS current location from API <[Open Notify -- API Doc | ISS Current Location (open-notify.org)](http://open-notify.org/Open-Notify-API/ISS-Location-Now/)>
- Retrieve sunrise and sunset timing of my location from API <[https://api.sunrise-sunset.org/json](https://api.sunrise-sunset.org/json)>
- When the ISS passes through my location, and it is nighttime, the code will automatically send an email to remind me to look up.
- The code will run every 60s