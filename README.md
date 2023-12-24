<p align="center">

![Screen Shot 2023-12-24 at 3 35 37 AM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/68661246-4eef-42d8-864c-adad3b33c7a5)

</p>

# Network File Shares And Permissions

This demonstration will walk you through the process of setting up Network File Shares and Permissions using Windows Server and Active Directory. 

>**Note***
The following uses material in my previous demonstration: ["Configuring On-Premises Active Directory Within Azure VM's"](https://github.com/Emq17/Configuring-On-premises-Active-Directory-within-Azure-VMs)

<h2>Overview</h2>

- Talk about file shares (what, when, where, why, and how)
- Create and test some file shares
- Talk about Security Groups (Windows and Active Directory)
- Create and test some Security Groups
- Clean up resources

<h2>Environments and Technologies</h2>

- Microsoft Azure Virtual Machines
- Microsoft Remote Desktop
- Active Directory Domain Services

<h2>Operating Systems</h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>Prerequisites</h2>

- Windows Server installed and configured
- Active Directory Domain 
- Two Virtual Client Machines connected to the Domain
- Administrative access to Server and Clients

<h2>Creating Files Shares with Various Permissions</h2>

- Return to DC-1 as **jane_admin**
- On the Top Right Header, Select `Tools`
- Select `Active Directory Users and Computers`
- Expand `mydomain.com`
- Select `_EMPLOYEES`
- Copy any User from the list (EX. i'll be using xen.dec)

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/2f00a0d7-9722-4868-8243-2a2c57e66333)

- Log into Client-01 with your User

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/9608783a-811a-426f-9f78-54c196d8f77d)

- Return to DC-1
- Open `File Explorer`
- Select `This PC`
- Select `Windows (C;)`
- Right Click inside the container and Select `New`
- Select `Folder` and Create Four Folders
    - One named **read-access**
    - One named **write-access**
    - One named **no-access**
    - One named **accounting**

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/57234b1e-b599-462f-acae-f6177ff814d1)

- Right Click **read-access**
- Select `Properties`
- Select `Sharing`
- Select `Share`

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/8996c525-0faa-4082-b978-1ee62ec29c3d)

- Type **domain users**
- Select `Add`
- Set `Permission Level` at **Read**
- Select `Share`

  ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/83cf8f34-e201-4956-a648-23c265a0e5c5)

- Select `Done`
- Select `Close`
- Repeat these steps again for the next two Folders (Write Access and No Access)
    - For Write Access, Set `Permission Level` to **Read/Write**

      ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/fb792019-936c-4f18-8f05-c33827f7ac8d)

    - For No Access, Type **Domain Admins** and Select `Add`
    - Set `Permission Level` to **Read/Write**

      ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/7886c96c-91c1-43a1-8a54-5c2999223539)

- Skip Accounting Folder for now

<h2>Attempting to Access File Shares as a Normal User</h2>

- Return to Client-01
- Open `File Explorer`
- Type **\\\dc-1** into Search Bar

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/3f655df3-79e1-4f4f-b5ab-78201aac3fed)

- Now you have access to every folder made before

![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/de2b2cfe-5aaa-4b9e-945a-c55c3ed32cbb)

- Select `no-access`
- You will see it fails access

    ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/1d955e24-d745-40ba-a490-b1a1c1dbb2f9)

- Select `read-access`
- Right Click and Select `New`
- Select `Rich Text Document` (RTF)
- You will see it fails to create the file

    ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/fe889214-4b73-4ca0-b320-6e54239f6342)

- Select `write-access`
- Right Click and Select `New`
- Select `Rich Text Document` (RTF)
- Type **Hello World**
- Hit `Enter` Button

    ![image](https://github.com/CarlosAlvarado0718/Network-F-P/assets/140138198/8cd14fee-8a30-49f8-a3c1-e2afd2cac759)

<h2>Creating a Security Group</h2>

- Go to DC-1
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
