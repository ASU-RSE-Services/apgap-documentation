+++
title = 'BE v1.8.0, FE v1.2.0'
date = 2026-08-07T07:07:07+01:00
weight = 11
+++

## **Changelog (Backend v1.8.0 / Frontend v1.2.0)**

### **New Features & Enhancements**

* **Reportability Rules Management:** Introduced a rule editor modal, preview API endpoints, and active rule counters ([*Ticket \#104*](https://github.com/azpathogens/apgap-development-tickets/issues/104)).  
* **Idle Notebook Auto-Shutdown:** Automatically stops idle Jupyter notebook server instances and sends notification alerts ([*Ticket \#225*](https://github.com/azpathogens/apgap-development-tickets/issues/225)).  
* **Rich-Text System Alerts & Acknowledgements:**  
  * Added rich-text/Markdown formatting support and safe HTML rendering ([*Ticket \#240*](https://github.com/azpathogens/apgap-development-tickets/issues/240)).  
  * Added "acknowledge" action for system alert URLs ([*Ticket \#241*](https://github.com/azpathogens/apgap-development-tickets/issues/241)).  
* **Saved Searches & Lab Filtering:**  
  * Added named saved searches for lab sequence lists ([*Ticket \#21*](https://github.com/azpathogens/apgap-development-tickets/issues/21)).  
  * Optimized dashboard and layout view for single-lab users ([*Ticket \#244*](https://github.com/azpathogens/apgap-development-tickets/issues/244), [*Ticket \#245*](https://github.com/azpathogens/apgap-development-tickets/issues/245)).  
  * Support filtering by lab when adding files to datasets ([*Ticket \#237*](https://github.com/azpathogens/apgap-development-tickets/issues/237)).  
* **Metadata & Dataset Tracking:**  
  * Added Excel (`.xlsx`) file upload support for applying metadata ([*Ticket \#173*](https://github.com/azpathogens/apgap-development-tickets/issues/173)).  
  * Added dataset modification date tracking and UI display logic ([*Ticket \#213*](https://github.com/azpathogens/apgap-development-tickets/issues/213)).  
  * Added a dashboard widget for pending metadata requests ([*Ticket \#239*](https://github.com/azpathogens/apgap-development-tickets/issues/239)).  
* **Usability & UX Enhancements:**  
  * Added automatic redirection back to the intended page after logging in ([*Ticket \#238*](https://github.com/azpathogens/apgap-development-tickets/issues/238)).  
  * Add dataset requests filtering by status ([Ticket \#236](https://github.com/azpathogens/apgap-development-tickets/issues/236)).  
  * Display status of batch endpoints ([Ticket \#232](https://github.com/azpathogens/apgap-development-tickets/issues/232)).

### **Behind the Scenes (Maintenance & Tech Debt)**

* **Framework & Library Upgrades:**  
  * Upgraded **Angular** to `20.3.26` ([Ticket \#233](https://github.com/azpathogens/apgap-development-tickets/issues/233)).  
  * Upgraded `pygraphviz` from `1.14` to `2.0.1`.  
  * Upgraded `actions/setup-python` from v4 to v7.  
  * Several dependency updates.
