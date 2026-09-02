# Host Groups Management

The **Host Groups** section (`/host-groups`) under **Inventory** enables operators to logically group servers and compute instances across clouds and regions. Host groups serve as the targeting mechanism for batch patch execution in **Planner Management** and automated playbook runs.

---

## Navigating to Host Groups

To access stored host groups:

* In the left navigation sidebar under **Inventory**, click **Host Groups**:

<a href="../../images/inventory-hostgroups-table.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-table.png" alt="Host Groups Overview Table">
</a>

### Host Groups Table Overview

The main table lists all configured host groups with 60-second background polling for synchronized state tracking:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Group display name (e.g., `ha-host-gtoup`) with a purple group icon, a link to the detail view, and a copyable UUID chip. |
| **Host Count** | Total number of servers currently assigned to the group. |
| **Created At** | Timestamp marking when the host group was created (`YYYY-MM-DD HH:mm:ss`). |
| **Updated At** | Timestamp tracking the most recent modification. |
| **Actions** | Context action menu (`...`) for editing or deleting host groups. |

### Table Toolbar Controls

* **Search / Global Filter**: Perform instant full-text searches across all host groups.
* **Column Filters**: Filter records by specific field attributes.
* **Show/Hide Columns**: Toggle table column visibility.
* **Density Toggle**: Adjust row spacing between standard and dense layouts.
* **Refresh**: Manually re-fetch the latest host group listings.
* **Add Host Group (`+`)**: Launch the host group creation modal.

---

## Creating a Host Group

To group servers for coordinated operations:

* Click the orange **`+`** (Add Host Group) button on the table toolbar:

<a href="../../images/inventory-hostgroups-add-button.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-add-button.png" alt="Add Host Group Toolbar Button">
</a>

* The **Create Host Group** dialog will appear:

<a href="../../images/inventory-hostgroups-create-modal.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-create-modal.png" alt="Create Host Group Dialog">
</a>

### Configuration Fields

* **Name** *(Required)*: Unique display name identifying the host group (e.g., `ha-host-gtoup`, `production-k8s-nodes`).
* **Description** *(Optional)*: Operational notes describing the role or maintenance schedule of this cluster.
* **Server Source**: Choose the inventory origin:
    * **Inventory Servers**: Target servers cataloged within PixelView's Cloud Inventory.
    * **Cloud Servers**: Query live instances directly from integrated cloud providers (such as OpenStack).
* **Cloud & Region Filters**: Select target Cloud and Region to load the member servers list.
* **Server Selection Table**:
    * Interactive multi-select table displaying `Server Name`, `IP Address`, `Cloud`, and `Region`.
    * Checkbox selectors to include individual servers or select all in bulk.
    * Real-time selection counter (`X of Y row(s) selected`) and **CLEAR SELECTION** control.
* Click **CREATE HOST GROUP** to save the cluster.

---

## Inspecting Host Groups & Server Members

* Locate the target host group row in the table (e.g., `ha-host-gtoup`):

<a href="../../images/inventory-hostgroups-row-select.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-row-select.png" alt="Selecting Host Group Row">
</a>

* Click on the host group name to open the **Host Group Details** dashboard:

<a href="../../images/inventory-hostgroups-details.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-details.png" alt="Host Group Details Dashboard">
</a>

### Host Group Details Overview

* **Host Count Ribbon**: High-level badge showing the total number of assigned instances (`3 hosts`).
* **General Info Card**: Displays the Group Name, unique UUID (with quick-copy button), and Description.
* **Location Card**: Identifies the primary Region (`ha-hosts-region-1`) and Cloud (`ha-host-inventory`) containing the hosts.
* **Timestamps Card**: Records creation and last modification timestamps.
* **Hosts Membership Table**:
    * **Server Name**: Lists all assigned host nodes (`ha-node-1`, `ha-node-2`, `ha-node-3`).
    * **IP Address**: Displays management network addresses (`119.9.94.19`, `119.9.94.22`, `119.9.94.30`).
* **Header Actions**:
    * **Edit (Pencil)**: Modify group metadata or adjust server membership.
    * **Delete (Trash)**: Remove the host group.

---

## Editing Host Groups

To adjust member servers or modify group details:

* Click the **Edit** (Pencil) button on the details page, or select **Edit** from the table row context menu (`...`).
* The **Edit Host Group** modal will open:

<a href="../../images/inventory-hostgroups-edit-modal.png" class="glightbox">
  <img src="../../images/inventory-hostgroups-edit-modal.png" alt="Edit Host Group Dialog">
</a>

* Update the group **Name** or **Description**.
* In the **Select Servers** table, check or uncheck individual servers to add or remove them from the group.
* Use search, column filtering, or fullscreen expansion for managing large fleets of servers.
* Click **UPDATE HOST GROUP** to persist changes.

---

## Deleting Host Groups

* Click the **Delete** (Trash) icon on the Host Group Details page, or choose **Delete Host Group** from the table actions menu (`...`).
* Confirm the confirmation prompt to remove the group definition.

> [!NOTE]
> Deleting a host group only removes the grouping reference; the underlying compute servers remain unaffected in Cloud Inventory.
