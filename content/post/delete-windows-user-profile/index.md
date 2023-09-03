---
title: "Properly and Fully Delete Windows User Profile"
date: 2023-01-31T07:57:19-07:00
toc: false
---

Recently, while investigating a "Low Free Space on C: Volume" alert on one of our servers I took a look at the `C:\Users` folder and noticed a user profile folder for a sysadmin that hadn't worked for the company in over 10 years. I mentioned this to a coworker who said, "Yeah I used to look at `C:\Users` on all the servers and delete that folder if I saw it but I kind of gave up after a while." If you've tried this you likely have found that it takes Windows quite a while to delete all the system files, etc. inside that profile folder and can often run into weird NTFS permission issues, or the cursed Thumbs.db "in use by another program" annoyance.

Whatever the reason you may have for deleting the user profile from a Windows Server (or workstation), deleting that folder is not even fully removing the user profile from the machine, leaving registry keys and other references to that profile strewn about the OS. The better way to do this is to use Powershell to leverage CIM (Common Information Model) and remove the entire profile, which includes that folder, quickly and easily.

### Nuke the profile
For my use case, I wanted to cycle through our hundreds of servers to remove this old profile (and a couple others that I knew were floating around out there) from every single one. My first step is to gather a list of just the name of all those servers. Every environment is different but the most generic way to grab all Windows Server names out of Active Directory is by filtering the `Get-ADComputer` cmdlet.
```powershell
#Replace 'username' below with the name of the target folder in C:\Users
$servers = (Get-ADComputer -Filter {operatingSystem -like "*Windows Server*"}).Name
$username = "username"
```
Once we have all server names and our target username, we can take advantage of the fact that the `Get-CimInstance` cmdlet's parameter `-ComputerName` accepts an array of strings (which our $servers variable from above will be). As a result, we won't need a foreach loop, or to push this out as jobs, it's a simple one-liner pipeline to do the rest:
```powershell
Get-CimInstance -ComputerName $servers -ClassName Win32_UserProfile | Where-Object {$_.localpath -like "*$username"} | Remove-CimInstance
```
That's it! This will go through all servers and remove all references to the user profile, including the profile folder, that match that username. In my environment it took less time for this to run through hundreds of servers than it took for a single user profile folder to be deleted in File Explorer, so in this case doing things the proper way also ended up being the easy way. Win-Win!