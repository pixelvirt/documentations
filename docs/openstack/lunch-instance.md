1. **Log in to Horizon:**
    
    - Navigate to your OpenStack Horizon login page and enter your username and password.
2. **Access the Compute Section:**
    
    - Once logged in, locate the "Project" tab on the Horizon dashboard.
    - Under the "Project" tab, click on "Compute" and then select "Instances".
3. **Launch Instance:**
    
    - On the "Instances" page, click the "Launch Instance" button located in the top right corner.
4. **Configure Instance Details:**
    
    - **Details Tab:**
        
        - **Instance Name:** Enter a descriptive name for your virtual machine instance.
        - **Description:** (Optional) Provide a brief description of the instance's purpose.
        - **Availability Zone:** (Optional) Choose a specific availability zone if desired (default is usually fine for beginners).
        - **Count:** Specify the number of identical instances you want to launch (default is 1).
    - **Sources Tab:**
        
        - **Boot Source:** Select "Image" as the boot source.
        - **Image:** Choose the desired operating system image for your instance from the available options.
        - **Create New Volume:** Select "Yes" or "No" depending on if you want to create a new volume for the instance's storage.
5. **Flavor Selection:**
    
    - In the "Flavor" tab, choose the appropriate instance size (flavor) that aligns with your resource requirements. Flavor options typically define CPU cores, RAM, and storage capacity.
6. **Network Selection:**
    
    - In the "Networks" tab, select the network(s) to which your instance should connect. This allows communication with other resources on the OpenStack cloud.
7. **Security Groups:**
    
    - In the "Security Groups" tab, select or create a security group that defines the network traffic allowed to and from your instance. It's recommended to create a custom security group with specific rules for secure access.
8. **Key Pair Selection:**
    
    - In the "Key Pair" tab, choose an existing SSH key pair for secure login to your instance. You can also generate a new key pair within Horizon if needed.
9. **Review and Launch:**
    
    - Carefully review all the configuration options you've chosen for your instance.
    - Once satisfied, click the "Launch Instance" button to initiate the creation process.
10. **Monitoring and Access:**
    

- After launching, your instance will appear in the "Instances" list with its status transitioning through "BUILDING" to "ACTIVE" when ready.
- Use the Horizon dashboard to view details, monitor performance, and access your instance (using SSH with the selected key pair).