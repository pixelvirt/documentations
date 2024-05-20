**1. Creating a  Internal Network**

1. Navigate to "Project" -> "Network" -> "Networks".
2. Click "Create Network" twice to create two separate internal networks.
3. For each network:
    - Enter a descriptive name (e.g., "internal-net1", "internal-net2").
    - Set "Admin State" to "Up".
    - Choose "No" for "Shared" (unless project-wide sharing is required).
    - **Check the "Create subnet" checkbox.**
    - Configure subnet details for each network:
        - Enter a name for the subnet (e.g., "internal-subnet1", "internal-subnet2").
        - Specify the IP address block using CIDR notation (e.g., 10.0.1.0/24 for the first network, 10.0.2.0/24 for the second).
        - (Optional) Define allocation pools if needed.
    - Click "Create Network" to create each network with its subnet.

**2. Create a Router:**

1. Navigate to "Project" -> "Network" -> "Routers".
2. Click "Create Router".
3. Enter a name for the router (e.g., "my-router").
4. Set "Admin State" to "Up".
5. Select the existing external network from the "External Network" dropdown menu (assuming you have one).
6. Click "Create Router" to create the router.

**3. Add Internal Networks to the Router:**

1. Navigate back to the "Routers" list.
2. Click on the name of the router you just created (e.g., "my-router").
3. In the router details view, locate the "Interfaces" section.
4. Click the "Add Interface" button twice (once for each internal network).
5. For each interface:
    - Choose "Subnet" as the interface type.
    - Select the corresponding internal network from the "Network" dropdown (e.g., "internal-net1" for the first interface, "internal-net2" for the second).
    - Click "Add Interface" to connect each internal network to the router.

**4. Obtain and Associate Floating IPs:**
- Provides a public IP address for your instance, allowing external resources to access it on the internet.
- Offers a more flexible and manageable way to expose instances publicly compared to assigning a static public IP directly to the instance.

1. **Obtain Floating IPs:** You'll need to acquire floating IP addresses from your OpenStack environment. This might involve requesting them from the administrator or using a self-service mechanism if available.
2. **Associate Floating IPs with Instances:**
    - Navigate to the instance details within OpenStack Horizon.
    - Look for an option to "Associate Floating IP" or similar wording.
    - Select the obtained floating IP(s) from the available options and associate them with your instances within the internal networks.

**5. (Optional) Verify Connectivity:**

- Once everything is configured, launch instances within the internal networks.
- You can use tools like `ping` or web browsing within the instances to verify connectivity:
    - Internal network communication should work seamlessly