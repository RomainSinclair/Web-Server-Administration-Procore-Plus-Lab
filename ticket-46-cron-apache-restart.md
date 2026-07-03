> **PROCORE-PLUS LAB** **Ticket T-46: Cron Job: Restart Apache Every 2 Days at 11 AM** *Cron · crontab · httpd · systemctl · Log Verification*

| **Ticket ID**   46 | **Reporter**   Procore Plus |
| --- | --- |
| **Project**   PROCORE-Plus Lab | **Assignee**   Romain Sinclair |
| **Priority**   Medium | **Server**   stage-web (stage-web-ma3) |
| **Status**   Done ✓ | **Service**   httpd (Apache HTTP Server) |

## 1. Objective

Create a cron job on stage-web to automatically restart the Apache web server (httpd) every 2 days at 11:00 AM. Verify the cron job runs correctly and its execution is captured in a log file.

> **  Task Requirements** VM: stage-web Schedule: Every 2 days at 11:00 AM (cron: 0 11 */2 * *) Command: /usr/bin/systemctl restart httpd Verify cron execution via log file or systemctl restart timestamps Log output redirected to /var/log/cron-apache-restart.log

## 2. Step-by-Step Execution

### Step 1 — Confirm systemctl Path and Create Cron Job

```bash
# Confirm the full path to systemctl (required for cron) command -v systemctl
# Output: /usr/bin/systemctl
# Open crontab editor crontab -e
# Insert the cron job: 0 11 */2 * * /usr/bin/systemctl restart httpd
# Save and exit
```

> **  Engineer's Note** Cron runs in a minimal environment — $PATH may not include /usr/bin. Always use the FULL path to commands in cron entries. Use "command -v systemctl" to find the correct path before writing the cron job.

> **  Screenshot 1: command -v systemctl output and crontab -e showing the Apache restart cron entry**

### Step 2 — Verify the Cron Job and Check Logs

```bash
# Verify cron job was saved
sudo crontab -l
# Expected: 0 11 */2 * * /usr/bin/systemctl restart httpd
# Initial log check (may not exist until first run) grep CRON /var/log/syslog │ grep "restart httpd"
# Result: /var/log/syslog: No such file — CentOS uses journald not syslog
```

> **  Troubleshooting** Issue: /var/log/syslog does not exist on CentOS/RHEL — system uses systemd journal. Fix: Update the cron job to redirect output directly to a dedicated log file. Updated cron entry:   0 11 */2 * * /usr/bin/systemctl restart httpd >> /var/log/cron-apache-restart.log 2>&1

> **  Screenshot 2: grep for syslog showing No such file and updated crontab -l with log redirect**

### Step 3 — Test at 1-Minute Interval to Confirm Execution

Temporarily change the schedule to every minute to verify the cron job actually executes before restoring the correct schedule.

```bash
# Temporarily change to every-minute for testing
# crontab -e → change to: */1 * * * * /usr/bin/systemctl restart httpd >> /var/log/cron-apache-restart.log 2>&1
# Wait 1-2 minutes, then check systemctl status
sudo systemctl status httpd
# Look at "Active:" timestamp — should show recent restart
# Also check the restart timestamp changes each minute
# Active: active (running) since Fri 2025-10-17 16:11:02 EDT; 7s ago  ← recent
# After confirming it works — restore the correct 2-day schedule
# crontab -e → change back to: 0 11 */2 * * /usr/bin/systemctl restart httpd >> /var/log/cron-apache-restart.log 2>&1
```

> **  Screenshot 3: systemctl status httpd showing active timestamp updating each minute confirming cron runs**

> **  Screenshot 4: Final sudo crontab -l showing restored 0 11 */2 schedule with log redirect**

## 3. Verification Matrix

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | systemctl full path confirmed | command -v systemctl: /usr/bin/systemctl |  Done |
| 2 | Cron job created (0 11 */2 * *) | sudo crontab -l shows correct entry |  Done |
| 3 | Log redirect added (>> ... 2>&1) | crontab entry includes log file path |  Done |
| 4 | Cron execution verified via 1-min test | systemctl status httpd shows recent restart timestamp |  Done |
| 5 | Schedule restored to every 2 days at 11 AM | sudo crontab -l: 0 11 */2 * * |  Done |
| 6 | httpd confirmed active and running | systemctl status httpd: active (running) |  Done |

> **Ticket T-46 · Cron Job: Restart Apache Every 2 Days at 11 AM · Procore-Plus Lab***  │  Assignee: Romain Sinclair · PROCORE Infrastructure Team*
