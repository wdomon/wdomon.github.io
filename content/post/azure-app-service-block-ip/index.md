---
title: "Block an IP address on Azure App Service"
date: 2022-08-27T18:01:14-07:00
draft: false
---

If malicious/suspicious user activity (scanning, auth attempts, etc.) is detected on an Azure App Service, blocking the source IP is often the quickest way to prevent further activity. To do so, it's as easy as adding a Network rule to the app service that blocks that IP/range. Access the app service in question and click the "Networking" navigation blade.  


Click "Access restriction"  
![](access-restriction.png)  

Click "Add rule"  
![](add-rule.png)


Give the rule a name, change the toggle to "Deny," set a priority that is higher than the "Allow All" rule that exists (assuming this is a public app service), add the CIDR notation of the IP address in question, and click the "Add rule" button to save the changes. All traffic from that IP address will immediately be blocked.  
![](rule-settings.png)