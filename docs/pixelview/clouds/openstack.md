# OpenStack Cloud Management

The **OpenStack** module (`/openstack`) under **Clouds** provides centralized enterprise cloud orchestration, infrastructure lifecycle management, and full-stack observability for OpenStack environments within PixelView.

PixelView seamlessly integrates with core OpenStack services to deliver a unified operational interface:
- **Nova (Compute)**: Virtual machine provisioning, live state management, sizing flavors, SSH key pairs, and anti-affinity placement policies.
- **Glance (Image Service)**: OS image catalogs, public and private template lifecycle, and distribution management.
- **Cinder & Swift (Storage Services)**: Persistent block volume lifecycle, NVMe/Standard storage tiers, point-in-time volume snapshots, durable volume backups, and distributed Swift object storage containers.
- **Neutron (Software-Defined Networking)**: Multi-tenant virtual networks, subnets, routers, distributed firewall security groups, public floating elastic IPs, virtual network ports, and QoS bandwidth policies.
- **Octavia (Load Balancing)**: Enterprise layer 4/7 load balancers, listener pools, and active health monitoring.
- **Keystone (Identity & Governance)**: Multi-tenant project boundaries, user authentication, role-based access control (RBAC), and automation application credentials.

---

## Accessing OpenStack & Multi-Region Management

### Navigating to OpenStack
To access the OpenStack module:
* In the primary left navigation sidebar, expand **Clouds**.
* Click **OpenStack**:

<a href="../../images/openstack-menu-select.png" class="glightbox">
  <img src="../../images/openstack-menu-select.png" alt="OpenStack Sidebar Navigation Selection">
</a>

### Multi-Region Architecture & Switcher
PixelView natively supports multi-region OpenStack deployments from a single unified pane. When entering OpenStack, PixelView automatically detects and displays all available regions:

<a href="../../images/openstack-regions-overview.png" class="glightbox">
  <img src="../../images/openstack-regions-overview.png" alt="OpenStack Multi-Region Navigation Bar & Global Search">
</a>

| Region Control | Description |
| :--- | :--- |
| **Region Navigation Tabs** | Located in the top header bar (e.g., `GNOCCHI`, `regionOne`). Clicking any tab instantly switches the active operational region without re-authenticating. |
| **Scope Search Area** | Dropdown filter allowing operators to search specifically within `Instances`, `Volumes`, `Networks`, or `All Resources`. |
| **Search Field Selector** | Targeted query criteria selector (`ID`, `Name`, `IP Address`, or `Status`). |
| **Global Search Input** | Real-time full-text search with instant cross-region filtering. |
| **Secondary Region Sidebar** | Dedicated service navigation tree on the left containing **Utilization**, **Compute**, **Storage**, **Network**, and **Identity**. |

> [!NOTE]
> All subsequent actions, resource tables, and provisioning wizards are scoped to the currently selected region displayed in the top header bar.

---

## Compute Infrastructure (Nova)

The **Compute** submenu provides complete management over virtual machines, hardware profiles, image catalogs, SSH credentials, and server affinity placement.

### Instances (Virtual Machines)

#### Instances Table Overview
Navigate to **Compute** &rarr; **Instances** in the secondary sidebar:

<a href="../../images/openstack-instances-table.png" class="glightbox">
  <img src="../../images/openstack-instances-table.png" alt="OpenStack Compute Instances Table Overview">
</a>

The **Instances** table provides comprehensive details for every virtual machine deployed in the active region:

| Column | Description |
| :--- | :--- |
| **ID / Name** | Virtual machine display name, system UUID, and quick-copy button. Clicking the instance name navigates to detailed metrics, VNC console, and attached interfaces. |
| **Status** | Real-time lifecycle state badge: `ACTIVE` (running), `SHUTOFF` (powered off), `PAUSED`, `SUSPENDED`, `BUILDING`, or `ERROR`. |
| **Flavor** | Allocated hardware profile (e.g., `gp.5.4.8`, `gp.5.8.16`, `mo.6.2.16`) defining vCPU, memory, and root disk allocations. |
| **IP Address** | Assigned IP endpoints categorized by type: `Fixed` (internal tenant network IP) and `Floating` (publicly routable elastic IP). |
| **Created At** | Timestamp indicating initial provisioning time (`YYYY-MM-DD HH:mm:ss`). |
| **Actions (`...`)** | Contextual action menu providing operations such as **Start**, **Shutoff**, **Soft/Hard Reboot**, **Open Web Console**, **Attach Volume**, **Associate Floating IP**, and **Terminate**. |

#### Launching a New Instance
To provision a new virtual machine:

* Click the orange **`+`** (Launch Instance) button located in the top-right toolbar above the table:

<a href="../../images/openstack-instances-add-button.png" class="glightbox">
  <img src="../../images/openstack-instances-add-button.png" alt="Launch Instance Toolbar Button">
</a>

* The multi-step **Launch Instance** modal wizard opens:

<a href="../../images/openstack-instances-launch-modal.png" class="glightbox">
  <img src="../../images/openstack-instances-launch-modal.png" alt="Launch Instance Multi-Step Configuration Wizard">
</a>

#### Launch Wizard Step Reference

| Step | Parameter | Type | Description |
| :--- | :--- | :--- | :--- |
| **Details** | **Instance Name** | Required Text | Hostname for the virtual machine instance. |
| | **Description** | Optional Text | Operational notes, purpose, or ownership tags. |
| | **Availability Zone** | Dropdown | Target physical zone for compute placement (e.g., `nova`). |
| | **Count** | Number | Number of identical instances to spin up concurrently (default: `1`). |
| | **Tags** | Comma-delimited | Searchable organizational metadata labels. |
| **Source** | **Boot Source** | Dropdown | Select boot volume source: `Image`, `Volume`, or `Volume Snapshot`. |
| | **Delete Volume on Termination** | Toggle | Automatically purge root storage upon VM deletion to avoid orphan volumes. |
| **Flavor** | **Compute Flavor** | Selection | Select resource profile matching CPU, RAM, and storage requirements. |
| **Networks** | **NIC Attachment** | Selection | Bind virtual network interfaces (e.g., tenant private subnet). |
| **Security Groups** | **Firewall Profile** | Multi-select | Attach distributed security groups controlling ingress and egress traffic. |
| **Key Pair** | **SSH Public Key** | Dropdown | Select injected SSH key pair for passwordless administrative access. |
| **Server Groups** | **Affinity Policy** | Optional Selection | Assign to an anti-affinity group to guarantee high availability across hypervisors. |

---

### Images (Glance)

The **Images** catalog (`/openstack/:regionName/images`) provides centralized management of base operating system images, cloud-init templates, and custom system snapshots.

#### Images Table Overview
Navigate to **Compute** &rarr; **Images** in the secondary sidebar:

<a href="../../images/openstack-images-table.png" class="glightbox">
  <img src="../../images/openstack-images-table.png" alt="OpenStack Glance Image Catalog Table">
</a>

The table organizes images across scope tabs (**Public**, **Private**, **Community**, **Shared**):

| Column | Description |
| :--- | :--- |
| **ID / Name** | Operating system release name (e.g., `Windows Server 2022`, `flatcar-stable`) and system UUID. |
| **Status** | Image readiness state: `Active`, `Saving`, `Queued`, or `Killed`. |
| **Visibility** | Access scope: `Public` (available across all projects) or `Private` (restricted to current project). |
| **Format** | Disk container format (`RAW`, `QCOW2`, `ISO`, `VMDK`). |
| **Size** | Storage consumption of the image payload. |
| **Actions** | Context actions to launch instance directly, download image metadata, or delete obsolete builds. |

#### Creating / Uploading an Image
* Click the orange **`+`** (Create Image) button in the top-right toolbar:

<a href="../../images/openstack-images-add-button.png" class="glightbox">
  <img src="../../images/openstack-images-add-button.png" alt="Create Image Toolbar Button">
</a>

* The **Create Image** modal dialog will open:

<a href="../../images/openstack-images-create-modal.png" class="glightbox">
  <img src="../../images/openstack-images-create-modal.png" alt="Create Image Configuration Modal">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Image Name** | Required Text | Descriptive display name for the template. |
| **Image Description** | Optional Text | Build notes, OS patch level, and kernel version. |
| **Source Type** | Dropdown | `File` (direct binary upload) or `URL` (remote HTTP/S download). |
| **Format** | Dropdown | File type specification (`RAW`, `QCOW2`, `ISO`). |
| **Minimum Disk (GiB)** | Number | Minimum root disk capacity required by the guest OS. |
| **Minimum RAM (MB)** | Number | Minimum memory required to boot. |
| **Visibility** | Selection | Set to `Public`, `Private`, or `Shared`. |
| **Architecture** | Text | Target CPU architecture (`x86_64`, `aarch64`). |

---

### Flavors (Hardware Profiles)

Flavors define virtual hardware sizing specifications, setting vCPU count, memory allocation, and ephemeral disk space.

#### Flavors Table Overview
Navigate to **Compute** &rarr; **Flavors** in the secondary sidebar:

<a href="../../images/openstack-flavors-table.png" class="glightbox">
  <img src="../../images/openstack-flavors-table.png" alt="OpenStack Compute Flavors Catalog Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Unique flavor profile identifier (e.g., `gp.5.4.8`, `gp.5.8.16`, `mo.6.2.16`). |
| **VCPUs** | Allocated virtual processor cores. |
| **RAM** | Memory allocation displayed in Megabytes (MB). |
| **Root Disk** | Primary operating system disk capacity in Gigabytes (GiB). |
| **Ephemeral Disk** | Temporary scratch disk capacity automatically wiped upon termination. |
| **Swap** | Swap partition size in Megabytes. |
| **Public** | Indicates whether the flavor is visible to all projects or restricted. |

#### Creating a Custom Flavor
* Click the orange **`+`** (Create Flavor) button in the top-right toolbar:

<a href="../../images/openstack-flavors-add-button.png" class="glightbox">
  <img src="../../images/openstack-flavors-add-button.png" alt="Create Flavor Toolbar Button">
</a>

* The **Create Flavor** modal dialog will open:

<a href="../../images/openstack-flavors-create-modal.png" class="glightbox">
  <img src="../../images/openstack-flavors-create-modal.png" alt="Create Compute Flavor Modal Dialog">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Flavor Name** | Required Text | Name for the flavor (e.g., `c2.large`, `m1.xlarge`). |
| **VCPUs** | Required Number | Number of virtual cores assigned to the guest VM. |
| **RAM (MB)** | Required Number | Total memory allocated to the instance. |
| **Root Disk (GiB)** | Required Number | Size of the root filesystem disk. |
| **Ephemeral Disk (GiB)** | Optional Number | High-speed temporary scratch space. |
| **Swap Disk (MB)** | Optional Number | Swap space allocation. |
| **Is Public** | Checkbox | Enable to make this flavor available across all tenant accounts. |

---

### Key Pairs (SSH Credentials)

Key pairs provide cryptographic SSH public key injection into virtual machines at launch time, ensuring passwordless root/admin login without hardcoded secrets.

#### Key Pairs Table Overview
Navigate to **Compute** &rarr; **Key Pairs** in the secondary sidebar:

<a href="../../images/openstack-keypairs-table.png" class="glightbox">
  <img src="../../images/openstack-keypairs-table.png" alt="OpenStack SSH Key Pairs Management Table">
</a>

| Column | Description |
| :--- | :--- |
| **Name** | Display name for the SSH key pair. |
| **Fingerprint** | Cryptographic SHA256 or MD5 hash fingerprint verifying key authenticity. |
| **Type** | Key algorithm (`ssh-rsa`, `ssh-ed25519`). |
| **Actions** | Download public key or delete key pair. |

#### Creating / Importing a Key Pair
* Click the orange **`+`** (Create Keypair) button in the top-right toolbar:

<a href="../../images/openstack-keypairs-add-button.png" class="glightbox">
  <img src="../../images/openstack-keypairs-add-button.png" alt="Create Keypair Toolbar Button">
</a>

* The **Create Keypair** modal dialog will open:

<a href="../../images/openstack-keypairs-create-modal.png" class="glightbox">
  <img src="../../images/openstack-keypairs-create-modal.png" alt="Create / Import SSH Keypair Modal Dialog">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Keypair Name** | Required Text | Label identifying the keypair (e.g., `devops-admin-key`). |
| **Key Type** | Selection | `SSH Key` (Standard) or `X509 Certificate`. |
| **Public Key** | Textarea (Optional) | Paste an existing public key (`ssh-ed25519 AAA...`) to import. Leave blank to have OpenStack generate a new private key for immediate download. |

---

### Server Groups (Placement & Anti-Affinity)

Server Groups enforce hardware-level placement policies, ensuring redundant instances reside on distinct physical hypervisors for fault tolerance.

#### Server Groups Table Overview
Navigate to **Compute** &rarr; **Server Groups** in the secondary sidebar:

<a href="../../images/openstack-server-groups-table.png" class="glightbox">
  <img src="../../images/openstack-server-groups-table.png" alt="OpenStack Server Groups Placement Policies Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Server group name and UUID. |
| **Policy** | Placement rule: `anti-affinity`, `soft-anti-affinity`, `affinity`, or `soft-affinity`. |
| **Members** | List of instance UUIDs currently assigned to the server group. |
| **Actions** | Delete server group or modify policy rules. |

#### Creating a Server Group
* Click the orange **`+`** button in the top-right toolbar:

<a href="../../images/openstack-server-groups-add-button.png" class="glightbox">
  <img src="../../images/openstack-server-groups-add-button.png" alt="Create Server Group Toolbar Button">
</a>

* The **Create Server Group** modal dialog will open:

<a href="../../images/openstack-server-groups-create-modal.png" class="glightbox">
  <img src="../../images/openstack-server-groups-create-modal.png" alt="Create Server Group Modal Dialog">
</a>

| Policy Type | Behavior | Best Use Case |
| :--- | :--- | :--- |
| **Anti-Affinity** | Guarantees instances are scheduled on completely separate physical hypervisors. Scheduling fails if insufficient hosts exist. | High-availability database clusters, Kubernetes control-plane nodes. |
| **Soft Anti-Affinity** | Attempts to schedule instances across different physical hosts, but permits collocation if capacity is exhausted. | Web worker tiers, stateless microservices. |
| **Affinity** | Forces all member instances to reside on the same physical hypervisor. | Ultra-low latency inter-node communication. |

---

## Storage Services (Cinder & Swift)

PixelView provides full enterprise block storage and distributed object storage operations across the OpenStack region.

### Volumes (Block Storage)

Volumes are durable block storage devices that can be attached to running instances, formatted with filesystems, and preserved across VM lifecycles.

#### Volumes Table Overview
Navigate to **Storage** &rarr; **Volumes** in the secondary sidebar:

<a href="../../images/openstack-volumes-table.png" class="glightbox">
  <img src="../../images/openstack-volumes-table.png" alt="OpenStack Cinder Storage Volumes Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Volume label and system UUID. |
| **Status** | Lifecycle state: `available` (unattached), `in-use` (attached to a VM), `creating`, or `error`. |
| **Size** | Total disk allocation in Gigabytes (GiB). |
| **Type** | Backend storage tier (e.g., `standard-nvme`, `standard-ssd`, `ceph-rbd`). |
| **Attached To** | Shows target VM hostname and device mount point (e.g., `/dev/vdb`). |
| **Bootable** | Indicates if the volume contains a bootable OS image. |
| **Actions** | Contextual menu: **Attach/Detach Volume**, **Create Snapshot**, **Create Backup**, **Extend Volume**, or **Delete**. |

#### Creating a Volume
* Click the orange **`+`** (Create Volume) button in the top-right toolbar:

<a href="../../images/openstack-volumes-add-button.png" class="glightbox">
  <img src="../../images/openstack-volumes-add-button.png" alt="Create Volume Toolbar Button">
</a>

* The **Create Volume** modal dialog will open:

<a href="../../images/openstack-volumes-create-modal.png" class="glightbox">
  <img src="../../images/openstack-volumes-create-modal.png" alt="Create Storage Volume Modal Dialog">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Volume Name** | Required Text | Human-readable label for the volume. |
| **Description** | Optional Text | Storage purpose and mount instructions. |
| **Size (GiB)** | Required Number | Capacity to allocate. |
| **Volume Type** | Dropdown | Storage backend profile defining performance SLA (e.g., NVMe vs SSD). |
| **Source** | Dropdown | `Empty Volume` (blank raw device), `Image` (clone from OS image), or `Snapshot` (restore from snapshot). |

---

### Volume Snapshots

Snapshots capture instantaneous, point-in-time read-only copies of a Cinder volume, allowing instant rollbacks or rapid provisioning of identical volumes.

#### Snapshots Table Overview
Navigate to **Storage** &rarr; **Snapshots** in the secondary sidebar:

<a href="../../images/openstack-snapshots-table.png" class="glightbox">
  <img src="../../images/openstack-snapshots-table.png" alt="OpenStack Storage Volume Snapshots Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Snapshot display name and UUID. |
| **Source Volume** | Name and UUID of the parent block volume. |
| **Size** | Capacity preserved from the source volume (GiB). |
| **Status** | Readiness state: `available`, `creating`, or `error`. |
| **Created At** | Timestamp recording snapshot creation. |
| **Actions** | Shortcuts to **Create Volume from Snapshot**, **Reset State**, or **Delete**. |

---

### Volume Backups

Volume backups provide durable, off-site secondary storage for entire block volumes. Backups are stored independently in secondary object storage clusters, protecting workloads against catastrophic storage pool outages.

#### Backups Table Overview
Navigate to **Storage** &rarr; **Backups** in the secondary sidebar:

<a href="../../images/openstack-backups-table.png" class="glightbox">
  <img src="../../images/openstack-backups-table.png" alt="OpenStack Volume Backups Table Overview">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Backup identifier and timestamped catalog label. |
| **Size** | Data size preserved in object storage. |
| **Status** | Backup lifecycle state: `Available`, `Restoring`, or `Error`. |
| **Source Volume** | Originating volume UUID. |
| **Container** | Target object storage container holding the backup chunks. |
| **Actions** | Restore backup to a new or existing volume, or purge backup record. |

---

### Object Storage (Swift Containers)

The **Object Storage** catalog (`/openstack/:regionName/object-storage`) allows operators to manage highly available, distributed OpenStack Swift object storage containers and multi-gigabyte blob storage.

#### Object Storage Table Overview
Navigate to **Storage** &rarr; **Object Storage** in the secondary sidebar:

<a href="../../images/openstack-object-storage-table.png" class="glightbox">
  <img src="../../images/openstack-object-storage-table.png" alt="OpenStack Swift Object Storage Table Overview">
</a>

| Column | Description |
| :--- | :--- |
| **Container** | Swift container name and quick-access prefix link. |
| **Objects** | Total number of individual data objects, blobs, and file chunks stored within the container. |
| **Bytes Used** | Aggregated storage consumption (e.g., `20 MiB`, `4.2 GiB`, `5.1 GiB`). |
| **Created At** | Timestamp indicating the container creation date and time. |
| **Actions** | Contextual options menu allowing operators to upload files, configure container ACLs, or delete empty containers. |

#### Creating a Container
To create a new Swift storage container:
* Click the orange **`+`** button located in the top-right toolbar:

<a href="../../images/openstack-object-storage-add-button.png" class="glightbox">
  <img src="../../images/openstack-object-storage-add-button.png" alt="Create Swift Container Toolbar Button">
</a>

* The **Create Container** modal dialog will open:

<a href="../../images/openstack-object-storage-create-modal.png" class="glightbox">
  <img src="../../images/openstack-object-storage-create-modal.png" alt="Create Swift Container Modal Dialog">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Container Name** | Required Text | A globally unique, alphanumeric identifier for the object storage container. |
| **Metadata (Optional)** | Key-Value Pairs | Custom user-defined key-value attributes attached to the container headers for categorization, lifecycle policy tagging, or application integration. Click **`+ ADD`** to specify additional metadata entries. |

---

## Software-Defined Networking (Neutron & Octavia)

The **Network** submenu provides comprehensive control over software-defined networks, visual topologies, interfaces, routing, firewalls, elastic IPs, bandwidth QoS policies, and load balancers.

### Networks & Subnets

#### Networks Table Overview
Navigate to **Network** &rarr; **Networks** in the secondary sidebar:

<a href="../../images/openstack-networks-table.png" class="glightbox">
  <img src="../../images/openstack-networks-table.png" alt="OpenStack Neutron Networks Table Overview">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Network label (e.g., `PUBLICNET`, internal tenant networks) and UUID. |
| **Subnets Associated** | List of CIDR subnets bound to the network with DHCP pool ranges. |
| **Shared** | Flag indicating if other tenant projects can attach interfaces to this network. |
| **External Network** | Indicates whether this network routes directly to the upstream internet/WAN. |
| **Status** | Network state: `ACTIVE` or `DOWN`. |
| **Admin State** | Administrative status: `UP` or `DOWN`. |
| **Actions** | Context menu shortcuts to **Edit Network**, **Add Subnet**, or **Delete**. |

#### Creating a Network & Subnet
* Click the orange **`+`** (Create Network) button in the top-right toolbar:

<a href="../../images/openstack-networks-add-button.png" class="glightbox">
  <img src="../../images/openstack-networks-add-button.png" alt="Create Network Toolbar Button">
</a>

* The **Create Network** modal dialog will open:

<a href="../../images/openstack-networks-create-modal.png" class="glightbox">
  <img src="../../images/openstack-networks-create-modal.png" alt="Create Network and Subnet Modal Wizard">
</a>

#### Network and Subnet Parameter Reference

| Step | Parameter | Description |
| :--- | :--- | :--- |
| **Network** | **Network Name** | Identifier for the L2 broadcast domain. |
| | **Enable Admin State** | Flag setting administrative operational state to UP. |
| | **Shared** | Allow other tenant projects to attach virtual interfaces. |
| | **Create Subnet** | Checkbox to immediately configure an IP subnet. |
| **Subnet** | **Subnet Name** | Descriptive label for the IP range. |
| | **Network Address** | CIDR notation subnet (e.g., `192.168.10.0/24`, `10.0.0.0/16`). |
| | **IP Version** | `IPv4` or `IPv6`. |
| | **Gateway IP** | Default gateway address for the subnet (e.g., `192.168.10.1`). |
| **Subnet Details** | **Enable DHCP** | Automatically run Neutron dnsmasq DHCP agents on the subnet. |
| | **Allocation Pools** | Explicit IP ranges reserved for dynamic lease assignment. |
| | **DNS Name Servers** | Upstream DNS resolver IPs (e.g., `1.1.1.1`, `8.8.8.8`). |

---

### Interactive Visual Network Topology

The **Topology** view (`/openstack/:regionName/topology`) delivers a dynamic, interactive visual map representing the complete multi-tenant network fabric in real time.

Navigate to **Network** &rarr; **Topology** in the secondary sidebar:

<a href="../../images/openstack-topology-overview.png" class="glightbox">
  <img src="../../images/openstack-topology-overview.png" alt="OpenStack Interactive Real-Time Network Topology Map">
</a>

#### Topology Visual Elements
- **External Networks**: Rendered as orange cloud nodes representing WAN gateway uplinks.
- **Virtual Routers**: Central routing icons depicting gateway forwarding points.
- **Tenant Networks**: Dedicated color-coded horizontal conduits showing isolated L2 segments.
- **Compute Instances**: Virtual machine nodes connected to their respective network wires with real-time status badges.
- **Interactive Canvas Controls**:
  - **Zoom In / Zoom Out**: Pan and inspect complex topologies with hundreds of endpoints.
  - **Fit-to-Screen**: Center and scale the entire regional topology in one click.
  - **Element Selection**: Clicking any instance, router, or network node displays an instant side drawer showing live IP bindings and traffic stats.

---

### Network Ports (Interfaces & Virtual Bindings)

The **Ports** inventory (`/openstack/:regionName/ports`) provides granular visibility and operational control over every virtual network interface (VIF) created across tenant networks, routers, compute instances, and Octavia load balancers.

#### Ports Table Overview
Navigate to **Network** &rarr; **Ports** in the secondary sidebar:

<a href="../../images/openstack-ports-table.png" class="glightbox">
  <img src="../../images/openstack-ports-table.png" alt="OpenStack Neutron Network Ports Inventory Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Port identifier, optional display name, and system UUID. |
| **Project** | Owning project boundary UUID. |
| **Bind Resource** | The attached OpenStack entity consuming the interface (e.g., `compute:az1` for VMs, `Octavia` for load balancer amphorae, or `network:router_interface` for router gateways). |
| **Owned Network** | The underlying Neutron network UUID hosting the subnet. |
| **Fixed IPs** | The assigned private IP address bound to the interface (e.g., `10.0.0.14`, `192.168.1.117`). |
| **Mac Address** | Unique virtual hardware MAC address assigned by Neutron (e.g., `fa:16:3e:26:54:82`). |
| **Status** | Real-time port link status: `ACTIVE` (connected and passing traffic) or `DOWN` (unbound or idle). |
| **Actions** | Context menu shortcuts to update port attributes, associate security groups, or delete interfaces. |

---

### Virtual Routers

Virtual routers provide Layer 3 packet forwarding between private tenant subnets and upstream external networks, enabling SNAT/DNAT internet routing.

#### Routers Table Overview
Navigate to **Network** &rarr; **Routers** in the secondary sidebar:

<a href="../../images/openstack-routers-table.png" class="glightbox">
  <img src="../../images/openstack-routers-table.png" alt="OpenStack Virtual Routers Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Router name and UUID. |
| **Status** | Operational state: `ACTIVE` or `DOWN`. |
| **External Network** | Upstream external gateway network providing WAN connectivity. |
| **SNAT** | Indicates if Source Network Address Translation is enabled. |
| **Admin State** | Administrative state (`UP`/`DOWN`). |
| **Actions** | Shortcuts to **Add Interface**, **Manage Static Routes**, or **Delete Router**. |

#### Creating a Router
* Click the orange **`+`** (Create Router) button in the top-right toolbar:

<a href="../../images/openstack-routers-add-button.png" class="glightbox">
  <img src="../../images/openstack-routers-add-button.png" alt="Create Router Toolbar Button">
</a>

* The **Create Router** modal dialog will open:

<a href="../../images/openstack-routers-create-modal.png" class="glightbox">
  <img src="../../images/openstack-routers-create-modal.png" alt="Create Virtual Router Modal Dialog">
</a>

| Parameter | Description |
| :--- | :--- |
| **Router Name** | Display identifier for the L3 gateway router. |
| **Admin State** | Initial operational status (`UP`/`DOWN`). |
| **External Network** | Select the upstream external gateway network (e.g., `PUBLICNET`) to enable SNAT for outbound traffic. |
| **Enable SNAT** | Checkbox to automatically translate private tenant IPs to the router's external gateway IP. |

---

### Security Groups (Distributed Firewalls)

Security groups act as virtual distributed firewalls applied at the port level to govern ingress and egress traffic for compute instances.

#### Security Groups Table Overview
Navigate to **Network** &rarr; **Security Groups** in the secondary sidebar:

<a href="../../images/openstack-security-groups-table.png" class="glightbox">
  <img src="../../images/openstack-security-groups-table.png" alt="OpenStack Security Groups Firewall Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Security group display name (e.g., `default`, `k8s-cluster-sg`) and UUID. |
| **Description** | Operational notes describing the rule baseline and permitted traffic. |
| **Rules Count** | Total number of active ingress and egress filtering rules configured. |
| **Actions** | Context shortcuts to **Manage Rules**, **Edit**, or **Delete**. |

#### Creating a Security Group
* Click the orange **`+`** button in the top-right toolbar:

<a href="../../images/openstack-security-groups-add-button.png" class="glightbox">
  <img src="../../images/openstack-security-groups-add-button.png" alt="Create Security Group Toolbar Button">
</a>

* The **Create Security Group** modal dialog will open:

<a href="../../images/openstack-security-groups-create-modal.png" class="glightbox">
  <img src="../../images/openstack-security-groups-create-modal.png" alt="Create Security Group Modal Dialog">
</a>

#### Security Group Rule Parameters Reference
When managing rules within a security group, each rule requires the following attributes:

| Parameter | Options | Description |
| :--- | :--- | :--- |
| **Direction** | `Ingress`, `Egress` | Specifies inbound or outbound packet filtering. |
| **Ethertype** | `IPv4`, `IPv6` | Network protocol family. |
| **Protocol** | `TCP`, `UDP`, `ICMP`, `Any` | Transport layer protocol filter. |
| **Port Range** | Single Port or Range | Target destination port(s) (e.g., `22` for SSH, `80/443` for Web, `6443` for Kubernetes API). |
| **Remote** | `CIDR` or `Security Group` | Permitted traffic source. Restrict to specific IP blocks (e.g., `10.0.0.0/16`) or another security group. |

---

### Floating IPs (Elastic Public IPs)

Floating IPs are static, publicly routable IP addresses allocated from an external pool that can be dynamically bound to any internal instance port to allow internet connectivity.

#### Floating IPs Table Overview
Navigate to **Network** &rarr; **Floating IPs** in the secondary sidebar:

<a href="../../images/openstack-floating-ips-table.png" class="glightbox">
  <img src="../../images/openstack-floating-ips-table.png" alt="OpenStack Floating IP Addresses Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Public IP address (e.g., `50.56.159.194`, `50.56.158.139`) and UUID. |
| **Project** | Owning project boundary. |
| **Description** | Operational annotation (e.g., `Floating IP for Kubernetes external service`). |
| **Status** | Allocation state: `ACTIVE` (mapped to a live port) or `DOWN` (unassociated pool address). |
| **Created At** | Timestamp indicating allocation date and time. |
| **Actions** | Shortcuts to **Associate** to a VM interface, **Disassociate**, or **Release** back to the pool. |

#### Allocating a Floating IP
* Click the **Link / Associate** icon button in the top-right toolbar:

<a href="../../images/openstack-floating-ips-add-button.png" class="glightbox">
  <img src="../../images/openstack-floating-ips-add-button.png" alt="Allocate Floating IP Toolbar Button">
</a>

* The **Allocate Floating IP** modal dialog will open:

<a href="../../images/openstack-floating-ips-create-modal.png" class="glightbox">
  <img src="../../images/openstack-floating-ips-create-modal.png" alt="Allocate Floating IP Modal Dialog">
</a>

| Field | Description |
| :--- | :--- |
| **Pool** | Dropdown selecting the upstream public external network provider (e.g., `PUBLICNET`). |
| **Floating IP Name** | Optional DNS or descriptive alias for the IP endpoint. |
| **Description** | Workload reference notes. |

> [!TIP]
> Once allocated, click the **Actions** menu on the floating IP row and select **Associate** to map the public IP directly to an active compute instance's virtual interface.

---

### Network QoS Policies (Traffic Shaping & Bandwidth Limits)

The **Network QoS** module (`/openstack/:regionName/network-qos`) provides quality-of-service governance, allowing administrators to define bandwidth enforcement rules, egress rate limits, and DSCP traffic marking across tenant ports and networks.

#### Network QoS Table Overview
Navigate to **Network** &rarr; **Network QoS** in the secondary sidebar:

<a href="../../images/openstack-network-qos-table.png" class="glightbox">
  <img src="../../images/openstack-network-qos-table.png" alt="OpenStack Network QoS Policies Table Overview">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | QoS policy name and system UUID. |
| **Description** | Operational documentation detailing rate limit parameters and target workloads. |
| **Shared** | Boolean indicator showing whether the QoS policy is shareable across multiple tenant projects. |
| **Actions** | Options menu to edit policy rules, attach bandwidth limit rules, or delete the policy. |

#### Creating a Network QoS Policy
To create a new Quality of Service policy:
* Click the orange **`+`** button in the top-right toolbar:

<a href="../../images/openstack-network-qos-add-button.png" class="glightbox">
  <img src="../../images/openstack-network-qos-add-button.png" alt="Create Network QoS Policy Toolbar Button">
</a>

* The **Create Network QoS Policy** modal dialog will open:

<a href="../../images/openstack-network-qos-create-modal.png" class="glightbox">
  <img src="../../images/openstack-network-qos-create-modal.png" alt="Create Network QoS Policy Modal Dialog">
</a>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| **Policy Name** | Required Text | A descriptive, unique name identifying the policy (e.g., `high-priority-egress`, `limit-100mbps`). |
| **Description** | Optional Text | Contextual notes detailing bandwidth constraints, DSCP markings, or burst rates. |
| **Shared Policy** | Toggle Switch | When enabled, permits this QoS policy to be bound to networks and ports belonging to other tenant projects within the region. |

---

### Load Balancers (Octavia)

PixelView provides full enterprise lifecycle management for **Octavia** load balancers, distributing application traffic across redundant backend compute instances.

#### Load Balancers Table Overview
Navigate to **Network** &rarr; **Load Balancers** in the secondary sidebar:

<a href="../../images/openstack-loadbalancers-table.png" class="glightbox">
  <img src="../../images/openstack-loadbalancers-table.png" alt="OpenStack Octavia Load Balancers Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Load balancer name and system UUID. |
| **Operating Status** | Real-time health status: `ONLINE`, `OFFLINE`, or `DEGRADED`. |
| **Provisioning Status** | Octavia orchestration state: `ACTIVE`, `PENDING_CREATE`, or `PENDING_UPDATE`. |
| **VIP Address** | Virtual IP endpoint exposed to clients. |
| **Provider** | Underlying driver (`amphora` or `ovn`). |
| **Actions** | Shortcuts to **Manage Listeners**, **Configure Pools & Members**, **Attach Health Monitors**, or **Delete**. |

#### Creating a Load Balancer
* Click the orange **`+`** button in the top-right toolbar:

<a href="../../images/openstack-loadbalancers-add-button.png" class="glightbox">
  <img src="../../images/openstack-loadbalancers-add-button.png" alt="Create Load Balancer Toolbar Button">
</a>

* The **Create Load Balancer** modal dialog will open:

<a href="../../images/openstack-loadbalancers-create-modal.png" class="glightbox">
  <img src="../../images/openstack-loadbalancers-create-modal.png" alt="Create Load Balancer Modal Dialog">
</a>

| Configuration Section | Parameters | Description |
| :--- | :--- | :--- |
| **Load Balancer Details** | Name, Description, Admin State | Display naming and operational state. |
| **Virtual IP (VIP)** | VIP Subnet, IP Address | Choose the tenant subnet to allocate the VIP address from. Optionally specify a fixed IP. |
| **Listener Configuration** | Protocol, Port, Connection Limit | Define frontend listening parameters (`HTTP/80`, `HTTPS/443`, `TCP/6443`). |
| **Backend Pool** | Algorithm, Members | Select load distribution algorithm (`ROUND_ROBIN`, `LEAST_CONNECTIONS`, `SOURCE_IP`) and add member instance IPs with weights. |
| **Health Monitor** | Type, Interval, Timeout, Max Retries | Configure automated health checks (`HTTP`, `TCP`, `PING`) to remove unhealthy instances automatically. |

---

## Identity & Governance (Keystone)

The **Identity** submenu manages Keystone multi-tenancy, project boundaries, user directory records, access groups, and automation credentials.

### Projects (Tenants)

#### Projects Table Overview
Navigate to **Identity** &rarr; **Projects** in the secondary sidebar:

<a href="../../images/openstack-projects-table.png" class="glightbox">
  <img src="../../images/openstack-projects-table.png" alt="OpenStack Keystone Projects Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Project display label (e.g., `admin`, `service`, `production-app`) and unique UUID. |
| **Domain** | Keystone authentication domain (default: `Default`). |
| **Description** | Business unit, environment, or billing cost-center notes. |
| **Enabled** | Status badge indicating whether project workloads are authorized. |
| **Actions** | Context shortcuts to manage project quotas, assign user memberships, or delete. |

---

### Users

#### Users Table Overview
Navigate to **Identity** &rarr; **Users** in the secondary sidebar:

<a href="../../images/openstack-users-table.png" class="glightbox">
  <img src="../../images/openstack-users-table.png" alt="OpenStack Keystone Users Management Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | User login handle, username, and UUID. |
| **Email** | User contact email address. |
| **Domain** | Keystone domain holding user credentials. |
| **Primary Project** | Default tenant scope assigned upon authentication. |
| **Enabled** | Account status (`true` / `false`). |
| **Actions** | Reset user password, modify role bindings, or disable login. |

---

### Groups

#### Groups Table Overview
Navigate to **Identity** &rarr; **Groups** in the secondary sidebar:

<a href="../../images/openstack-groups-table.png" class="glightbox">
  <img src="../../images/openstack-groups-table.png" alt="OpenStack Keystone Groups Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Group name (e.g., `administrators`, `devops`, `auditors`) and UUID. |
| **Description** | Functional purpose and security scope. |
| **Domain** | Owning Keystone domain. |
| **Actions** | Add/remove users from group, assign group-level project roles, or delete. |

---

### Application Credentials

Application credentials allow automated systems, CI/CD pipelines, and SDK scripts to authenticate against OpenStack APIs without exposing user passwords.

#### Application Credentials Table Overview
Navigate to **Identity** &rarr; **Application Credentials** in the secondary sidebar:

<a href="../../images/openstack-app-credentials-table.png" class="glightbox">
  <img src="../../images/openstack-app-credentials-table.png" alt="OpenStack Application Credentials Table">
</a>

| Column | Description |
| :--- | :--- |
| **ID / Name** | Credential label (e.g., `terraform-provisioner`, `k8s-cloud-provider`) and UUID. |
| **Project** | Scoped project boundary for API authorization. |
| **Description** | System purpose and expiration tracking. |
| **Expiration** | Expiry date/time or `Unrestricted`. |
| **Roles** | Whitelist of RBAC permissions granted to the API token (`member`, `reader`). |
| **Actions** | Download `clouds.yaml` configuration or revoke credential token. |

---

## Operational Best Practices & Troubleshooting

### Common Operational Patterns
* **Cross-Zone High Availability**: When spinning up redundant database or application clusters, always assign instances to an **Anti-Affinity Server Group** to ensure physical hardware separation.
* **Floating IP Lifecycle**: Disassociate and release unused floating IPs back to public pools promptly to prevent IPv4 exhaustion and avoid unnecessary billing costs.
* **Storage Sizing**: Use high-performance NVMe volume types for I/O-intensive workloads (e.g., PostgreSQL, Elasticsearch) and standard volumes for general storage.
* **Monitor Network Topology**: Periodically inspect the [Interactive Network Topology](#interactive-visual-network-topology) to identify disconnected ports, orphaned floating IPs, and router bottlenecks.

### Troubleshooting Common States

| State / Symptom | Probable Cause | Recommended Resolution |
| :--- | :--- | :--- |
| **Instance in `ERROR` State** | Hypervisor out of compute capacity, flavor sizing exceeds quota, or host scheduling failure. | Review instance console logs via **Actions** &rarr; **Open Web Console**. Verify quota limits under regional settings. |
| **Volume in `error_extending`** | Backend Cinder Ceph pool out of physical storage capacity. | Check storage backend pool health. Ensure target storage volume size is supported. |
| **Cannot Reach Instance via Floating IP** | Security Group lacks inbound rule, or instance guest OS firewall is active. | Verify security group has an `Ingress` rule permitting TCP port `22` (SSH) or `3389` (RDP) from client CIDR. Verify instance default gateway routes through router interface. |
| **Load Balancer `OFFLINE`** | Backend pool members failed health monitor probes or listener port misconfigured. | Inspect **Health Monitor** timeout and interval settings. Verify member compute instances are answering HTTP/TCP checks on target port. |
