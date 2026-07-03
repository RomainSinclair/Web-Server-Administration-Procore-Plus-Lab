# Web Server Administration — Procore-Plus Lab

Web Server Administration — Apache/httpd operations, content deployment via GitLab and rsync, Docker-based WordPress, incident response, and production website changes.

## Environment
CentOS Stream / RHEL-based VMs in the Procore-Plus lab (dev-app, stage-web, dev-performance hosts), managed as a production-style environment with Jira ticket tracking.

## Skills & Tools
Apache httpd, SELinux, rsync, GitLab, Docker + MariaDB containers, logrotate, cron, incident response, firewall/port troubleshooting, HTML/CSS content deployment

## Tickets

| Ticket | Title | Documentation |
| --- | --- | --- |
| #29 | Configure Apache Web Content via GitLab & Rsync | [ticket-29-apache-content-gitlab-rsync.md](tickets/ticket-29-apache-content-gitlab-rsync.md) |
| #46 | Cron Job: Restart Apache Every 2 Days at 11 AM | [ticket-46-cron-apache-restart.md](tickets/ticket-46-cron-apache-restart.md) |
| #59 | Rotate HTTPD Logs | [ticket-59-httpd-log-rotation.md](tickets/ticket-59-httpd-log-rotation.md) |
| #67 | Deploy WordPress via Docker Containers | [ticket-67-wordpress-docker-deploy.md](tickets/ticket-67-wordpress-docker-deploy.md) |
| #68 | Dev-Performance Web Server Not Responding | [ticket-68-dev-web-server-down.md](tickets/ticket-68-dev-web-server-down.md) |
| #69 | Dev Web Server Down — Apache httpd Missing Config / Port 8080 Fix | [ticket-69-httpd-incident-fix.md](tickets/ticket-69-httpd-incident-fix.md) |
| #70 | Notify Website Under Maintenance | [ticket-70-maintenance-banner.md](tickets/ticket-70-maintenance-banner.md) |
| #71 | Modify User Account Profile Picture | [ticket-71-profile-picture-update.md](tickets/ticket-71-profile-picture-update.md) |
| #72 | Update Performance Chart on Procore Products Website | [ticket-72-performance-chart-update.md](tickets/ticket-72-performance-chart-update.md) |
| #73 | Update Order List on Procore Products Website | [ticket-73-order-list-update.md](tickets/ticket-73-order-list-update.md) |
| #74 | Remove Maintenance Banner | [ticket-74-remove-maintenance-banner.md](tickets/ticket-74-remove-maintenance-banner.md) |

## About
Each ticket document includes the objective, environment details, step-by-step resolution, commands used, and verification/outcome.
