Procore Linux Jira Tickets  |  Prepared by: Romain Sinclair

| Ticket #71 **Modify User Account Profile Picture** | **COMPLETED** |
| --- | --- |

| **VM / Host** | dev-performance-rs1.procore.prod1 |
| --- | --- |
| **Live Image Path** | /var/www/html/procore-products/procore/img/ |
| **Profile Image** | jessica.jpg (resized from notification-dog.jpg) |
| **References Updated** | notification-01/02/03 HTML files |
| **Category** | Web Server Administration / Code Deployment |

**Objective**

Maintain web server service through changes in code deployments. The task was to update the user profile picture on the Procore Products website. This required resizing a source image and updating the HTML notification files that reference it.

**Requirements**

- VM: dev-performance

- Update the profile picture for the user account

- Resize image to 100x100 pixels using ImageMagick convert

- Update all HTML notification files referencing the profile image

- Restart Apache after changes

**Implementation Steps**

# Step 1: Resize the source image to 100x100 as jessica.jpg
sudo convert /var/www/html/procore-products/procore/img/notification-dog.jpg \
  -resize 100x100 -gravity center 100x100 \
  /var/www/html/procore-products/procore/img/jessica.jpg

# Step 2: Verify the image file was created
ls -lh /var/www/html/procore-products/procore/img/

# Step 3: Copy the image to the img directory (if needed)
sudo cp jessica.jpg /var/www/html/procore-products/procore/img/jessica.jpg

# Step 4: Restore SELinux context on the image
sudo restorecon /var/www/html/procore-products/procore/img/jessica.jpg

# Step 5: Update notification HTML files to reference the new image
sudo sed -i 's|notification-dog.jpg|img/jessica.jpg|g' \
  /var/www/html/procore-products/procore/notification-01.html

# Step 6: Verify the change
grep -n "jessica" /var/www/html/procore-products/procore/notification-01.html

# Step 7: Restart Apache
sudo systemctl restart httpd

**Troubleshooting ****&**** Root Cause Analysis**

Key considerations for this ticket:

- ImageMagick (convert) must be installed — if not: sudo dnf install -y ImageMagick

- SELinux context must be restored on new image files or Apache will return a 403

- All notification HTML files (01, 02, 03) need to be updated to reference the new image

**Validation ****&**** Testing**

ls -lh /var/www/html/procore-products/procore/img/jessica.jpg
grep "jessica" /var/www/html/procore-products/procore/notification-01.html
sudo systemctl status httpd
# Browse to the Procore Products site and verify profile picture updated

**Screenshots**

*dev-performance: convert image to jessica.jpg, ls img/, sed update to notification HTML files, systemctl restart httpd*

Procore-Plus Lab Environment  |  CentOS Stream / RHEL  |  Page