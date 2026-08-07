## Creating Generic Alerts with Python

Pixelview allows users to integrate their existing moitoring backend with the system. Whatever monitoring tool you are using
you can use pixelvirt to aggregate it to a central view.

There is also a generic alerting template that allows users to integrate existing tools or any scrip they may already have
to trigger or resolve alerts in Pixelview system.

Below is an example of a simple python script that allows us to create an alert:

``` py title="create-alerts.py"
#!/usr/bin/env python

import argparse
import json
import sys
import requests

url = "https://pixelview.pixelvirt.com/api/new_event"

payload = {
  "alert_data": {"key1": "value1", "key2": "value2"},
  "generated_by": "Custom Monitoring Script",
  "hostname": "compute01.pixelview.com",
  "service_name": "Docker",
  "details": "Here is some details about the alert",
  "tags": "docker,compute01",
  "Title": "Docker service is not running",
  "status": "trigger", # trigger or resolve
  "severity": "CRITICAL",
}

headers = {"X-Auth-Key": "xxxxx-abb6822810b4"}
resp = requests.post(url, headers=headers, data=json.dumps(payload), verify=False)
print(resp.status_code)
print(resp.text)
```

!!! note
    Multiple trigger with the same data will not create new alert. As long as `hostname`, `service_name`, `title` and `status` are the
    same, it will be treated as re-alerting and as long as an open alert with the same info exists it will not trigger new alert.
