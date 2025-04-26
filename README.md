📡 ISS Overhead Notifier 🚀
This Python script checks if the International Space Station (ISS) is currently overhead your location and if it's nighttime—so you can actually see it! If both conditions are met, it sends an email notification.

🔧 Features:
Fetches real-time ISS location using the Open Notify API

Checks local sunrise/sunset times using the Sunrise-Sunset API

Sends an email alert when the ISS is overhead at night

📁 iss_notifier.py
python
Copy
Edit
import requests
from datetime import datetime
import smtplib
import time
"

# Your location
MY_LAT = 17.275984
MY_LONG = 74.200325

def is_iss_overhead():
    """Check if the ISS is currently overhead (±5° latitude/longitude)."""
    response = requests.get(url="http://api.open-notify.org/iss-now.json")
    response.raise_for_status()
    data = response.json()

    iss_latitude = float(data["iss_position"]["latitude"])
    iss_longitude = float(data["iss_position"]["longitude"])

    return (MY_LAT - 5 <= iss_latitude <= MY_LAT + 5) and (MY_LONG - 5 <= iss_longitude <= MY_LONG + 5)

def is_night():
    """Check if it is currently night at your location."""
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

    time_now = datetime.utcnow().hour  # UTC to match API
    return time_now >= sunset or time_now <= sunrise

# Main loop
while True:
    time.sleep(60)
    if is_iss_overhead() and is_night():
        with smtplib.SMTP("smtp.gmail.com") as connection:
            connection.starttls()
            connection.login(user=MY_EMAIL, password=MY_PASSWORD)
            connection.sendmail(
                from_addr=MY_EMAIL,
                to_addrs=MY_EMAIL,
                msg="Subject:Look Up! 👨‍🚀\n\nThe ISS is above you in the sky!"
            )
⚠️ Notes
Be careful not to share your email or app password publicly.

You may want to store credentials in environment variables or use a .env file for security.

The datetime.utcnow() is used to match the UTC time format from the API.
