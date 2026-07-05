**Ticket #72 — Update Performance Chart on Procore Products Website**

*Category: Web Server Administration / Code Deployment*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 72 |
| **Title** | Update Performance Chart on Procore Products Website |
| **Category** | Web Server Administration / Code Deployment |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

# **Objective**

Add a new 'Cache Memory' bar (pink color) to the Performance chart on the Procore Products website by replacing the live tooplate-scripts.js with the updated version from addPinkBarToPerformanceChart/.

# **Requirements**

- VM: dev-performance

- Website updated with Performance chart new feature

- New item: Cache Memory, Pink Color Bar

- File to replace: tooplate-scripts.js

- New script location: addPinkBarToPerformanceChart/tooplate-scripts-orig.js

# **Environment Details**

- VM: dev-performance

- Live JS: /var/www/html/procore-products/procore/js/tooplate-scripts.js

- Source: /var/www/html/procore-products/addPinkBarToPerformanceChart/tooplate-scripts-orig.js

# **Implementation Steps**

Step 1 — Navigate to the live procore directory:

cd /var/www/html/procore-products/procore

Step 2 — Back up the current script:

sudo cp -av js/tooplate-scripts.js js/tooplate-scripts.js.bak.$(date +%F_%H%M)

Step 3 — Copy the new script over the live file:

sudo cp -av /var/www/html/procore-products/addPinkBarToPerformanceChart/tooplate-scripts-orig.js /var/www/html/procore-products/procore/js/tooplate-scripts.js

Step 4 — Fix ownership and permissions:

sudo chown apache:apache /var/www/html/procore-products/procore/js/tooplate-scripts.js

sudo chmod 644 /var/www/html/procore-products/procore/js/tooplate-scripts.js

Step 5 — Restore SELinux context:

sudo restorecon -v /var/www/html/procore-products/procore/js/tooplate-scripts.js

Step 6 — Restart Apache:

sudo systemctl restart httpd

Step 7 — Verify the updated script contains Cache Memory:

grep -n "Cache Memory" /var/www/html/procore-products/procore/js/tooplate-scripts.js

# **Troubleshooting ****&**** Root Cause Analysis**

The updated script was present on disk and served by Apache, but the UI did not immediately reflect the new pink bar. Checksums were used to confirm the live file matched the repo source file.

index.html was inspected to confirm it was loading tooplate-scripts.js (not a cached or alternate version).

The remaining issue was frontend-side: browser caching of the old JavaScript file. After refreshing with a cache-busting query string (?v=2), the chart showed the Cache Memory bar successfully.

# **Validation ****&**** Testing**

md5sum /var/www/html/procore-products/procore/js/tooplate-scripts.js

md5sum /var/www/html/procore-products/addPinkBarToPerformanceChart/tooplate-scripts-orig.js

grep -n "Cache Memory" /var/www/html/procore-products/procore/js/tooplate-scripts.js

- Open the website and confirm the pink Cache Memory bar appears in the Performance chart

# **Key Lessons Learned**

- Always back up the live file before replacing it — use a timestamped backup name

- md5sum both files to confirm the copy succeeded and the live file matches the source

- Browser caching can mask a successful deployment — use hard refresh (Ctrl+Shift+R) or cache-busting query strings


Ticket #72 | Update Performance Chart on Procore Products Website | Procore Linux Jira  |  Page  of
