## Integrate prometheus alertmanger with pixelview

In order for alertmanger to send prometheus alerts to pixelview please ensure that you do the following:

1. Setup a service to receive prometheus alerts
2. Copy the service key
3. Make sure that alertmanager config has the follwing receiver

```
    receivers:
      - name: 'null'
      - name: 'all_alerts_receiver' # pixelview
        webhook_configs:
          - url: 'https://<your pixelview url>/webhook/prometheus?servicekey=xxx-xxx-xxx-xxx'
            send_resolved: true
```

Make sure that your servicekey is the service key of the service you created above.
