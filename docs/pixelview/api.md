# Pixelview API

Pixelview API can be used to interact with the Pixelview backend. You can use the API to various
things like create alert, and interact with existing alerts among other things.

What you need before you can use the API.

| Header       | Description                          |
| -------------| ------------------------------------ |
| `X-Username` | This is your email address that you use for login  |
| `X-Auth-Key` | This is the service key for the which you want to create alerts. This is only required to create alerts. |
| `X-API-Key`  | Your API Key (get from your profile page) |


### Alerts (/api/alerts)

``` shell title="Get all open alerts"
curl "https://pixelview.pixelvirt.com/api/alerts" -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1"
```

``` shell title="Get all open alerts for a given service"
curl "https://pixelview.pixelvirt.com/api/alerts?service_uuid=a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1"
```

``` shell title="Get details of a given alert with incident_key"
curl "https://pixelview.pixelvirt.com/api/alerts/52183169-9472-4afe-97e7-e6fa670c86f1" -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1"
```

``` shell title="Update the status of an alert"
curl "https://pixelview.pixelvirt.com/api/alerts/52183169-9472-4afe-97e7-e6fa670c86f1" -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X PUT -d '{"status": "acknowledged"}'
```

``` shell title="Type Incident Activity or Alert Activity"
// IncidentActivity represents the structure for posting incident activity
type IncidentActivity struct {
        IncidentKey      string                 `json:"incident_key" bson:"incident_key"`
        ActivityType     string                 `json:"activity_type" bson:"activity_type"`
        Description      string                 `json:"description" bson:"description"`
        Details          string                 `json:"details" bson:"details"`
        ServiceName      string                 `json:"servicename" bson:"servicename"`
        Hostname         string                 `json:"hostname" bson:"hostname"`
        AutomationResult StackstormNotification `json:"automation_result" bson:"automation_result"`
        RawData          interface{}            `json:"raw_data" bson:"raw_data"`
}
```

``` shell title="Add Activity to alert Timeline"
curl "https://pixelview.pixelvirt.com/api/alerts/52183169-9472-4afe-97e7-e6fa670c86f1/activities" -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"activity_type": "Acknowledged", "incident_key": "52183169-9472-4afe-97e7-e6fa670c86f1"}'
```

### Comments
``` shell title="Type Comment"
    type Comment struct {
        Body         string    `json:"body" bson:"body"`
        TaskUUID     string    `json:"task_uuid" bson:"task_uuid"`
        CaseUUID     string    `json:"case_uuid" bson:"case_uuid"`
        IncidentKey  string    `json:"incident_key" bson:"incident_key"`
    }
```

!!! note
    One of `incident_key` or `case_uuid` is required. If you pass both, incident_key is used.

``` shell title="Create/Add New Comment"
curl "https://pixelview.pixelvirt.com/api/comment_create"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"incident_key": "I have Acknowledged this alert.", "incident_key": "52183169-9472-4afe-97e7-e6fa670c86f1"}'
```

``` shell title="Delete a Comment"
curl "https://pixelview.pixelvirt.com/api/comment_delete"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"incident_key": "52183169-9472-4afe-97e7-e6fa670c86f1"}'
```
