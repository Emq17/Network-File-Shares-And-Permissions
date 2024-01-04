<p align="center">
<img src="https://i.imgur.com/Vh87N7q.png" alt="Logo"/>
</p>

<h1 align="center">Network File Shares And Permissions</h1>

This demonstration will walk you through the process of setting up Network File Shares and Permissions using Windows Server and Active Directory. We're going to be sharing out files and folders essentially over the network and allowing different people in groups different levels of access. 

>**Note***
The following uses material in my previous demonstration: ["Configuring On-Premises Active Directory Within Azure VM's"](https://github.com/Emq17/Configuring-On-premises-Active-Directory-within-Azure-VMs)

<h2>Overview (What We Will Be Doing)</h2>

![Screen Shot 2023-12-25 at 8 37 17 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/cb3a2dd1-4067-4bcb-809f-5a1f833d635f)

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

- Log into Client-01 with your user of choice for your second connection & while that is booting up...

![Screen Shot 2023-12-25 at 6 16 02 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/3b0bc7ed-d035-4917-8365-ad5a52659c98)

- Return back to DC-1 (jane_admin) on your other remote connection
- Open `File Explorer`
- Select `This PC`
- Select `Windows (C;)`
- Right Click inside the container and Select `New`
- Select `Folder` and create four new folders
    - read-access
    - write-access
    - no-access
    - accounting

![Screen Shot 2023-12-25 at 6 38 49 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/9a8fbe0a-12e0-46fd-a55d-3a71bebf514f)


- Right Click "read-access"
- Select `Properties` on the drop down menu
- Select `Sharing` tab
- Select `Share`

![Screen Shot 2023-12-25 at 6 43 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/22b04351-d275-4097-a16c-90848fa08bd5)

- Type "Domain Users"
- Select `Add`
- Set `Permission Level` to "Read"
- Select `Share`

![Screen Shot 2023-12-25 at 6 45 53 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/669473d1-7099-4114-9529-bec2a22cff81)

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

Because we gave the "read-access" folder access to Domain Users (which is what the users in `_EMPLOYEES` are)  with "Read" only permissions, we should be able to open it but not create a new file

- Select `read-access`
- Right Click and Select `New`
- Select `Rich Text Document` (RTF)
- You will see it fails to create the file

![Screen Shot 2023-12-25 at 7 26 14 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/203e31b3-ad4d-44e7-ac92-37d43427067e)

![Screen Shot 2023-12-25 at 7 45 54 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/a66434c5-1bb0-4a6a-a839-2838862bd1cc)

Because we gave the "write-access" folder access to "Read/Write" permissions, we should be able to open it and also create a new file within it with no problems

- Select `write-access`
- Right click and select `New`
- Select `Text Document`
- Type in anything
- Hit `Save`

![Screen Shot 2023-12-25 at 8 01 06 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/07f19128-f878-43aa-ac36-bd697c22b0f2)

- As you can see the new file is successfully created

![Screen Shot 2023-12-25 at 8 08 25 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/a8fabdc0-05e7-461d-b1b6-d990571e1f68)

- Now just for fun, lets go back to DC-1 and create a new Text Document file in the "read-access" folder
- I named mine "You can only read me..."

![Screen Shot 2023-12-25 at 8 19 15 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/2ea34707-d0dd-4888-beab-05fc0b4548de)

- Go back to Client-1's connection and observe the new folder inside of "read-access" and that you're able to open it.

![Screen Shot 2023-12-25 at 8 21 25 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/ccc3aa01-ee31-4549-b0c9-c7cb25ed4407)

- Even though you can open the file and read it, remember that you are still not able to create a file for yourself due to having "read" only permissions

![Screen Shot 2023-12-25 at 9 39 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/600cb0a0-9bff-45d1-b4c6-0f1f180792d3)

![Screen Shot 2023-12-25 at 8 20 40 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/62f92dac-3446-4e5f-a833-2d8575e04766)

- This demonstration should provide you a fundamental understanding of how file permissions now operate

<h2>Creating a Security Group</h2>

- Go back to DC-1's Active Directory
- Right Click `mydomain.com`
- Select `New`
- Select `Organizational Unit`
- Set `Name` to "_SECURITY_GROUPS" just to keep things organized

![Screen Shot 2023-12-25 at 8 39 23 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/3ecd6559-4f24-480c-a2a3-5d593aced25f)

![Screen Shot 2023-12-25 at 8 39 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/0ce1e395-3514-482e-989e-c8f5c4b709b9)

- Refresh `mydomain.com`

![Screen Shot 2023-12-25 at 8 40 30 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/5b055bb7-6bdb-413d-85ab-917349390ab4)

- Click `_SECURITY_GROUPS`
- Right click inside folder and choose `New` --> `Group`

![Screen Shot 2023-12-25 at 8 42 27 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/b7b0940e-4c5a-4e83-ad75-7fc552972812)

- Set `Group name` to "ACCOUNTANTS"
- `Group type` as "Security"
- Click `OK`

![Screen Shot 2023-12-25 at 8 44 10 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/de544c2f-af0c-4992-b01f-c480945a4b52)

![Screen Shot 2023-12-25 at 8 45 22 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/33b17671-4ed3-4be6-ad05-678e5d3bb363)

- Return to `File Explorer`
- Select `accounting` folder
- Right click and select `Properties`

![Screen Shot 2023-12-25 at 8 47 37 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/7543f153-abf7-42d1-b9c0-7e9884931eba)

- Select `Share`

![Screen Shot 2023-12-25 at 8 48 59 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/15b5383a-e4b1-4ca3-8541-3b853bcf707d)

- Type "ACCOUNTANTS"
- Select `Add`
- Set `Permission Level` to "Read/Write"
- Select `Share`

![Screen Shot 2023-12-25 at 8 52 05 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/dbfb18aa-b983-4fb3-b261-6b735c0bd0de)

<h2>Changing User's Permissions</h2>

- Now return back to Client-01
- Go to your File Explorer
- Type in "\\dc-1 at the top
- Attempt to access the `accounting` folder
- Observe that the user does not have permission to access the folder (due to the fact that "dox.kena" is not in that security group)

![Screen Shot 2023-12-25 at 8 54 45 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/f9e0b583-306d-4972-aa71-c49b827effa3)

In order to give ourselves access to this, we need to add whoever our user is (dox.kena) and make this person a member of the accountants security group

- Return back to DC-1's `_SECURITY_GROUPS`
- Double click on `ACCOUNTANTS`

![Screen Shot 2023-12-25 at 9 02 35 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/43c875a1-4f0a-451a-ad65-a89b891022a9)

- Click the `Members` tab up top
- Then select `Add`

![Screen Shot 2023-12-25 at 9 06 04 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/608102ec-e9f2-46ce-9837-78c990552e97)

- Type in user's name (dox.kena)
- Select `Check Names`
- Select `OK`, `Apply`, then `OK`

![Screen Shot 2023-12-25 at 9 07 43 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/2c95b393-aeb7-4ece-8d67-c90d8c2f81fe)

- As you can see if we go back to Client-1 we still do not have permissions for it
- All we have to do now is log out of our remote desktop connection to restart everything
- Usually your permissions get assigned to your users at log in time

![Screen Shot 2023-12-25 at 9 10 27 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/95b1ab7b-1433-4dcb-b785-3ebbc40b1a10)

- After logging off, log back in to your user again for Client-1's remote desktop connection

![Screen Shot 2023-12-25 at 9 14 53 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/9f35a543-9422-415e-a693-f233c15b91d7)

- Go to the accounting folder
- Instead of opening up file explorer, a fancier way of getting there quicker is (if you're on mac) to hit `Command` + `R` or `Windows` + `R` (if you're running windows) 
- Then type in "\\dc-1"

![Screen Shot 2023-12-25 at 9 17 12 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/1f2805e4-577c-4572-bab3-f17035f62408)

- Select the `accounting` folder

![Screen Shot 2023-12-25 at 9 20 35 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/ff6aa745-7d78-4104-9061-62dd8fe9a1d1)

- Now that the necessary permissions have been applied, we can see that we have gained access and are also able to read and write within it

![Screen Shot 2023-12-25 at 9 20 56 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/cb6965b2-c420-43be-a73c-61227ad57513)

![Screen Shot 2023-12-25 at 9 54 42 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/b1c11d6e-2477-459a-b613-f357c3c910cd)

![Screen Shot 2023-12-25 at 9 56 49 PM](https://github.com/Emq17/Network-File-Shares-And-Permissions/assets/147126755/5ac2fef4-6e6a-4c74-9395-dd4860cf40eb)

- If you've made it this far, thank you for sticking around to the end of this walkthrough
- I hope I was able to demonstrate a thorough understanding of how overall Network File Shares and Permissions work
