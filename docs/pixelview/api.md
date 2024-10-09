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

### Inventory

``` shell title="Type Inventory Cloud"
type Cloud struct {
        UUID        string                 `json:"uuid" bson:"uuid"`
	Name        string                 `json:"name" bson:"name"`
	Description string                 `json:"description" bson:"description"`
	Labels      []Label                `json:"labels" bson:"labels"`
	VaultData   VaultData              `json:"vault_data" bson:"vault_data"`
	Data        map[string]interface{} `json:"data" bson:"data"`
}
```

``` shell title="Create Inventory Cloud"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"name" "Cloud1", "description": "this is my cloud", "labels": ['abcd'], "data": {'a':'b'}}'
```

``` shell title="Update Inventory Cloud"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"name" "Cloud1", "description": "this is my cloud", "labels": ['abcd'], "data": {'a':'c'}}'
```

``` shell title="Delete Inventory Cloud"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"uuid": "f74b4df7-f3f4-4777-9562-796355555faf"}'
```

``` shell title="Type Inventory Region"
type Region struct {
	UUID        string                 `json:"uuid" bson:"uuid"`
	Name        string                 `json:"name" bson:"name"`
	Description string                 `json:"description" bson:"description"`
	Labels      []Label                `json:"labels" bson:"labels"`
	CloudUUID   string                 `json:"cloud_uuid" bson:"cloud_uuid"`
	Data        map[string]interface{} `json:"data" bson:"data"`
}
```

``` shell title="Create Inventory Region"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X POST -d '{"name" "Region1", "description": "this is region1 in cloud 1", "cloud_uuid": "f74b4df7-f3f4-4777-9562-796355555faf", "labels": ['abcd'], "data": {'one':'two'}}'
```

``` shell title="Update Inventory Region"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X PUT -d '{"name" "Region1", "description": "this is region1 in cloud 1", "cloud_uuid": "f74b4df7-f3f4-4777-9562-796355555faf", "labels": ['abcd'], "data": {'one':'three'}}'
```

``` shell title="Delete Inventory Region"
curl "https://pixelview.pixelvirt.com/api/inventory/cloud"  -H "X-Username: user@pixelvirt.com" -H "X-Auth-Key: a3b0d9445r-21e4-4c89-123-89e7cd8a3bfe" -H "X-API-Key: 99b0d943-21e4-4c89-abcx-o9e7cd8a3bf1" -X DELETE -d '{"uuid": "ec44b4df7-a3f4-3777-cc62-796355555fca"}'
```

``` shell title="Type Inventory Zone"
type Zone struct {
	UUID        string                 `json:"uuid" bson:"uuid"`
	Name        string                 `json:"name" bson:"name"`
	Description string                 `json:"description" bson:"description"`
	Labels      []Label                `json:"labels" bson:"labels"`
	RegionUUID  string                 `json:"region_uuid" bson:"region_uuid"`
	CloudUUID   string                 `json:"cloud_uuid" bson:"cloud_uuid"`
	Data        map[string]interface{} `json:"data" bson:"data"`
}
```

``` shell title="Type Inventory Server"
type Server struct {
	UUID       string                 `json:"uuid" bson:"uuid"`
	Name       string                 `json:"name" bson:"name"`
	Type       string                 `json:"type" bson:"type"`
	CloudID    int                    `json:"cloud_id" bson:"cloud_id"`
	RegionID   int                    `json:"region_id" bson:"region_id"`
	ParentID   int                    `json:"parent_id" bson:"parent_id"`
	IPAddress  string                 `json:"ip_address" bson:"ip_address"`
	DeviceType string                 `json:"device_type" bson:"device_type"`
	Active     bool                   `json:"active" bson:"active"`
	Note       string                 `json:"note" bson:"note"`
	Labels     []Label                `json:"labels" bson:"labels"`
	RegionUUID string                 `json:"region_uuid" bson:"region_uuid"`
	ZoneUUID   string                 `json:"zone_uuid" bson:"zone_uuid"`
	CloudUUID  string                 `json:"cloud_uuid" bson:"cloud_uuid"`
	Data       map[string]interface{} `json:"data" bson:"data"`
}
```

``` shell title="Type Inventory App"
type App struct {
	UUID        string                 `json:"uuid" bson:"uuid"`
	Name        string                 `json:"name" bson:"name"`
	Type        string                 `json:"type" bson:"type"`
	Description string                 `json:"description" bson:"description"`
	Labels      []Label                `json:"labels" bson:"labels"`
	RegionUUID  string                 `json:"region_uuid" bson:"region_uuid"`
	ZoneUUID    string                 `json:"zone_uuid" bson:"zone_uuid"`
	CloudUUID   string                 `json:"cloud_uuid" bson:"cloud_uuid"`
	Data        map[string]interface{} `json:"data" bson:"data"`
}
```

``` shell title="Type Inventory Netowrk Device"
type NetworkDevice struct {
	UUID       string                 `json:"uuid" bson:"uuid"`
	Name       string                 `json:"name" bson:"name"`
	Type       string                 `json:"type" bson:"type"`
	CloudID    int                    `json:"cloud_id" bson:"cloud_id"`
	RegionID   int                    `json:"region_id" bson:"region_id"`
	ParentID   int                    `json:"parent_id" bson:"parent_id"`
	IPAddress  string                 `json:"ip_address" bson:"ip_address"`
	DeviceType string                 `json:"device_type" bson:"device_type"`
	Active     bool                   `json:"active" bson:"active"`
	ModelName  string                 `json:"model_name" bson:"model_name"`
	OSVersion  string                 `json:"os_version" bson:"os_version"`
	VLANS      []int                  `json:"vlans" bson:"vlans"`
	Note       string                 `json:"note" bson:"note"`
	Labels     []Label                `json:"labels" bson:"labels"`
	RegionUUID string                 `json:"region_uuid" bson:"region_uuid"`
	ZoneUUID   string                 `json:"zone_uuid" bson:"zone_uuid"`
	CloudUUID  string                 `json:"cloud_uuid" bson:"cloud_uuid"`
	Data       map[string]interface{} `json:"data" bson:"data"`
}
```
