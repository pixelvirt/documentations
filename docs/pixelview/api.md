# Pixelview API

Pixelview API can be used to interact with the Pixelview backend. You can use the API to various
things like create alert, and interact with existing alerts among other things.

What you need before you can use the API.

| Header       | Description                          |
| -------------| ------------------------------------ |
| `X-Username` | This is your email address that you use for login  |
| `X-Auth-Key` | This is the service key for the which you want to create alerts. This is only required to create alerts. |
| `X-API-Key`  | Your API Key (get from your profile page) |


### /api/alerts

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
