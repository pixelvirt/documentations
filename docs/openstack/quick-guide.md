**This documentation is only for the first login of a project admin.**After the initial configuration, you can use these steps as a reference for creating additional private networks, routers, and enabling internet access for your VMs within the OpenStack environment.

**Prerequisites:**

- Access to OpenStack Horizon dashboard with project admin credentials.

**Steps:**

**1. Create a Private Network:**

1. Navigate to "Project" -> "Network" -> "Networks".
2. Click "Create Network" to initiate the creation of a new internal network for your VMs.
3. Enter a descriptive name for your network (e.g., "internal-net1").
4. Set "Admin State" to "Up" to ensure the network is active.
5. Choose "No" for "Shared" unless you intend to share this network with other projects within your OpenStack environment.
6. (Optional) Customize network quotas (e.g., number of subnets, IP addresses) if your cloud provider allows it.
    - Network quotas might not be directly configurable within Horizon; consult your cloud provider's documentation for details.
7. Click "Create Network" to create the internal network.

**2. Create a Subnet for the Private Network:**

1. **Within the "Networks" list,** locate the newly created network (e.g., "internal-net1"). Click its name to access details.
2. In the network details view, click the "Create Subnet" button.
3. Enter a name for the subnet (e.g., "internal-subnet1").
4. Specify the IP address block for the subnet using CIDR notation (e.g., 10.0.1.0/24). Choose a block that fits your project's IP address needs and avoids conflicts with existing networks.
5. Leave the "Gateway IP" field blank for now. We'll configure this later when enabling internet access.
6. (Optional) Define allocation pools if you need to reserve specific IP addresses within the subnet for designated purposes.
7. Click "Create Subnet" to create the subnet within your internal network.

**3. Create a Router:**

1. Navigate back to "Project" -> "Network" -> "Routers".
2. Click "Create Router" to initiate the creation of a new router.
3. Enter a descriptive name for your router (e.g., "my-router").
4. Set "Admin State" to "Up" to activate the router.
5. In the "External Network" dropdown menu, **select the existing external network that provides internet access** within your OpenStack environment (assuming you have one). This network typically connects to an external gateway and the internet.
6. Click "Create Router" to create the router.

**4. Add Interface to Router (Connecting Private Network):**

1. **Navigate back to the "Routers" list.**
2. Click the name of the router you just created (e.g., "my-router").
3. Locate the "Interfaces" section within the router details view.
4. Click the "Add Interface" button.
5. In the "Interface Type" dropdown menu, choose "Subnet".
6. In the "Network" dropdown menu, select the internal network you created earlier (e.g., "internal-net1").
7. Click "Add Interface" to connect the internal network to the router. Essentially, the router will now act as a gateway for VMs within the internal network to reach the internet.

**Verification**

- Once you've completed these steps, launch VMs and assign them to the created subnet.
- You can use tools like `ping` or web browsing within the VMs to verify connectivity:
**Additional Notes:**

- Each private network can be attached to only one router, maintaining clear network segmentation and management.