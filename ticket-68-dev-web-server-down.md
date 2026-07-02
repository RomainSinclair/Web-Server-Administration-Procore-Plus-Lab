Procore Linux Jira Tickets  |  Prepared by: Romain Sinclair

| Ticket #68 **Dev-Performance Web Server Not Responding** | **SEE RELATED TICKET** |
| --- | --- |

| **VM / Host** | dev-performance-rs1.procore.prod1 |
| --- | --- |
| **Category** | Web Server Administration / Code Deployment |
| **Status** | Details merged into adjacent ticket in original report |

**Objective**

Investigate and resolve why the dev-performance web server was not responding to HTTP requests. Objective: Maintain web server service through diagnostics and recovery.  Related ticket content covers service restart, Apache config validation, and CheckMK alert resolution.

**Note: **The detailed step-by-step content for this ticket was included inline within an adjacent ticket in the original Jira report. For full implementation details, refer to the related ticket document: Ticket-61-Fix-Web-Server-CheckMK.docx.

**Environment**

All web server tickets in this group operated on dev-performance-rs1.procore.prod1, running CentOS Stream 9 with Apache httpd and the Procore Products GitLab repository cloned to /var/www/html/procore-products/.

**Related Ticket Documents**

- See the related ticket document in this same folder (4-Web-Server-Administration) for full implementation steps, troubleshooting, and screenshots.

Procore-Plus Lab Environment  |  CentOS Stream / RHEL  |  Page