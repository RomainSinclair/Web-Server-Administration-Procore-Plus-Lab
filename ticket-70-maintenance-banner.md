
 **Field** | Value |
| --- | --- |
| **Ticket #** | 70 |
| **Title** | Notify Website Under Maintenance** |
| **Category** | Web Server Administration / Code Deployment |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |



# **Objective**

Deploy a maintenance banner to the Procore Products website on dev-performance by cloning the repo and copying files from the websiteDownForMaintenance/ directory into the live procore/ document root.

# **Requirements**

- VM: dev-performance

- Source Code Repository: git@gitlab.com:procoreplusmd/procore-products.git

- Website updated to show 'website under maintenance' banner

- Use files under websiteDownForMaintenance/

# **Environment Details**

- VM: dev-performance

- Repo path: /var/www/html/procore-products

- Live site path: /var/www/html/procore-products/procore

# **Implementation Steps**

Step 1 — Clone the repository (or pull latest changes):

git clone git@gitlab.com:procoreplusmd/procore-products.git

Step 2 — Copy maintenance banner files over the live site:

cp -a websiteDownForMaintenance/* procore/

Step 3 — Fix file ownership for Apache:

chown -R apache:apache /var/www/html/procore-products

Step 4 — Restore SELinux contexts:

restorecon -Rv /var/www/html

Step 5 — Reload Apache to pick up changes:

systemctl reload httpd

Step 6 — Verify the banner is served:

curl -I http://localhost/procore-products/procore/

# **Troubleshooting ****&**** Root Cause Analysis**

The page initially rendered as plain text because index.html referenced css/bootstrap.min.css, css/fontawesome.min.css, and css/templatemo-style.css but the live procore folder did not contain those assets.

The fix involved restoring missing CSS files (and webfonts/JS) from the backup procore directory, or applying inline CSS as a fallback. Browser cache also affected how relative CSS paths were resolved — appending a cache-busting query string (?v=2) forced fresh asset loading.

Trailing slash behavior in Apache also affected relative CSS path resolution — requests to /procore/ work correctly while /procore (no trailing slash) may cause relative paths to break.

# **Validation ****&**** Testing**

curl -I http://localhost/procore-products/procore/css/bootstrap.min.css

curl -I http://localhost/procore-products/procore/

- Open browser and confirm the maintenance banner displays with proper CSS styling

# **Key Lessons Learned**

- Always copy CSS/JS/webfont assets along with the HTML when deploying a page — missing assets cause plain-text rendering

- restorecon is required after copying files to /var/www/ on SELinux-enforcing systems

- Use trailing slash in Apache paths to ensure relative CSS/JS paths resolve correctly


Ticket #70 | Notify Website Under Maintenance | Procore Linux Jira  |  Page  of
