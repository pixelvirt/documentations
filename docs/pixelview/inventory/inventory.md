# Cloud Inventory & Catalogue

The **Catalogue** section (`/inventory`) under **Inventory** provides a complete hierarchical asset management system for cataloging, grouping, and monitoring all physical and virtual infrastructure resources across your multi-cloud estate.

Infrastructure in PixelView is organized in a structured tree hierarchy:

$$\text{Cloud} \longrightarrow \text{Region} \longrightarrow \text{Zone} \longrightarrow \text{Entities (Servers, Network Devices, Apps, Storage)}$$

---

## Navigating to Catalogue

To access the Cloud Inventory Catalogue:

* In the left navigation sidebar under **Inventory**, click **Catalogue**:

<a href="../../images/inventory-sidebar-select.png" class="glightbox">
  <img src="../../images/inventory-sidebar-select.png" alt="Inventory Catalogue Sidebar Navigation & Dashboard">
</a>

The top header provides contextual cloud switching and breadcrumb navigation:
* **Cloud Selector Dropdown**: Switch between configured cloud environments.
* **Refresh**: Reload live cloud telemetry and asset states.
* **Add Cloud (`+ Add Cloud`)**: Button to register a new cloud environment partition.

---

## Cloud Management

### Adding a New Cloud

To create a new top-level cloud environment:

* Click the **`+ Add Cloud`** button in the top-right header:

<a href="../../images/inventory-add-cloud-button.png" class="glightbox">
  <img src="../../images/inventory-add-cloud-button.png" alt="Add Cloud Button">
</a>

* The **Add a new cloud** modal dialog will appear:

<a href="../../images/inventory-add-cloud-modal.png" class="glightbox">
  <img src="../../images/inventory-add-cloud-modal.png" alt="Add a New Cloud Dialog">
</a>

Configure the following cloud attributes:

| Field | Requirement | Description |
| :--- | :--- | :--- |
| **Name** | Required | Unique display name identifying the cloud environment (e.g., `testCloud5`, `production-aws`). |
| **Description** | Optional | Operational notes or purpose of this cloud partition. |
| **Labels** | Optional | Key-value pairs for organizational tagging, ownership, and dynamic filtering. |
| **Data** | Optional | Custom environment key-value configurations and integration parameters. |

* Click **ADD CLOUD** to initialize the environment.

---

### Editing & Deleting Clouds

On the Cloud Overview dashboard, the **ACTIONS** panel provides cloud-level controls:

* Click **EDIT CLOUD**:

<a href="../../images/inventory-edit-cloud-button.png" class="glightbox">
  <img src="../../images/inventory-edit-cloud-button.png" alt="Edit Cloud Action Button">
</a>

* The **Edit Cloud** dialog allows updating name, description, labels, and custom data attributes:

<a href="../../images/inventory-edit-cloud-modal.png" class="glightbox">
  <img src="../../images/inventory-edit-cloud-modal.png" alt="Edit Cloud Dialog">
</a>

* Click **DELETE CLOUD** to permanently remove the cloud environment and its subordinate regions, zones, and assets.

---

## Region Management

A **Region** represents a geographical location or primary datacenter partition within a Cloud.

### Adding a Region

* In the cloud dashboard's **ACTIONS** panel, click **`+ ADD REGION`**:

<a href="../../images/inventory-add-region-button.png" class="glightbox">
  <img src="../../images/inventory-add-region-button.png" alt="Add Region Action Button">
</a>

* The **Add a new region** dialog will open:

<a href="../../images/inventory-add-region-modal.png" class="glightbox">
  <img src="../../images/inventory-add-region-modal.png" alt="Add a New Region Dialog">
</a>

* Configure Region Name (e.g., `testRegion`, `us-east-1`), Description, Labels, and Data parameters.
* Click **ADD REGION** to save.

---

### Viewing a Region

* Under the **REGIONS** section of the Cloud Dashboard, click **View region $\rightarrow$** on any region card (such as `testRegion`):

<a href="../../images/inventory-view-region-card.png" class="glightbox">
  <img src="../../images/inventory-view-region-card.png" alt="Region Card Navigation">
</a>

* The **Region Overview** dashboard opens, displaying the region breadcrumb path (`testCloud5 > testRegion`), description, labels, and administrative action controls (**EDIT REGION**, **`+ ADD ZONE`**, **DELETE REGION**):

<a href="../../images/inventory-region-overview.png" class="glightbox">
  <img src="../../images/inventory-region-overview.png" alt="Region Overview Dashboard">
</a>

---

## Zone Management

A **Zone** (Availability Zone or Datacenter Rack Partition) represents an isolated failure domain within a Region that hosts compute, network, application, and storage assets.

### Adding a Zone

* On the Region Overview dashboard, click **`+ ADD ZONE`** in the **ACTIONS** panel.
* In the **Add a new zone** dialog, enter the Zone Name (e.g., `testZone`, `zone-a`), optional Description, Labels, and custom Data attributes.
* Click **ADD ZONE** to create the zone within the current region.

---

### Editing & Deleting Regions and Zones

* **Edit Region / Zone**: Click **EDIT REGION** (on Region Overview) or **EDIT ZONE** (on Zone Overview) to modify the display name, description, tags, or custom data mappings.
* **Delete Region / Zone**: Click **DELETE REGION** or **DELETE ZONE** to remove the partition. Note that a zone cannot be deleted while child assets (servers, network devices, apps, storage) are still active within it.

---

### Viewing a Zone

* On the Region Overview dashboard, locate the target zone under **ZONES** and click **View zone $\rightarrow$**:

<a href="../../images/inventory-view-zone-card.png" class="glightbox">
  <img src="../../images/inventory-view-zone-card.png" alt="Zone Card Navigation">
</a>

* The **Zone Details** dashboard opens with four asset management tabs:
    * **SERVER**: Virtual machines, bare-metal nodes, and hypervisors.
    * **NETWORK DEVICES**: Switches, routers, load balancers, and firewalls.
    * **APP**: Packaged software applications and standalone services.
    * **STORAGE**: Block storage, NFS mounts, volume types, and object stores.

---

## Server Asset Management

### Inspecting Servers

* Inside the Zone view, the **SERVER** tab displays all registered compute hosts:

<a href="../../images/inventory-zone-server-tab.png" class="glightbox">
  <img src="../../images/inventory-zone-server-tab.png" alt="Servers Inventory Tab">
</a>

| Column | Description |
| :--- | :--- |
| **ID/Name** | Server display name (e.g., `testServer`) and unique system UUID with quick-copy button. |
| **IP Address** | Primary management IP address (e.g., `1.1.1.1`, `159.135.206.19`). |
| **Device Type** | Hardware or virtualization classification (`Virtual Machine`, `Bare Metal`). |
| **Operating System** | Detected OS platform (e.g., `Linux`, `Ubuntu`). |
| **Status** | Real-time health indicator (Green dot for Active). |
| **Actions** | Context menu (`...`) for editing or deleting the server record. |

---

### Adding a New Server

* Click the orange **`+`** (Add Server) button on the table toolbar:

<a href="../../images/inventory-add-server-button.png" class="glightbox">
  <img src="../../images/inventory-add-server-button.png" alt="Add Server Button">
</a>

* Configure server specifications in the **Add a new server** dialog:

<a href="../../images/inventory-add-server-modal.png" class="glightbox">
  <img src="../../images/inventory-add-server-modal.png" alt="Add a New Server Dialog">
</a>

* **Name**: Display name for the server (e.g., `testServer`).
* **Username & Password**: Administrator login credentials with visibility toggle.
* **Operating System**: Target OS name (e.g., `Linux`, `Ubuntu 22.04`).
* **IP Address** *(Required)*: IPv4 or IPv6 management endpoint.
* **Device Type**: Classification from the dropdown (`Virtual Machine`, `Physical Server`).
* **Active Toggle**: Enable or disable active status monitoring.
* **Hardware Specs**: Allocated CPU cores, Memory (MB/GB), and GPU accelerators.
* Click **ADD SERVER** to register the host.

---

### Server Details Dashboard

* Click on any server name in the table to open its full telemetry dashboard:

<a href="../../images/inventory-server-details.png" class="glightbox">
  <img src="../../images/inventory-server-details.png" alt="Server Details Dashboard">
</a>

* **Hardware Telemetry Ribbon**: Real-time metrics for CPU cores (`16`), Memory (`1024 MB`), GPU units (`2`), and Operating System (`Linux`).
* **General Info Card**: Name, UUID, Management IP Address (`1.1.1.1`), Device Type (`Virtual Machine`), and attached Credential.
* **Location & Status Card**: Physical location (`Kathmandu`), Status badge (`Active`), and operator notes.
* **Infrastructure Card**: Parent Cloud (`testCloud`), Region (`testRegion`), and Zone (`testZone`) with unique system UUIDs.
* **Operational Cards**: Live indicators for Patch Status and Automation Status.
* **Top Right Actions**:
    * **Edit Host (Pencil)**: Update server configuration, IP, or specifications.
    * **Attach Credential (Key)**: Assign SSH keys or authentication profiles for automation.
    * **Delete Host (Trash)**: Remove host from inventory.

---

## Network Devices Management

### Inspecting Network Devices

* Switch to the **NETWORK DEVICES** tab within the Zone dashboard:

<a href="../../images/inventory-zone-network-devices-tab.png" class="glightbox">
  <img src="../../images/inventory-zone-network-devices-tab.png" alt="Network Devices Inventory Tab">
</a>

* Displays network equipment names (e.g., `testNetworkDevice`), IP addresses, device types, model names, active statuses, and row action menus.

---

### Adding a Network Device

* Click the orange **`+`** (Add Network Device) button on the table toolbar:

<a href="../../images/inventory-add-network-device-button.png" class="glightbox">
  <img src="../../images/inventory-add-network-device-button.png" alt="Add Network Device Button">
</a>

* Configure network parameters in the **Add a new network device** dialog:

<a href="../../images/inventory-add-network-device-modal.png" class="glightbox">
  <img src="../../images/inventory-add-network-device-modal.png" alt="Add a New Network Device Dialog">
</a>

* **Name & Type**: Identifier name and device classification (`Router`, `Switch`, `Firewall`).
* **IP Address & Device Type**: Management IP and device category.
* **Active Toggle**: Enable active monitoring state.
* **Model Name & OS Version**: Hardware model identifier and firmware OS release.
* **Credential**: Select attached SSH/SNMP credentials profile from the dropdown.
* **VLANs**: Click **`+`** to configure assigned VLAN IDs.
* **Note**: Additional operational comments.
* Click **ADD NETWORK DEVICE** to save.

---

### Network Device Details Dashboard

* Click on any network device in the table to inspect its comprehensive configuration:

<a href="../../images/inventory-network-device-details.png" class="glightbox">
  <img src="../../images/inventory-network-device-details.png" alt="Network Device Details Dashboard">
</a>

* **General Info**: Name, UUID, IP Address, and attached Credentials.
* **Device Details**: Type (`networkDevice`), Model Name, OS Version, and configured VLAN count.
* **Status & Note**: Status badge (`Inactive`/`Active`) and operator notes.
* **Infrastructure**: Parent Cloud, Region, and Zone placement with UUIDs.
* **Header Actions**: Quick controls to Edit, Attach Credentials, or Delete.

---

## Application Management

### Inspecting Applications

* Switch to the **APP** tab in the Zone dashboard:

<a href="../../images/inventory-zone-app-tab.png" class="glightbox">
  <img src="../../images/inventory-zone-app-tab.png" alt="Applications Inventory Tab">
</a>

* Displays registered application instances (e.g., `testApp`, UUID `cc22b1db`), descriptions, application types, hosting platforms, and attached labels.

---

### Adding a New Application

* Click the orange **`+`** (Add App) button on the table toolbar:

<a href="../../images/inventory-add-app-button.png" class="glightbox">
  <img src="../../images/inventory-add-app-button.png" alt="Add Application Button">
</a>

* Configure application parameters in the **Add a new app** dialog:

<a href="../../images/inventory-add-app-modal.png" class="glightbox">
  <img src="../../images/inventory-add-app-modal.png" alt="Add a New Application Dialog">
</a>

* **Name & Description**: Application identifier (e.g., `testApp`) and functional description.
* **Application Type**: Service category (e.g., `Web Server`, `Database`, `Microservice`).
* **Hosting Platform**: Underlying host platform (e.g., `Docker`, `Kubernetes`, `Virtual Machine`).
* **Credential**: Associated service authentication profile.
* **Labels & Data**: Custom metadata tags and key-value configuration attributes.
* Click **ADD APP** to register the application.

---

### Application Details Dashboard

* Click on any application name to open its details view:

<a href="../../images/inventory-app-details.png" class="glightbox">
  <img src="../../images/inventory-app-details.png" alt="Application Details Dashboard">
</a>

* **General Info & App Details**: Displays application name, system UUID, description, assigned credentials, hosting platform, and type.
* **Infrastructure Hierarchy**: Tracks parent Cloud, Region, and Zone placement.
* **Operational Statuses**: Indicators for live Patch Status and Automation Status.
* **Header Actions**: Quick controls for editing, attaching credentials, or deleting the application.

---

## Storage Types Management

### Inspecting Storage Types

* Switch to the **STORAGE** tab within the Zone dashboard:

<a href="../../images/inventory-zone-storage-tab.png" class="glightbox">
  <img src="../../images/inventory-zone-storage-tab.png" alt="Storage Types Inventory Tab">
</a>

* Displays storage pools, volume backends, capacity allocations (GB), mount paths, active statuses, and metadata labels.

---

### Adding a New Storage Type

* Click the orange **`+`** (Add Storage Type) button on the table toolbar:

<a href="../../images/inventory-add-storage-button.png" class="glightbox">
  <img src="../../images/inventory-add-storage-button.png" alt="Add Storage Type Button">
</a>

* Configure storage specifications in the **Add a new storage type** dialog:

<a href="../../images/inventory-add-storage-modal.png" class="glightbox">
  <img src="../../images/inventory-add-storage-modal.png" alt="Add a New Storage Type Dialog">
</a>

* **Name & Description**: Unique storage resource identifier (e.g., `testStorageType`).
* **Storage Type**: Select storage backend classification (`NFS`, `Ceph Block`, `iSCSI`, `NVMe`).
* **Capacity (GB)**: Storage volume allocation in gigabytes.
* **Mount Path**: Target filesystem mount point (e.g., `/mnt/data`).
* **Active Toggle**: Enable or disable active status monitoring.
* **Credential**: Select storage backend authentication profile.
* **Labels & Data**: Key-value metadata and driver configuration parameters.
* Click **ADD STORAGE TYPE** to register the storage resource.

---

### Storage Type Details Dashboard

* Click on any storage resource in the table to open its detailed view:

<a href="../../images/inventory-storage-details.png" class="glightbox">
  <img src="../../images/inventory-storage-details.png" alt="Storage Details Dashboard">
</a>

* **General Info & Storage Details**: Displays storage name, system UUID, backend type (`storageType`), allocated capacity, and mount path.
* **Infrastructure Placement**: Tracks assigned Cloud, Region, and Zone UUID mappings.
* **Header Actions**: Quick buttons to Edit storage parameters, Attach Credentials, or Delete the storage resource.
