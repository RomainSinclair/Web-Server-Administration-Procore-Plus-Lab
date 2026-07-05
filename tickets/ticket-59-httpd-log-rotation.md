**Ticket #59 — Rotate HTTPD Logs**

*Category: Web Server Administration / Apache Log Management*

| **Field** | Value |
| --- | --- |
| **Ticket #** | 59 |
| **Title** | Rotate HTTPD Logs |
| **Category** | Web Server Administration / Apache Log Management |
| **Prepared by** | Romain Sinclair |
| **Environment** | Procore-Plus Lab (CentOS Stream / RHEL-based) |

# **Objective**

Modify logrotate settings for HTTPD on stage-web so that Apache logs rotate daily and only 14 days of logs are retained, replacing the default weekly/4-week retention policy.

# **Requirements**

- VM: stage-web

- Log rotation: daily

- Retention: 14 days only

- Modify /etc/logrotate.d/httpd

# **Environment Details**

- VM: stage-web

- Service: httpd (Apache HTTP Server)

- Logrotate config: /etc/logrotate.d/httpd

- Log location: /var/log/httpd/

# **Implementation Steps**

Step 1 — Inspect current log files and sizes:

ls -l /var/log/httpd/

Step 2 — Review the current logrotate config for httpd:

sudo cat /etc/logrotate.d/httpd

Step 3 — Edit the httpd logrotate config:

sudo vi /etc/logrotate.d/httpd

Change 'weekly' to 'daily'

Change 'rotate 4' to 'rotate 14'

Step 4 — Dry-run to verify config syntax:

sudo logrotate -d /etc/logrotate.conf

Step 5 — Force rotation to test immediately:

sudo logrotate -f /etc/logrotate.conf

Step 6 — Verify rotated logs appeared:

ls -lh /var/log/httpd/

# **Troubleshooting ****&**** Root Cause Analysis**

The main concern was avoiding duplicate logrotate definitions — if an httpd block exists in both /etc/logrotate.conf and /etc/logrotate.d/httpd, logrotate may warn about duplicate entries.

Using logrotate -d (dry run) before -f (force) confirms the config parses correctly without actually rotating logs.

The correct approach was to edit the existing /etc/logrotate.d/httpd file rather than creating a new one, to avoid duplicate block conflicts.

# **Validation ****&**** Testing**

sudo logrotate -d /etc/logrotate.conf

sudo logrotate -f /etc/logrotate.conf

ls -lh /var/log/httpd/

- Verify rotated log files (access_log.1, error_log.1) appear in /var/log/httpd/

# **Key Lessons Learned**

- Always run logrotate -d (debug/dry-run) before -f (force) to catch config errors

- Edit existing /etc/logrotate.d/httpd instead of adding a new file — avoids duplicate blocks

- 'rotate 14' with 'daily' means 14 daily rotations are kept before the oldest is deleted

# **Screenshots**

[Screenshot: stage-web — logrotate config for httpd showing daily rotation and 14-day retention]

[Screenshot: logrotate force run and /var/log/httpd listing with rotated files]

Ticket #59 | Rotate HTTPD Logs | Procore Linux Jira  |  Page  of
