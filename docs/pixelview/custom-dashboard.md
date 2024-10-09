# Custom Dashborad
Pixelview dashboard is done in a way that allows users to expose data from any application to show in Pixelview.
The dashboard expect you to expose your own dashboard/data and simply calls your data to show in Pixelview.

# get-chart
/get-charts
```json
{
	"charts":[
		{
			"name": "chart-name-1",
			"source": "data-url-for-chart-1"
		},
		{
			"name": "chart-name-2",
			"source": "data-url-for-chart-2"
		}
	]
}
```

# table
/list-object-details?object="object-id" (data-source-url-for-dashboard)
```json
{
	"name": "example-name",
	"type": "table",
	"labels": ["label-1", "label-2", "label-3"],
	"source_name": "source-name",
	"data": [
		{
			"values": ["data-1", "data-2", "data-3"],
			"on_click_action": "",
			"source": "on-click-action-data-source-url"
		}
	]
}
```
note: on_click_action can be popup, newpage

if on_click_action is "popup", then json will be displayed in the popup window.
if on_click_action is "newpage", then follow the following format,
```json
{
	"name": "display-title-name",
	"charts":[
		{
			"name": "chart-name-1",
			"source": "data-url-for-chart-1"
		},
		{
			"name": "chart-name-2",
			"source": "data-url-for-chart-2"
		}
	] ,
	"status": "object-status",
	"cards": [
		{
			"name": "card-name-1",
			"value": "value1"
		},
		{
			"name": "card-name-2",
			"value": "value2"
		}
	]
}
```


# piechart
/list-object-details?object="object-id" (data-source-url-for-dashboard)
```json
{
	"name": "example-name",
	"type": "piechart",
	"labels": ["label-1", "label-2", "label-3"],
	"source_name": "source-name",
	"data": [1, 2.1, 3.7]
}
```


# linechart
/list-object-details?object="object-id" (data-source-url-for-dashboard)
```json
{
	"name": "example-name",
	"type": "linechart",
	"source_name": "source-name",
	"axis": ["x-axis-lebel", "y-axix-lebel"],
	"min": 0,
	"max": 100,
	"series": [
		{
			"name": "line-name-1",
			"data": [ [1.2, 2.1], [3, 4.3] ]
		},
		{
			"name": "line-name-2",
			"data": [ [4, 5.2], [7.7, 8.1] ]
		}
	],
}
```

# barchart
/list-object-details?object="object-id" (data-source-url-for-dashboard)
```json
{
	"name": "example-name",
	"type": "barchart",
	"source_name": "source-name",
	"axis": ["x-axis-lebel", "y-axix-lebel"],
	"min": 0,
	"max": 100,
	"series": [
		{
			"name": "line-name-1",
			"data": [ [1.2, 2.1], [3, 4.3] ]
		},
		{
			"name": "line-name-2",
			"data": [ [4, 5.2], [7.7, 8.1] ]
		}
	],
}
```
