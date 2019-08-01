---
layout: post
title: Automating Google Calendar with Python using Google Calendar API
permalink: /automate-google-calendar-with-python/
categories:
  - python
  - Google Calendar API
---

Google Calendar API allows you automate your calendar with coding. Isn't that beautiful 😊 ? You can use Java, PHP or Ruby for this, but in this post, I'm gonna use Python as an example.

## 1. Create a project in Google Developer Console

Go to [Google Developers Console](https://console.developers.google.com/) and create a new project.
Then go to the *Dashboard*, search for `Google Calendar API` in the search bar and enable it for your project.

## 2. Generate application credentials for Google Calendar API

Go to *Credentials* -> *OAuth Consent Screen*, provide an application name and then select all the necessary scopes that you would like to obtain permissions for.

For this example, I will select permision as below

![example]({{ site.baseurl }}/images/gcal-scope.png)

Once the application scope and consent screen is setup, we now generate credentials by navigating to `Credentials` tab. Click `Create credentials` and choose `OAuth client ID`

Then download the credentials were just created into JSON file and rename it as `credentials.json`. Save it in the same directory where you will write your code.

Now we can just skip all these boring steps and go to fun part (Fun ke weh? HAHAHA)

## 3. Using Google Auth SDK for Python

```bash
# Install libraries
pip install --upgrade google-api-python-client google-auth-httplib2 google-auth-oauthlib
```

Set up OAuth, let us create a file named `cal_service.py` with the code below:

```python
import datetime
import pickle
import os.path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request

# More details for scopes to choose...
# https://developers.google.com/calendar/auth
SCOPES = ['https://www.googleapis.com/auth/calendar']

CREDENTIALS_FILE = 'credentials.json'

def get_calendar_service():
    creds = None

    # The file token.pickle stores the user's access and refresh tokens, and is
    # created automatically when the authorization flow completes for the first
    # time.
    if os.path.exists('token.pickle'):
        with open('token.pickle', 'rb') as token:
            creds = pickle.load(token)
    # If there are no (valid) credentials available, let the user log in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                CREDENTIALS_FILE, SCOPES)
            creds = flow.run_local_server(port=0)

        # Save the credentials for the next run
        with open('token.pickle', 'wb') as token:
            pickle.dump(creds, token)

    service = build('calendar', 'v3', credentials=creds)

    try:
        # Returns the rules in the access control list for the calendar.
        acl = service.acl().list(calendarId='primary').execute()
        # print(acl)
    except Exception as e:
        err_type = e.__class__.__name__
        
        # re-authenticating if not valid auth
        if err_type == "RefreshError":
            flow = InstalledAppFlow.from_client_secrets_file(
                CREDENTIALS_FILE, SCOPES)
            creds = flow.run_local_server(port=0)

            with open('token.pickle', 'wb') as token:
                pickle.dump(creds, token)

            service = build('calendar', 'v3', credentials=creds)

    return service
```

## 4. Playing around

To start playing around with the calendar, we create test file named `playground.py` in the root of the project and import these at top of the file:

```python
from datetime import datetime, timedelta
from cal_service import get_calendar_service
```

### List all Calendars

```python
def gcal_get_list_of_calendars():
  
    service = get_calendar_service()

    # Call the Calendar API
    calendars_result = service.calendarList().list().execute()
    calendars = calendars_result.get('items', [])

    if not calendars:
        print('No calendars found.')
    for calendar in calendars:
        summary = calendar['summary']
        id = calendar['id']
        primary = True if calendar.get('primary') else False

        print("%s\t%s\t%s" % (summary, id, primary))
```

### Get events from Calendar

```python
def gcal_get_list_of_events():

    service = get_calendar_service()
    # Call the Calendar API
    # now = datetime.utcnow().isoformat() + 'Z'  # 'Z' indicates UTC time

    events_result = service.events().list(
        calendarId='primary',
        maxResults=10,
        # timeMin=now,
        # singleEvents=True,
        # orderBy='startTime'
    ).execute()
    events = events_result.get('items', [])

    if not events:
        print('No upcoming events found.')
    for event in events:
        start = event['start'].get('dateTime', event['start'].get('date'))
        end = event['start'].get('dateTime', event['end'].get('date'))
        print("{} - {}, {}".format(start, end, event['summary']))
```

### Create an event

```python
def gcal_create_event():

    service = get_calendar_service()

    d = datetime.now().date()
    tomorrow = datetime(d.year, d.month, d.day, 10)+timedelta(days=1)
    start = tomorrow.isoformat()
    end = (tomorrow + timedelta(hours=1)).isoformat()

    event_result = service.events().insert(
        calendarId='primary',
        body={
            "summary": 'Automating calendar',
            "description": 'This is a tutorial example of automating google calendar with python',
            "start": {"dateTime": start, "timeZone": 'Asia/Kuala_Lumpur'},
            "end": {"dateTime": end, "timeZone": 'Asia/Kuala_Lumpur'},
            # "attendees": [
            #     {"email": "fadziljusri@gmail.com", "responseStatus": "accepted" }
            # ],
            "guestsCanInviteOthers": False,
            "location": "KidoCode",
        },
        conferenceDataVersion=None,
        sendUpdates=None
    ).execute()

    print("created event")
    print("id: ", event_result['id'])
    print("summary: ", event_result['summary'])
```

### Get a specific event

```python
def gcal_get_specific_event():

    service = get_calendar_service()

    try:
        event = service.events().get(
            calendarId=KSCHEDULE_CALENDAR_ID,
            eventId='<YOUR EVENT ID>'
        ).execute()
        
        print("event", event)
        
    except Exception as e:
        print("Event not exist!")
```

### Update an event

```python
def gcal_update_event():
    service = get_calendar_service()

    d = datetime.now().date()
    tomorrow = datetime(d.year, d.month, d.day, 10)+timedelta(days=1)
    start = tomorrow.isoformat()
    end = (tomorrow + timedelta(hours=1)).isoformat()

    event_result = service.events().update(
        calendarId='primary',
        eventId='<YOUR EVENT ID>',
        # Note that previous 'body' of event will be replaced with any new 'body' field, not merging it (attendees related etc) 
        body={
            "summary": 'Updated Automating calendar',
            "description": 'This is a tutorial example of automating google calendar with python, updated time.',
            "start": {"dateTime": start, "timeZone": 'Asia/Kuala_Lumpur'},
            "end": {"dateTime": end, "timeZone": 'Asia/Kuala_Lumpur'},
            "attendees": [
                {"email": "fadziljusri@gmail.com", "responseStatus": "accepted"},
            ],
            "reminders": {
                "overrides": [{"minutes": 60 * 8, "method": "popup"}],
                "useDefault": False
            },
            "guestsCanInviteOthers": False,
            "location": "KidoCode"
        },
        conferenceDataVersion=0,
        sendUpdates="none"
    ).execute()

    print("updated event")
    print("id: ", event_result['id'])
    print("summary: ", event_result['summary'])
```

### Delete an Event

```python
def gcal_delete_event():
    
    # Delete the event
    service = get_calendar_service()
    try:
        service.events().delete(
            calendarId='primary',
            eventId='<YOUR EVENT ID>',
            sendUpdates=None
        ).execute()
        
        print("Event deleted")
    except Exception as e:
        print("Failed to delete event")
```

## 5. API References

There are alot more things you can do with the API, enjoy~


1. [https://developers.google.com/calendar/v3/reference/](https://developers.google.com/calendar/v3/reference/)
2. [https://developers.google.com/resources/api-libraries/documentation/calendar/v3/python/latest/calendar_v3.events.html#list](https://developers.google.com/resources/api-libraries/documentation/calendar/v3/python/latest/calendar_v3.events.html#list)

