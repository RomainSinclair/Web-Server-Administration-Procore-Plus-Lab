| **PROCORE-PLUS LAB** **Ticket 29: Configure Apache Web Content via GitLab ****&**** Rsync** *Apache · GitLab · rsync · SELinux · Web Deployment* |
| --- |

| **Ticket ID**   29 |
| --- | --- |
| **Project**   PROCORE-Plus Lab | **Assignee**   Romain |
| **Type**   Task | **Date**   October 11, 2025 |
| **Priority**   Medium | **Server**   stage-web (stage-web-ma3) |
| **Status**   Done ✓ | **Repository**   git@gitlab.com:procoreplusmd/ariclaw.git |

**1. ****Objective**

Configure the Ariclaw web server content on the stage-web server by pulling the web application code from GitLab and deploying it cleanly to Apache's document root using rsync. The deployed page must be externally accessible.

| **📋  Task Requirements** VM: stage-web GitLab repository: git@gitlab.com:procoreplusmd/ariclaw.git Web content deployed to /var/www/html/ SSH keys must be configured for GitLab access Provide working webpage URL |
| --- |

**2. ****Step-by-Step Execution**

**Step 1 — ****Install git and rsync**

| # Install required tools on stage-web sudo dnf -y install git rsync # Verify installations git --version && rsync --version |
| --- |

| **  Screenshot 1: dnf install git rsync output showing packages installed** |
| --- |

**Step 2 — ****Configure GitLab SSH Access**

| # Test SSH connection to GitLab ssh -T git@gitlab.com # Expected: Welcome to GitLab, @<username>! |
| --- |

| **  Engineer****'****s Note** The -T flag tells SSH not to allocate an interactive terminal on the remote side — correct for testing GitLab authentication. If the SSH key is not configured, add the public key to GitLab first (Settings → SSH Keys). |
| --- |

**Step 3 — ****Clone Repository and Deploy with Rsync**

| # Clone the Ariclaw repo to /tmp (NOT directly to /var/www/html) git clone https://gitlab.com/procoreplusmd/ariclaw.git /tmp/ariclaw # Why clone to /tmp first? # Rsync lets you exclude the .git/ directory from deployment # — avoids exposing .git to the web server # Deploy to Apache document root with rsync sudo rsync -av --delete /tmp/ariclaw/ /var/www/html/ # -a : archive mode (preserves permissions, symlinks, timestamps) # -v : verbose output # --delete : remove files in destination not in source (clean deploy) |
| --- |

| **  Troubleshooting: Server Freeze During git clone** Symptom: Server froze mid-clone — screen stopped updating, no new output. Cause: VPN drop or server resource spike caused the clone to stall. Fix: Navigated to /tmp, ran sudo rm -rf ariclaw/ to delete the incomplete clone.      Closed and reopened the server connection, then re-ran git clone successfully. Result: Second clone completed fully — 410/410 objects received. |
| --- |

| **  Screenshot 2: Successful git clone output showing 410 objects received and rsync deployment** |
| --- |

**Step 4 — ****Relabel SELinux and Reload Apache**

| # Relabel SELinux security contexts for Apache sudo restorecon -Rv /var/www/html # Relabels all files from httpd_log_t → httpd_sys_content_t # Reload Apache to apply changes sudo systemctl reload httpd # Verify Apache is serving the content curl -I http://127.0.0.1 # Expected: # HTTP/1.1 200 OK # Server: Apache/2.4.62 (CentOS Stream) |
| --- |

| **  Engineer****'****s Note** SELinux relabeling is critical on CentOS/RHEL. Without restorecon, Apache may return "403 Forbidden" even if file permissions look correct — SELinux blocks the read because the context is wrong. |
| --- |

| **  Screenshot 3: curl -I output showing HTTP/1.1 200 OK and Apache/2.4.62 server response** |
| --- |

**3. ****Verification Matrix**

| **#** | **Check Item** | **How to Verify** | **Status** |
| --- | --- | --- | --- |
| 1 | git and rsync installed on stage-web | git --version, rsync --version |  Done |
| 2 | GitLab SSH access confirmed | ssh -T git@gitlab.com: Welcome message |  Done |
| 3 | Repository cloned to /tmp/ariclaw | ls /tmp/ariclaw shows web content files |  Done |
| 4 | Content deployed to /var/www/html/ | ls /var/www/html shows Ariclaw files |  Done |
| 5 | SELinux contexts relabeled | restorecon -Rv /var/www/html ran without errors |  Done |
| 6 | Apache reloaded successfully | systemctl reload httpd — no errors |  Done |
| 7 | HTTP 200 confirmed via curl | curl -I 127.0.0.1: HTTP/1.1 200 OK |  Done |

| **Ticket 29 · Configure Apache Web Content via GitLab ****&**** Rsync · Procore-Plus Lab***  │  Assignee: HeRO · PROCORE Infrastructure Team* |
| --- |

PROCORE Infrastructure Team  |  
