# Zabbix integration with Pixelview

## Overview

This documentation outlines the step-by-step process of integrating **Zabbix** with **PixelView** to enable seamless alert forwarding and enhanced visualization of monitoring data.

## Prerequisites
Before proceeding, ensure you have the following:

- A running **Zabbix Server**
- **PixelView account** with appropriate permissions
- Access to the **Zabbix alert scripts** directory
- **Python3** installed on the Zabbix server

## Create a Custom Alert Script in Zabbix
To send alerts from Zabbix to PixelView, a custom script must be created.

### Create the Alert Script
Log in to your Zabbix server and create the alert script in the appropriate directory:

```bash
sudo vim /usr/lib/zabbix/alertscripts/alertAgility.py
```

### Insert the Following Python Script
```python
#!/usr/bin/python3

import requests
import sys
import json
import logging.handlers

URL = "http://<alertagility_server>:<port>/api/new_event"

log = logging.getLogger(__name__)
log.setLevel(logging.DEBUG)
handler = logging.handlers.SysLogHandler(address='/dev/log')
formatter = logging.Formatter('%(module)s.%(funcName)s: %(message)s')
handler.setFormatter(formatter)
log.addHandler(handler)

def main(askey, subject, body, key):
    reqbody = {}
    itemLists = body.splitlines()
    for i in itemLists:
        k, v = i.split(":", 1)
        reqbody[k.strip()] = v.strip()
    headers = {'X-Auth-Key': key}
    resp = requests.post(URL, headers=headers, data=json.dumps(reqbody))
    if resp.status_code != 200:
        print(resp.text)
        sys.exit(1)

if __name__ == "__main__":
    to = sys.argv[1]
    subject = sys.argv[2]
    body = sys.argv[3]
    key = sys.argv[4]
    main(to, subject, body, key)
```

### Modify the Script
Replace `<alertagility_server>:<port>` with the IP and port where your **AlertAgility service** is running.

### Set File Permissions
```bash
sudo chmod +x /usr/lib/zabbix/alertscripts/alertAgility.py
sudo chown zabbix:zabbix /usr/lib/zabbix/alertscripts/alertAgility.py
```

### Restart Zabbix Server
```bash
sudo systemctl restart zabbix-server
```

## Generate a PixelView Service Key
To authenticate alerts from Zabbix, you need a service key from PixelView.

### Login to PixelView and Create a Service
1. Go to `Services > Add Service`
2. Enter a **Service Name** (e.g., "Zabbix Alerts")
3. Select **Zabbix** from the integration dropdown
4. Click **Create Template**
5. Copy the **Service Key**
6. Click **Save Template**

## Configure Zabbix Media Types
Zabbix needs to use the custom script for alert notifications.

### Add a New Media Type
- Go to `Alerts > Media Types > Create Media Type`
- Enter the following details:
    - **Name:** PixelView\_alerts
    - **Type:** Script
    - **Script Name:** alertAgility.py
    - **Script Parameters:**
        - `{ALERT.SENDTO}`
        - `{ALERT.SUBJECT}`
        - `{ALERT.MESSAGE}`
    - **Key** (Paste the **Service Key** from PixelView)

### Create Message Templates
- For **Problems:**
    - **Subject:** `Problem: {EVENT.NAME}`
    - **Message:**
        ```
        status:{EVENT.STATUS}
        service_name:{EVENT.NAME}
        hostname:{HOST.NAME}
        host_id:{HOST.ID}
        severity:{EVENT.SEVERITY}
        event_id:{EVENT.ID}
        event_opsdata:{EVENT.OPDATA}
        event_tags:{EVENT.TAGS}
        ```

- For **Problem Recovery**:
    - **Subject:** `Resolved in {EVENT.DURATION}: {EVENT.NAME}`
    - **Message:**
        ```
        status:OK
        service_name:{EVENT.NAME}
        hostname:{HOST.NAME}
        host_id:{HOST.ID}
        severity:{EVENT.SEVERITY}
        event_id:{EVENT.ID}
        event_opsdata:{EVENT.OPDATA}
        ```
- Click **Update** to save.

## Configure Zabbix Actions
This step ensures alerts are sent to the right recipients.

### Create an Action in Zabbix
- Go to `Action > Trigger Action > Create Action`
- Set an **Action Name** (e.g., "Send Alerts to PixelView")
- Define **Conditions** to determine which alerts should be sent
- Select **Host Groups** for which alerts will be triggered

### Set Up Operations
- In Operations, click **Add:**
    - Select **Users/User Groups**
    - In **Send Only To**, select **PixelView\_alerts**
    - Define any additional conditions
    - Click **Update**
- For Recovery Operations: Choose **Notify all involved** or select specific users
- Click **Update** to save

## Creating Zabbix User with Admin Permission
Before creating Trigger actions, you need a user with admin permission.

### Create a New User in Zabbix
- Go to `Users > Users > Create User`
- Enter User Details
- Click **Media**, then **Add**
- Select **PixelView\_alerts** as Media Type
- Enter the username in **Send to**
- Configure other options as needed
- Click **Save**

## Conclusion
By integrating **Zabbix** with **PixelView**, you gain a robust alerting solution that enhances monitoring efficiency, reduces downtime, and improves overall system reliability. This integration ensures real-time alerts are sent to the right users, enabling prompt action on critical events.
