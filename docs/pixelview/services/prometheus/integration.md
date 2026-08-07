# Prometheus integration with Pixelview
## Setups
In order for alertmanger to send prometheus alerts to pixelview please ensure that you do the following:

- Setup a service to receive prometheus alerts
- Copy the service key
- Make sure that alertmanager config has the follwing receiver

```
    receivers:
      - name: 'null'
      - name: 'all_alerts_receiver' # pixelview
        webhook_configs:
          - url: 'https://<your pixelview url>/webhook/prometheus?servicekey=xxx-xxx-xxx-xxx'
            send_resolved: true
```

Make sure that your servicekey is the service key of the service you created above.
