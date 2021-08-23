# Phantombuster_API_Tips
Few useful use case of the [phantombuster](https://phantombuster.com?deal=fabian15) API

# Update multiple cookies at once
Python code to update all linkedin cookies at once. Edit API_key and newCookie
```
#Parameters
API_key = ""
newCookie = ""
#################

import requests
import json
import re

#regex to update the cookies
regexCookie = '"sessionCookie": .*,'
replace = '"sessionCookie": "' + newCookie + '" ,'


#get list of agent
url = "https://api.phantombuster.com/api/v2/agents/fetch-all"
headers = {"Accept": "application/json"}
response = requests.request("GET", url, params={"key": API_key})

print(response.text)
data = response.text
data = pd.read_json(data)

#API to get current argument
url = "https://api.phantombuster.com/api/v2/agents/fetch"
headers = {"Accept": "application/json"}

#API to update argument
url2 = "https://api.phantombuster.com/api/v2/agents/save"
headers2 = {    "Accept": "application/json",    "Content-Type": "application/json"}


for id in data['id']:
    #get current argument
    params={"key": API_key,"id": id}
    response = requests.request("GET", url, headers=headers, params = params)
    json_data = json.loads(response.text)
    argument = json_data["argument"]
    
    #Update argument
    new = re.sub(regexCookie,  replace, argument)
    
    #remove line breaks
    new = new.replace("\n\t", "")
    new = new.replace("\n", "")
    #new = '"""'+new + '"""'
    
    json_data["argument"] = new
    payload = {    "id": str(id), "argument": new }
    
    response = requests.request("POST", url2,json=payload, headers=headers2, params={"key": API_key})

```

# Unchedule all Phantom with Zapier
Replace 1111 by the API key
```
https://api.phantombuster.com/api/v2/agents/unschedule-all?key=1111
```
Select trigger "New Ouput in Phantombuster
Add a stage "Filter by Zapier". In condition select "Exit Code" Greater than 0 (It means that an error ooccured with the Phantom)
Add a stage "Webhook by Zapier". Pick "Get" as Action Event. Paste the code above as url.
