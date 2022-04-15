# Active Directory Introduction

## Goal

-   Create an Active Directory (AD) forest

-   Merge Win10 with the AD forest

-   Login into Win10 with AD username/PW

-   Setup a network share with rights

-   Connect to share via Win10 and access files

-   Map Drive letter to network share

## Lab Setup 

1.  Using GNS3 create a network that looks like the following diagram.

![][1]

2.  Start Win2019 and Win10 VMs and wait for the desktops to appear or start it by clicking on Windows menu and searching for "Server Manager".
<br>

3.  On Win2019 the Server Manager is the center of Windows GUI admin functions. Start the Server Manager application by right-clicking on My Computer icon and selecting the Management choice from the context list. It's not uncommon for an "Installation Progress" window to open. This is informing the admin about package installation status. If this occurs select the Close button in the lower right corner.

## Machine Names and Naming

DNS is integral to the function of AD because of this each machine name **must be unique**. For this lab the name of the forest is "lab.com" and the machine name is "its-win2019-1" then the unique hostname for the machine in DNS will be "its-win2019-1.lab.com". This is also known as an FQDN (fully qualified domain name). In a professional setting documentation of the machine names will need to be kept so that two machines don't get the same name.

## Static IP Address

4.  Since this VM will be a server (directory services is a server service) you will need to assign it a static IP. Open Network icon on the desktop, right-click on the appropreate NIC, select "Properties", then select "Internet Protocol Version 4 (TCP/IPv4)" and click on the "Properties" button. This sub-sub-sub-(far too many sub)-properties window allows the configuration of IP in Windows. Change the BOTTOM radio-button option to, "Use the following DNS server addresses:". Preferred DNS and Alternate DNS servers will un-gray at this point.  select "Properties", select "Internet Protocol Version 4 (TCP/IPv4)" and click on the "Properties" button. This sub-sub-sub-(far too many sub)-properties window allows the configuration of IP in Windows. Change the BOTTOM radio-button option to, "Use the following DNS server addresses:". Preferred DNS and Alternate DNS servers will un-gray at this point. Review the [Tech Nugget - N0.6 - Basic Diag Tools - NIC Config] for more help.

    -   IP Address: `192.168.122.200`
    -   Subnet mask: `255.255.255.0`
    -   Gateway: `192.168.122.1`
    -   DNS: `192.168.122.1` (Note: this will change after you install AD services).
\
Once the IP changes are made **make sure they work!** Ping the outside world and make sure you can resolve hostnames (`ping 8.8.8.8` and `ping google.com`).

## AD Forest Creation

In server manager we can add "Roles" and "Features" to a server. Roles tend to be BIG things like DHCP Servers and DNS servers and AD Controllers. Features tend to be smaller things like .NET installation or telnet client installations.

5.  In the upper right select the *Manage* menu and then *Add Roles and Features*. Work through the GUI to add the "Active Directory Domain Services" Role. Don't add any OTHER features at this time.
<br>

6.  As part of the Installation Progress the option to "Promote this server to a domain controller" will be presented. Click on it to start the wizard to configure the rest of the AD forest.

![][2]

7.  At *Deployment Configuration* select *Add new forest* option and set the *Root domain name:* to lab.com. The lab.com DNS is NOT controlled by us. Meaning that it will only work INSIDE your GNS3 project!
<br>

8.  At *Domain Controller Options* set the Directory Services Restore Mode password to the usual itsclass password (you should know what it is).
<br>

9.  At *DNS Options* select next and move on. In a typical corporate setup, more integration of DNS and AD would need to be setup a head of time.
<br>

10. Select next at *Additional Options, Paths, Review Options, Prerequisites Check.* (In the corporate world all those warnings should be read and understood before moving on).
<br>

11. At *Prerequisites Check* select Install. This could take a while depending on the speed of the VM and conditions and there is a required reboot and a long wait while it sets everything up.
<br>

12. Once rebooted and logged in, open the Start Menu, and use the search option to look for "Windows Administrative Tools" (MMC with pre-set snap-ins). In the folder that opens note all of the entries that start with "Active Directory..." are new entries as well as the DNS entry. In addition, there new entries on the left-hand side bar of the Server Manager dialog called *AD DS*.
\
At this point all the basics of AD are up and running. In a large organization there might be a dozen to several dozen Domain Controllers (DC) that work interchangeably for backup or latency reasons.

## Users and Groups

13. User and Group creation is one of the first things needed after creating a new domain. *Use Active Directory Users and Computers* icon to open the MMC with the correct snap-in added. Add a user to the lab.com -> Users folder by right-clicking on the Users folder going to New -> User. *Set First name:* and *User logon name:* to "a-ducky". Password set to usual password.\
\
**Note:** Prepending the "a-" to the username it denotes it as an "administrator equivalent" user. Set a password that's easy to remember (in this case use the itsclass password). **UN-** check "User must change password at next logon" option and **DO** check "Password never expires" option.\
\
**DO NOT** change the user Administrator password. That's the backdoor into the system in case there is a mistake!
<br>

14. The "a-ducky" user is an "administrator equivalent" user meaning this user account needs to be added this "Domain Admins" group. Being in this group will give that username the same godlike rights that the administrator account has across all machines and functions of the domain. Double-Click on *Domain Admins* group, select the *Members* tab and the *Add* button. Enter "a-ducky" in the Object Names field and press *Check Names* button. This will validate the username and shows you the full username. If the name validates press OK to add the user to the group. Press OK to close out of the group dialog and save updates.
<br>

15. Normally everyday users aren't in the Domain Admins group. It's too dangerous to have that much power in an account they could accidentally destroy half the domain before they realize what they are doing. This is why we make "a-" accounts and then only use them when we NEED and administrative function.
\
Go and create a the following normal user accounts (Sally Smith, username:smiths and Bob Jones, username: jonesb and Tom Jerry, username: jerryt). Create two different groups (a security group is fine). One called "Marketing" and one called "Sales". Put "smiths" in the Marketing group and "jonesb" in the Sales group, leave jerryt alone. Set the all account passwords to the usual itsclass pw.

## Using Administrative Tools

16. The MMC is a great place to bring your "most used" tools together. In our case it will be:

-   Active Directory Users and Computers

-   DNS

-   Event Viewer (Local)

-   Windows Defender Firewall with Advanced Security on Local Computer

-   Group Policy Management

-   Resultant Set of Policy

17. Open a Run dialog (Start Button -> Run) and type "MMC", click OK. This opens a blank MMC, File -> Add/Remove Snap-in... Presents a list of snap-ins to add to MMC. Select all the options on the list above then click on OK.
\
Before going further save the MMC choices so it is not necessary to rebuild the MMC snap-in list every time it is opened. Select File -> Save option, in the save box, select a file name for the console  (that's the name given what is being created). Select the DESKTOP as the location for where to save this console. This will provide an icon to click on for future uses of the MMC. Once saved close the MMC. The saved console should be on the desktop. Double click on the custom MMC and the snap-in list should appear just as it was left.

## Merging machines with the Active Directory Domain (the BORG!)

18. Start and login to your Windows 10 (it may auto-login depending on the VM)

### Change the DNS (but not IP) of Win 10

19. Remember without DNS AD won't work! Double click on the Network icon on the desktop. One of the ethernet cards, usually named "Ethernet \<number\>" will the active NIC (no red X).
<br>

20. Open Network icon on desktop, right-click on appropreate NIC, select "Properties", then select "Internet Protocol Version 4 (TCP/IPv4)" and click on the "Properties" button. This sub-sub-sub-(far too many sub)-properties window allows the configuration of IP in Windows. Change the BOTTOM radio-button option to, "Use the following DNS server addresses:". Preferred DNS and Alternate DNS servers will un-gray at this point.
\
To work with the domain the Windows 10 box must point only to DNS servers that understand the lab.com AD domain. **ONLY** fill in only the Primary DNS server with 192.168.122.200 (the AD controller and DNS server). Press on OK to all the windows until they are closed and then close the Network Connections window. See picture below.

![][3]

### Merge with lab.com AD domain

21. To merge machine with the domain. Right-Click the "My Computer" icon on the desktop and select (you guessed it) "Properties", in the *Settings* window that opens scroll down and click on "Rename this PC (advanced)" option. This will open a window called "System Properties". It should be opened to the "Computer Name" tab. Select the "Change..." button to start the merge process. This will open a sub-window called "Computer Name/Domain Changes.
<br>
22. In this window the computer name can be changed, and the PC is joined to the domain. Select the "Domain:" option and enter "lab.com" into the field under it. (Note: Not all versions of Windows can be joined to a domain. To join to a domain the version must be Windows Professional or higher). Press the OK and when prompted for AD credentials (username and PW) use "a-ducky@lab.com".
\
This account was set with Domain Admin rights. One of those rights is the right to add machines to the domain. The "normal" accounts you created will not have the rights to join machines to AD. We would not want just account to add machines to the domain! Click OK through all the different windows and sub-windows. There will be a prompt to reboot to complete the merge process. (There's always areboot, sigh).
<br>

23. After the reboot Win10 has been added to the lab.com domain. It is now possible to login using the username and PW of the users you created earlier (smiths or jonesb). Once the PC finishes the boot process (you'll know by seeing the Time/Date on the screen). Click once with the mouse to get a login prompt.
\
Select "Other user" in the bottom left corner to select a different user. Notice how the "Sign in to: LAB" is already setup. In the username field put in jonesb and fill in the password.
\
The first time a user account logs into a Windows PC a "user profile" is created. This means all the folders and environment that a user needs is created. This process can be lengthy but is only needed the first time a user logs into a machine.
\
Previous to the AD merge you couldn't login with any account but the local "Administrator" account. Since the workstation is now "merged" or "joined" with AD it is possible to centrally manage the user accounts from the MMC! Part of the merge process also created an account for the machine in AD as well (Check the MMC under Computer OU). In this way is possible to manage the user and machine as separate objects in AD.
\
Part of the merge process puts the Domain Admins group in the local machine Admins group. In this way Domain Admins have admin rights on all the workstations. Note: When using AD there is NO presumption of privacy. Meaning that the admins can, if they choose to read/write/see everything on the PC. It is not possible to hide the Admins with AD. These PCs are NOT personal machines with the presumption or privacy.
<br>

24. Due to the personalization that is done to make the system easier to use the first time the start menu is used a dialog will popup asking for Settings for Open-Shell. Click on the OK button to continue.

## Sharing is good... oversharing is bad

"Shares" are folders on a machine that have been setup to allow other users, over the networks, access the files/folder inside them. Accessing files over the network is a combination of file system rights (NTFS rights) and network access (aka Sharing Rights). Both sets of rights have to allow access, or the user is blocked. Think of is as multiple coffee filters before the file can be accessed. (Crazy, but there is an easy way to sort it out...only use the NTFS permissions to manage access).

25. On Win2019 open "This PC" icon and open the root of C: drive, create a new folder using the called "data". Open the folder and create a text file called `ducky is great.txt`.

### Sharing Rights Configuration

26. Right-Click on the "data" folder and select properties on the menu. Along the top select the "Sharing" tab. Click on the "Advanced Sharing" button and check the box "Share this folder". The remaining options in the window will un-gray at this point. These are the SMB sharing options.
<br>

27. Click on the "Permissions" button, this will show the default permissions for objects in the "data" folder. Note that a special group called the "Everyone" group is already here with read access.Highlight the Everyone group and check "Full Control" box to add those network sharing rights.

### NTFS File System Rights Configuration

28. Open the properties of the "data" folder and to the "Security" tab. The domain group "Users (LAB\\Users)" is already listed and has several rights by default (like read). This gives read access by DEFAULT to all domain users. There are ways to remove this access, but it's beyond the scope of this lab at this time.
<br>

29. Click on the "Edit..." button and add the two groups, Marketing and Sales. Highlight each group and give them "Full Control". This will allow members of those groups full rights (read/write etc.) to files/folders in the "data" directory. Press OK and/or Close to finish out the "data" folder properties and save the choices.
\
Because we set the rights using the AD group not to the specific user it will be MUCH easier to add more users to Sales and make sure they all have the same access. Otherwise the admin would have to go to each share/object and set up each user individually (big time waste and might screw it up!).

### Accessing Shares

30. From Win10 workstation open the Run dialog (Start Menu and Run... option) and type in: `\\its-win2019-1.lab.com` and press OK. This will open a window showing ALL visible shares on the remote server.
\
Note: "\\\\" has a specific pronunciation in IT. Backslashes \\ are called a "whack". So, two of them would be pronounced whack-whack. Pronounced all together as:
"whack whack its dash win2019 dash one dot lab dot com".
<br>

31. Double click on the "data" share to open it. Open `ducky is great.txt` file and add some text to it.

### Map a Drive Letter

32. Mapping a drive letter to a network share is a common occurrence for local network shares. On Win10 from the run dialog open: `\\its-win2019-1.lab.com` as done previously.
<br>

33. Right-Click on the "data" share and select "Map Network Drive...".  Select the drive letter L from the pull down. Make sure the check box for "Reconnect at sign-in" is checked. This will make the drive letter persistent through reboots/re-logins.
<br>

34. Open "This PC" by finding it on the Start Menu. Scroll down the list of items to the L-Drive and look at the documentation that goes with it. Telling the user where on the network the drive letter connects.
<br>

35. AD GPOs are in the next lab. They are the BEST part of Active  Directory!

[Tech Nugget - N0.6 - Basic Diag Tools - NIC Config]: https://youtu.be/Vu1CeJIXMnk 
[1]: images/pic1.png
[2]: images/pic2.png
[3]: images/pic3.png
