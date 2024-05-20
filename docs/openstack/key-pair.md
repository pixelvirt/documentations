**Creating a New Key Pair:**

1. **Access Horizon:** Log in to your OpenStack Horizon dashboard with appropriate credentials.
2. **Navigate to "Project" -> "Compute"  -> "Key Pairs".**
3. **Click "Create Key Pair".**
4. **Enter a descriptive name for your key pair (e.g., "my-key-pair").**
5. **(Optional) Choose a key type.** By default, it's usually SSH Key.
6. **Click "Create Key Pair".** Horizon will generate a new key pair consisting of a private key (`.pem` file) and a public key.

**Downloading the Private Key:**

- A dialog box will appear with the private key content.
- **Important:** It's crucial to download and save the private key securely. **Do not share your private key with anyone.** You'll use this private key to connect to your instances later.
- Click "Download Key Pair" to save the private key as a `.pem` file to your local machine.

**Importing an Existing Key Pair:**

1. **Navigate to "Project" -> "Compute" -> "Access & Security" -> "Key Pairs".**
2. **Click "Import Key Pair".**
3. **Enter a name for your key pair (e.g., "imported-key").**
4. **Paste the public key content into the "Public Key" text box.** You can obtain the public key from the existing key pair file (usually named `<key-name>.pub`).
5. **Click "Import Key Pair".** Horizon will import the public key associated with your existing key pair.

**Additional Notes:**

- **Security Groups:** Remember to configure security groups for your instances to control inbound traffic and ensure security. Even with a key pair, you'll need appropriate security group rules to allow SSH access on port 22 for your instances.
- **Key Pair Management:** You can view details, download private keys again (if needed), or delete key pairs from the "Key Pairs" list within Horizon.