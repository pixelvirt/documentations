# Services Overview

The **Services** section (`/services`) under **Services** serves as the central alerting ingestion hub in PixelView. It connects external monitoring systems (such as Prometheus Alertmanager, Zabbix, or generic monitoring pipelines) to PixelView's incident management framework, linking incoming alert streams directly to automated escalation policies and outbound notification webhooks.

---

## Navigating to Services

To access configured alerting services:

* In the left navigation sidebar under **Services**, click **All Services**:

<a href="../../images/services-table.png" class="glightbox">
  <img src="../../images/services-table.png" alt="Services Overview Table">
</a>

### Services Table Overview

The main table lists all configured alerting services with 60-second background polling for synchronized status tracking:

| Column | Description |
| :--- | :--- |
| **Name** | User-defined display name for the monitoring stream (e.g., `ha-monitoring`). |
| **Alerts Type** | Identifier of the monitoring integration platform (e.g., `generic`, `prometheus`, `zabbix`). |
| **Escalation Policy** | Name of the bound escalation workflow (e.g., `test-policy`) responsible for routing alerts when incidents trigger. |
| **Status** | Operational status badge indicating whether alert ingestion is **Enabled** (green) or **Disabled**. |
| **Subscribed** | Personal notification subscription status badge indicating whether the logged-in operator is **Subscribed** (green) or **Not subscribed**. |
| **Actions** | Context action menu (`...`) for subscribing, editing, or deleting services. |

### Table Toolbar Controls

The top-right toolbar provides dynamic Material React Table controls:

* **Search / Global Filter**: Instant full-text search across all service records.
* **Column Filters**: Filter records by individual column values.
* **Show/Hide Columns**: Toggle the visibility of specific columns.
* **Density Toggle**: Switch between compact and comfortable table row spacing.
* **Refresh**: Instantly query backend state to refresh service listings and escalation bindings.
* **Add Service (`+`)**: Launch the new service creation dialog.

---

## Creating a New Service

To register a new monitoring alert stream:

* Click the orange **`+`** (Add Service) button on the table toolbar:

<a href="../../images/services-add-button.png" class="glightbox">
  <img src="../../images/services-add-button.png" alt="Add Service Toolbar Button">
</a>

* The **Add New Service** dialog will open:

<a href="../../images/services-create-modal.png" class="glightbox">
  <img src="../../images/services-create-modal.png" alt="Add New Service Dialog">
</a>

### Configuration Fields

* **Service Name** *(Required)*: Enter a unique, descriptive identifier for this service stream (e.g., `ha-monitoring`, `k8s-production-alerts`).
* **Integration Type** *(Required)*: Select the integration type corresponding to the external monitoring platform from the dropdown menu (e.g., `generic`, `prometheus`, `zabbix`).

### Dialog Actions

* **Cancel**: Abort service creation and close the dialog.
* **Clear**: Reset all form inputs.
* **CREATE TEMPLATE**: Submit and register the new service. PixelView will generate a unique cryptographic **Service Key** and make the service available in the table.

---

## Managing Subscriptions & Context Actions

Each service row includes an **Actions** context menu (`...`) to manage subscriptions, modify service configurations, or delete the stream:

* Click the **`...`** button in the **Actions** column of the target service:

<a href="../../images/services-context-menu.png" class="glightbox">
  <img src="../../images/services-context-menu.png" alt="Services Context Actions Menu">
</a>

### Available Actions

* **Subscribe / Unsubscribe** (Bell icon): Toggle whether you receive real-time notifications when alerts arrive on this service stream. A confirmation dialog will prompt to verify the action.
* **Edit** (Pencil icon): Open the **Edit Service** configuration modal to update service parameters, copy the Service Key, configure outbound webhooks, or bind escalation policies.
* **Delete** (Trash icon): Permanently remove the service stream after confirmation.

> [!NOTE]
> Operators with standard user permissions can toggle **Subscribe / Unsubscribe**. Administrative privileges are required to **Edit** or **Delete** configured services.

---

## Editing & Configuring a Service

To inspect service keys, modify status, configure outbound webhooks, or attach escalation policies:

* In the **Actions** context menu (`...`), select **Edit**.
* The **Edit Service** dialog will open:

<a href="../../images/services-edit-modal.png" class="glightbox">
  <img src="../../images/services-edit-modal.png" alt="Edit Service Dialog">
</a>

### General Settings

* **Service Name**: Update the display name of the service stream.
* **Service Type**: Displays the immutable integration type (e.g., `generic`).
* **Service Key**: Read-only unique UUID generated by PixelView for this service.
    * Click the **Copy** icon at the right of the field to copy the Service Key to your clipboard.

> [!TIP]
> The **Service Key** authenticates incoming alert webhooks sent by external monitoring systems. For example, Prometheus Alertmanager routes alerts to:
> ```text
> http://<pixelview-host>/webhook/prometheus?servicekey=<service_key>
> ```

* **Status**: Toggle between **Enabled** and **Disabled**. When disabled, incoming alerts on this stream are ignored.

### Outbound Webhooks

The **Webhooks** section allows forwarding alert payloads received on this service to external endpoints (such as chat relays, incident management systems, or custom API endpoints):

* **Active Webhooks Table**: Lists all active webhook endpoints with `ID`, `Name`, and `URL`.
* **Add Webhook (`+`)**: Click the orange plus icon on the webhooks toolbar to expand the inline registration form:
    * **Name**: Descriptive name for the webhook destination.
    * **URL**: Target HTTP/HTTPS endpoint URL.
    * Click **Add Webhook** (green checkmark) to add the webhook to the list.
* **Delete Webhook**: Click the trash icon on any webhook row to remove it.

### Escalation Policy Binding

The **Escalation** section links the service stream directly to an automated incident escalation workflow:

* **Escalation Policy**: Select an escalation workflow from the dropdown menu (e.g., `test-policy`) or select `None`.
* When alerts trigger on this service, PixelView executes the configured escalation policy tiers (routing to automation bots, individual team members, or active on-call engineers).

### Saving Changes

* **Cancel**: Close the modal and discard unsaved modifications.
* **Clear Changes**: Revert form values back to their previously saved state.
* **UPDATE SERVICE**: Persist the updated configuration to the backend.

---

## Deleting a Service

* From the **Actions** context menu (`...`), select **Delete**.
* Confirm the prompt (`Are you sure you want to delete [service name]?`).

> [!WARNING]
> Deleting an alerting service invalidates its **Service Key**. External monitoring systems sending alerts with this key will receive authentication errors, and incoming alert routing for this stream will permanently stop.
