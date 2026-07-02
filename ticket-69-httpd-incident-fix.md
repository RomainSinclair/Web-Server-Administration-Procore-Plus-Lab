| **PROCORE-PLUS LAB** **Ticket T-69: Dev Web Server Not Responding — Apache httpd Fix** *Incident Response · httpd · Missing Config · Firewall · Port 8080* |
| --- |

| **Ticket ID**   T-69 (TL-68) | **Reporter**   Michael Anthony Lopez |
| --- | --- |
| **Project**   PROCORE-Plus Lab | **Assignee**   HeRO |
| **Type**   Incident / Task | **Date**   November 2025 |
| **Priority**   High | **Server**   dev-performance-tl.procore.prod1 |
| **Status**   Done ✓ | **Root Cause**   httpd.conf missing after corrupt install |

**1. ****Objective**

End users are reporting connectivity issues with the internal dev-performance system. Troubleshoot the network and web server to identify and resolve the root cause of the service disruption.

| **📋  Task Requirements** VM: dev-performance Investigate and resolve connectivity issues reported by end users Restore web service on port 8080 Ensure httpd is enabled, active, and config is intact |
| --- |

**2. ****Incident Timeline ****&**** Diagnosis**

**Step 1 — ****Verify Network Connectivity**

Before checking the web service, confirm basic network connectivity between servers.

| # Ping dev-performance from dev-app and stage-web ping -c 4 dev-performance-tl.procore.prod1 # Result: 0% packet loss — network layer is UP # Test HTTP on default port (80) curl -v http://dev-performance-tl.procore.prod1 # Result: Connected ✓ # Test HTTP on port 8080 curl -v http://dev-performance-tl.procore.prod1:8080 # Result: Connection REFUSED ✗ — port 8080 not responding |
| --- |

| **💡  Engineer****'****s Note** This narrows the issue: network is fine, port 80 works, but port 8080 fails. Root cause is either the Apache config is wrong (not listening on 8080) or port 8080 is blocked by the firewall. |
| --- |

| **📸  Screenshot 1: ping tests showing 0% packet loss and curl showing port 8080 connection refused** |
| --- |

**Step 2 — ****Check Apache Service and Logs**

Inspect the httpd service status and journal logs to find the exact error.

| # Check Apache status sudo systemctl status httpd --no-pager -l # Shows: ExecReload failure — code=exited, status=1/FAILURE # Key error from journal logs: # httpd: Could not open configuration file # /etc/httpd/conf/httpd.conf: No such file or directory # Reload failed for The Apache HTTP Server |
| --- |

| **⚠️  Troubleshooting** Root Cause Identified: /etc/httpd/conf/httpd.conf is MISSING. This means Apache is installed but its main configuration file was deleted or never created. Possible causes:   1. httpd was installed but the install was incomplete or corrupted   2. httpd.conf was accidentally deleted   3. A previous reinstall/upgrade left the config directory empty Confirmed with:  rpm -q httpd  →  httpd-2.4.62-7.el9.x86_64  (package IS installed)                  ls /etc/httpd/conf/httpd.conf  →  No such file or directory |
| --- |

| **📸  Screenshot 2: systemctl status httpd showing FAILURE and ls /etc/httpd/conf/ showing missing httpd.conf** |
| --- |

**Step 3 — ****Reinstall httpd to Restore Missing Config**

Use dnf reinstall to restore the missing configuration file without removing user data.

| # Reinstall httpd to restore the missing httpd.conf sudo dnf reinstall -y httpd # Output: Reinstalled: httpd-2.4.62-7.el9.x86_64 # → This restores /etc/httpd/conf/httpd.conf # Verify the config file is now present ls -la /etc/httpd/conf/httpd.conf # Expected: -rw-r--r--. 1 root root <size> /etc/httpd/conf/httpd.conf |
| --- |

| **📸  Screenshot 3: dnf reinstall httpd output showing Reinstalled and ls confirming httpd.conf exists** |
| --- |

**Step 4 — ****Configure Apache to Listen on Port 8080**

The default httpd.conf listens on port 80. Update it to listen on port 8080 as required.

| # Edit the Apache configuration sudo vi /etc/httpd/conf/httpd.conf # Find the line: # Listen 80 # Change it to: # Listen 8080 # Save and quit (:wq) |
| --- |

**Step 5 — ****Open Port 8080 in the Firewall**

| # Add port 8080 to the firewall (was not present — verified with firewall-cmd --list-all) sudo firewall-cmd --add-port=8080/tcp --permanent sudo firewall-cmd --reload # Enable and start httpd sudo systemctl enable --now httpd # Verify httpd is active sudo systemctl status httpd --no-pager -l |
| --- |

| **📸  Screenshot 4: firewall-cmd --add-port output and systemctl enable --now httpd showing active (running)** |
| --- |

**Step 6 — ****Confirm Service Restored**

| # Test port 8080 is now accessible curl -v http://dev-performance-tl.procore.prod1:8080 # Expected: HTTP/1.1 200 OK # Verify from dev-app and stage-web as well curl http://dev-performance-tl.procore.prod1:8080 |
| --- |

| **📸  Screenshot 5: curl showing HTTP 200 OK response on port 8080 from dev-performance** |
| --- |

**3. ****Root Cause Summary**

| **Area** | **Finding** | **Resolution** |
| --- | --- | --- |
| Network | Ping successful — network layer was never the issue | No action needed |
| Port 8080 | Connection refused — port not open in firewall | firewall-cmd --add-port=8080/tcp |
| Apache Config | httpd.conf missing — corrupt/incomplete install | dnf reinstall -y httpd |
| Listen Directive | Apache configured for port 80 not 8080 | Edited httpd.conf: Listen 8080 |
| Service State | httpd not running due to config failure | systemctl enable --now httpd |

**4. ****Verification Matrix**

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | Network connectivity confirmed (ping) | ping -c 4 dev-performance: 0% packet loss | ✅  Done |
| 2 | Root cause identified: missing httpd.conf | ls /etc/httpd/conf/httpd.conf: No such file | ✅  Done |
| 3 | httpd package confirmed installed | rpm -q httpd: httpd-2.4.62-7.el9.x86_64 | ✅  Done |
| 4 | httpd.conf restored via dnf reinstall | ls /etc/httpd/conf/httpd.conf: file present | ✅  Done |
| 5 | Listen directive changed to port 8080 | grep Listen /etc/httpd/conf/httpd.conf: Listen 8080 | ✅  Done |
| 6 | Port 8080 opened in firewall | firewall-cmd --list-ports: 8080/tcp | ✅  Done |
| 7 | httpd enabled and running | systemctl status httpd: active (running) | ✅  Done |
| 8 | HTTP 200 confirmed on port 8080 | curl -v http://dev-performance:8080: HTTP/1.1 200 OK | ✅  Done |

| **Ticket T-69 · Dev Web Server Not Responding — Apache httpd Fix · Procore-Plus Lab***  │  Assignee: HeRO · PROCORE Infrastructure Team* |
| --- |

PROCORE Infrastructure Team  |  Page  of