# Prometheus integration with Pixelview
## Setups
To enable alertmanger to send prometheus alerts to pixelview, ensure the following:

### On PixelView
#### Add `Prometheus` service in pixelview
- Go to `Services` -> `All Services`

    <a href="../images/prometheus1.jpg" class="glightbox">
      <img src="../images/prometheus1.jpg">
    </a>

- Click on `+` icon on top right to create a new service

    <a href="../images/prometheus2.jpg" class="glightbox">
      <img src="../images/prometheus2.jpg">
    </a>

- Give it a service name
- Select Prometheus from the dropdown menu
- Click `Create Template`

    <a href="../images/prometheus3.jpg" class="glightbox">
      <img src="../images/prometheus3.jpg">
    </a>

#### Copy service key
- Once the service is created, click `...` on the service row and click `edit`

    <a href="../images/prometheus4.jpg" class="glightbox">
      <img src="../images/prometheus4.jpg">
    </a>

- Copy the `Service Key`, and click `cancel`

    <a href="../images/prometheus5.jpg" class="glightbox">
      <img src="../images/prometheus5.jpg">
    </a>

#### Subscribe to the service
- click `...` on the service row and click `Subscribe`

    <a href="../images/prometheus6.jpg" class="glightbox">
      <img src="../images/prometheus6.jpg">
    </a>


### On Prometheus
#### Change the receiver
```yaml
receiver: 'pixelview'
```

#### Add the receiver config
```yaml
  - name: 'pixelview'
    webhook_configs:
      - url: 'http://[pixelview_url]/webhook/prometheus?servicekey=[service_key]'
        send_resolved: true
```
!!! note
    - Replace [pixel_view] with the actual PixelView URL
    - Replace [service_key] with the actual key that was copied earlier

Once the Prometheus service is added successfully, you will start getting alerts in `Cases`.