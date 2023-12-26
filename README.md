<p align="center">
<img src="https://i.imgur.com/Vh87N7q.png" alt="Logo"/>
</p>

<h1 align="center">Network File Shares And Permissions</h1>

This demonstration will walk you through the process of setting up Network File Shares and Permissions using Windows Server and Active Directory. We're going to be sharing out files and folders essentially over the network and allowing different people in groups different levels of access. 

>**Note***
The following uses material in my previous demonstration: ["Configuring On-Premises Active Directory Within Azure VM's"](https://github.com/Emq17/Configuring-On-premises-Active-Directory-within-Azure-VMs)

<h2>Overview (What We Will Be Doing)</h2>

![Screen Shot 2023-12-25 at 5 43 08 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/fe2745ed-7111-4188-826b-121745a4a20e)

- Talk about file shares (what, when, where, why, and how)
- Create and test some file shares
- Talk about Security Groups (Windows and Active Directory)
- Create and test some Security Groups
- Clean up resources

<h2>Environments and Technologies</h2>

- Microsoft Azure Virtual Machines
- Microsoft Remote Desktop
- Active Directory Domain Services
- Windows Server 2022
- Windows 10 (22H2)

<h2>Prerequisites</h2>

- Windows Server installed and configured
- Active Directory Domain
- Two Virtual Client Machines connected to the Domain
- Administrative access to Server and Clients
  
Essentially the same set up from my earlier walkthrough: <br>
https://github.com/Emq17/Configuring-On-premises-Active-Directory-within-Azure-VMs <br>

1. Active Directory running in Azure on a virutal machine (DC-1) <br>
2. A Client machine running in Azure on a virtual machine (Client-1) and joined to the domain

<h2>Creating Files Shares with Various Permissions</h2>

What are file shares? - File shares are basically folders on your desktop but instead of just being on your desktop its shared out to the rest of your network or organization. 

- Sign into DC-1 as your domain admin account (jane_admin)
- On the Top Right Header, Select `Tools`
- Select `Active Directory Users and Computers`

![Screen Shot 2023-12-25 at 6 11 33 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/99b50d18-9af2-4a6a-99b0-37a9285ac7ce)

- Expand `mydomain.com`
- Select `_EMPLOYEES`
- Copy any User from the list (I will be using "dox.kena")
  - Remember that this list was produced by the PowerShell Script from the previous walkthrough using the Password: Password1

![Screen Shot 2023-12-25 at 6 13 49 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/1d9167ba-9037-4f9f-bf68-3e08a04c8db5)

- Log into Client-01 with your user of choice

![Screen Shot 2023-12-25 at 6 16 02 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/3b0bc7ed-d035-4917-8365-ad5a52659c98)

- Return to DC-1
- Open `File Explorer`
- Select `This PC`
- Select `Windows (C;)`
- Right Click inside the container and Select `New`
- Select `Folder` and Create Four Folders
    - read-access
    - write-access
    - no-access
    - accounting

![Screen Shot 2023-12-25 at 6 38 49 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/9a8fbe0a-12e0-46fd-a55d-3a71bebf514f)


- Right Click "read-access"
- Select `Properties` on drop down menu
- Select `Sharing` tab
- Select `Share`

![Screen Shot 2023-12-25 at 6 43 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/22b04351-d275-4097-a16c-90848fa08bd5)

- Type "Domain Users"
- Select `Add`
- Set `Permission Level` to "Read"
- Select `Share`

![Screen Shot 2023-12-25 at 6 45 53 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/669473d1-7099-4114-9529-bec2a22cff81)

- Select `Done`
- Select `Close`
- Repeat these steps again for the next folder "Write Access"
    - Set `Permission Level` to "Read/Write"

![Screen Shot 2023-12-25 at 6 47 57 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/3fc2cf90-d0a6-4f2f-af1a-80f6106a36c6)

- Do the same for "no-access" but
- Type "Domain Admins" and Select `Add`
- Set `Permission Level` to "Read/Write"

![Screen Shot 2023-12-25 at 7 18 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/d5a7feb9-6a2d-4f45-96f7-71b5d66a4368)

- Skip Accounting Folder for now

<h2>Attempting to Access File Shares as a Normal User</h2>

- Return to Client-1's remote desktop connection
- Open `File Explorer`
- Type "\\\dc-1" into Search Bar up top

![Screen Shot 2023-12-25 at 7 43 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/4d6a14e9-c1d3-4943-ad30-b7ed2d884c94)

- Now you have access to every folder made before

![Screen Shot 2023-12-25 at 7 33 02 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/9abf0fd5-07a3-49e2-a044-e65457699079)

Because we gave the "no-access" folder access to only Domain Admins, we should not be able to open this file

- Select `no-access`
- You will see it fails

![Screen Shot 2023-12-25 at 7 37 20 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/da886c99-c79c-495b-8b4d-4df0ca4a0bb8)

Because we gave the "read-access" folder access to "Read" only permissions, we shouldn't be able to create a file in here

- Select `read-access`
- Right Click and Select `New`
- Select `Rich Text Document` (RTF)
- You will see it fails to create the file

![Screen Shot 2023-12-25 at 7 26 14 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/203e31b3-ad4d-44e7-ac92-37d43427067e)

![Screen Shot 2023-12-25 at 7 45 54 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/a66434c5-1bb0-4a6a-a839-2838862bd1cc)

Because we gave the "write-access" folder access to "Read/Write" permissions, we should actually be able to create a file in here actually

- Select `write-access`
- Right click and select `New`
- Select `Text Document`
- Type in anything
- Hit `Save` Button 

![Screen Shot 2023-12-25 at 7 50 08 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/f0bdb5c1-35b2-42c6-aa19-350c2a4014e2)

![Screen Shot 2023-12-25 at 8 01 06 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/07f19128-f878-43aa-ac36-bd697c22b0f2)

- As you can see the new file is successfully created

![Screen Shot 2023-12-25 at 8 08 25 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/a8fabdc0-05e7-461d-b1b6-d990571e1f68)

- If we go back to DC-1 and create a new Text Document file in the "read-access" folder

![Screen Shot 2023-12-25 at 8 19 15 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/2ea34707-d0dd-4888-beab-05fc0b4548de)

- You can see how on Client-1 the new folder inside of "read-access" was created and you're able to open it but still not able to create a file for yourself

![Screen Shot 2023-12-25 at 8 21 25 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/ccc3aa01-ee31-4549-b0c9-c7cb25ed4407)

![Screen Shot 2023-12-25 at 8 20 40 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/62f92dac-3446-4e5f-a833-2d8575e04766)

- This should provide you a solid understanding of how file permissions function.

<h2>Creating a Security Group</h2>

- Go back to DC-1
- On the Top Right Header, Select `Tools`
- Select `Active Directory Users and Computers`
- Expand `mydomain.com`
- Right Click `mydomain.com`
- Select `New`
- Select `Organizational Unit`
- Set `Name` to **_SECURITY_GROUPS**

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/dd46a511-38b5-4984-ae0f-d1181d45bbd2)


- Right Click Security Groups
- Select `Group`
- Set 'Group name` to **ACCOUNTANTS**
- Click `OK`

>**Note***
>_By Double-Clicking the Group you can add users by selecting the `Members` tab then selecting `Add`_

 ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/a501897e-5d69-4382-ae27-d4ea1b488056)

- Return to `File Explorer`
- Select `accounting` folder
- Select `Properties`
- Select `Share`
- Type **ACCOUNTANTS**
- Select `Add`
- Set `Permission Level` to **Read/Write**
- Select `Share`

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/2f28d281-c994-4cfd-a13b-3940c978081a)

<h2>Changing User's Permissions</h2>

- Return to Client-01
- Attempt to access the `accounting` folder
- Observe that the user does not have permission to access the folder

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/cefdd339-cf8d-4b04-a1f3-233bf17ae16c)

- Return to DC-1
- Select `Properties` of the `ACCOUNTANTS` group
- Click `Members`
- Select `Add`

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/4cbbc3f5-dc5a-4bbd-b2c5-07327846f7b7)

- Type in <your user> (EX. xen.dec)
- Select `Check Names`
- Select `OK`

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/99781a0d-5c93-4be3-8471-4d7fbae93bbb)

- Select `Apply`
- Select `OK`
- Restart Client-01
- Login to <your user> (EX: xen.dec)
- Go to the accounting folder
- Select the `accounting` folder
- You are now able to read and write in the Accounting Folder!!

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/97ba878d-a714-4a59-86fd-6c1276c16f18)

<h1>ALL DONE!!! CONGRATS!!!</h1>
