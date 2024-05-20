1. **Login to Horizon Dashboard**:
    
    - Open a web browser and navigate to the Horizon dashboard URL.
    - Enter your username and password to log in.
2. **Navigate to the Images Panel**:
    
    - Once logged in, on the left-hand side menu, click on "Compute"  to expand the menu.
    - Click on "Images" to navigate to the Images panel.
3. **Create a New Image**:
    
    - In the Images panel, click on the "Create Image" button (usually located at the top right corner or bottom of the page).
4. **Fill in Image Details**:
    - Provide a name for the new image in the "Name" field.
    - Optionally, you can add a description to the image in the "Description" field.
    - Select the source from which you want to create the image:
        - **Image File**: If you have an image file that you want to upload.
        - **Instance Snapshot**: If you want to create an image from an existing instance snapshot.
        - **Volume Snapshot**: If you want to create an image from an existing volume snapshot.
    - Depending on the source selected, provide additional details like file location or instance/volume snapshot.
5. **Configure Image Properties**:
    
    - Set the format of the image (usually QCOW2 or RAW).
    - Specify the minimum disk and minimum RAM required for instances created from this image.
    - You may also set other properties like architecture, OS type, etc., if needed.
6. **Add Tags (Optional)**:
    
    - You can optionally add tags to the image for easier categorization and organization.
7. **Review and Create**:
    
    - Review all the details provided for the new image.
    - Click on the "Create Image" or "Create" button to create the image.

8. **Verify Image Creation**:
    - Once the image creation process is complete, you should see the new image listed in the Images panel of the Horizon dashboard.
    - You can also verify the image creation by launching instances from the newly created image.