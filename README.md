# Active Directory Home Lab
This repository showcases a walkthrough on creating and configuring an Active Directory lab environment within VirtualBox.
### Goals for this lab
- Setup a virtual enviornment with Windows Server and Windows 11 client devices
- Install and configure Active Directory
- Add users and computers to the domain
- Create and configure Groups with GPOs and organizing them with OUs
- Remoting into a machine with RDP
- Utilizing a ticketing system for support requests
- Create startup scripts for specific groups
 
# Required Downloads
1. Install [Oracle VirtualBox](https://www.virtualbox.org/)
2. Download [Windows 11 ISO](https://www.microsoft.com/en-us/software-download/windows11)
3. Download [Windows Server 2022 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022)
# Windows 2022 Setup 
- Once VirtualBox is installed, create a new machine called "Server 2022"
- Configure system specs as needed
<img width="1075" height="775" alt="image" src="https://github.com/user-attachments/assets/d5f6843c-df09-4ae2-aff6-672ddc5ffdac" />

+ Start the machine then boot with Windows 2022 ISO
+ Continue through the installation process and install the ***GUI version*** of Windows Server
+ ### **If successful, your VM window should show something similar to this.**
<img width="1024" height="768" alt="VirtualBox_Server 2022_12_09_2025_22_09_56" src="https://github.com/user-attachments/assets/a125f62d-f5ba-4a2b-9692-659be589dab4" />

# Configuring Domain Services
- First rename the computer to someting more readable by going to "Local Server" and clicking on the computer name, then go to "Change..."
- Rename it to whatever you want, I renamed it to "Main-DC" (Main Domain Controller) because it's the only server im going to be using for this lab

<img width="1024" height="768" alt="VirtualBox_Server 2022_12_09_2025_22_26_21" src="https://github.com/user-attachments/assets/2179c5c2-743a-4b10-aeb0-049b56720d2f" />

- Once you apply the change, it should prompt a restart

## Now Lets Install Active Directory Domain Services
- Go to "Manage" -> "Add Roles and Features" and click next until you get to "Server Roles"
- Select "Active Directory Domain Services" and Add features
<img width="1024" height="768" alt="VirtualBox_Server 2022_12_09_2025_22_33_47" src="https://github.com/user-attachments/assets/42ff1d76-2581-43c3-83e6-1c4f68ff299c" />

- Click next until prompted to install
- ### After installation, you'll notice a flag with a warning symbol
- ### All that means is that you need to "Promote this server to a domain controller"
<img width="1024" height="768" alt="VirtualBox_Server 2022_12_09_2025_22_40_09" src="https://github.com/user-attachments/assets/de8958dc-3b99-456e-b045-16c76cbd7de2" />

- Simply click the link and "Add a new forest", because this a new domain
- And name it what ever you want with any TLD
- I named it "cubelab.local" 
<img width="1024" height="768" alt="VirtualBox_Server 2022_12_09_2025_22_47_15" src="https://github.com/user-attachments/assets/6ef22526-8fba-4f69-9980-eb1a0c50215e" />

- Click next, then type the DSRM (Directory Services Restore Mode) password
- Keep clicking next until prompted to install, your machine should automatically restart
### Active Directory Domain Services is now installed!

# Installing Guest Additions
- ***Before we do anything else, you should install Guest additions***
- This is installed inside the VM to optimize the guest operating system
- Simply go to "Devices" -> "Insert Guest Additions CD Image" at the top of the VM window
- Then within the VM, select the Guest Additions drive and install the "amd-64" version
<img width="1024" height="768" alt="VirtualBox_Server 2022_13_09_2025_13_50_38" src="https://github.com/user-attachments/assets/7411fc2a-ef6c-41e6-ab30-5027fb754404" />

- Once you install it, it should prompt a restart\
**Guest Additions is now installed!**
> You are free to eject it after installation

# Creating Users within Active Directory
- **Before we join other machines to the domain, we are going to create some users**
- Within Server Manager, go to "Tools" -> "Active Directory Users and Computers"
- Then right click on Ssers and go to "New" -> "User"
- Create a user (name it whatever you want)
<img width="1024" height="768" alt="VirtualBox_Server 2022_13_09_2025_14_24_51" src="https://github.com/user-attachments/assets/b1c40059-a091-40cf-85ad-bbc70d92ab15" />

- Click next, uncheck "User must change password.." and type in a password (that you can remember)
- Click next again and then finish
- **You will now see the new user you created within the "Users" group**
- Now check their account with the net user command
  - net user {USER} /domain
<img width="1024" height="768" alt="VirtualBox_Server 2022_13_09_2025_14_50_02" src="https://github.com/user-attachments/assets/ab70d840-82c7-41bc-b933-f29f101783f3" />

- ### Now, create two more accounts using the steps above

# Joining a Machine to a Domain
- First, install the two Windows 11 machines needed, follow the steps used to install "Server 2022"
- ***During the installation process, make sure to install the PRO VERSION of Windows 11***
  - This version allows us to add the computers to the domain
<img width="623" height="112" alt="image" src="https://github.com/user-attachments/assets/47e9a100-b630-4683-a1c5-ee7960ea979f" />

> M-1 stands for "Machine 1"
- Once you get to the setup screen go to "Devices" -> "Network" -> "Network Settings" and switch it to "Host-only Adapter"
<img width="546" height="126" alt="image" src="https://github.com/user-attachments/assets/55230f39-ca57-436e-9c93-c434bbc2e7b6" />

> We do this so we can skip the long setup process (and will be used for IP addressing later)\
> If that doesn't work for some reason there is a documented solution [here](https://techcommunity.microsoft.com/discussions/windows11/how-to-install-windows-11-without-internet-or-network-on-a-pc/4183117)

- All you need to do is set a name and skip the password
-  Once you're on the desktop, rename both machines and restart when prompted
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_13_09_2025_19_43_40" src="https://github.com/user-attachments/assets/00b445cb-41fd-46ee-a2f5-6a7335b327a8" />

> I renamed my first client VM to M-1
- Now that the two client machines are up and running, go ahead and install guest additions for both
- **Once that's done, go back to Server 2022 so we can begin adding machines to the domain**

## Lab Network Configuration
- ### In Server 2022 set it to "Host-only Adapter"
- Then, within the VM Go to "Control Panel" -> "Network and Internet" -> "View network status and tasks" -> "Change adapter settings"
- Then go to "Ethernet" -> "Internet Protocol Version 4 (TCP/IPv4)" 
- Here we will configure IP addressing
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_20_56_53" src="https://github.com/user-attachments/assets/b8786a0b-4096-4600-899c-17c5939caf7d" />


> I set the internal network ID as 192.168.1.0/24\
> I set the perferred DNS and gateway to the Main-DC IPv4 address, because it will "act" as both\
> We are only creating an internal network, we will not be connecting to the internet

- Now configure IP addressing for the client machines using the same method
  - Set a different address within the IP scope (not using X.X.X.0 or X.X.X.255)
  - Keep the same DNS address
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_13_09_2025_21_02_48" src="https://github.com/user-attachments/assets/68056f2c-aaf6-41e9-86e2-0302aaa7787b" />


- After both machines are configured run the ping command to test connectivity
  - ping cubelab.local
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_13_09_2025_21_02_17" src="https://github.com/user-attachments/assets/082ade29-337c-4654-8938-4645cefb5a88" />

### FINALY, we can add the machines to the domain
- Go to your client machines "System Properties" -> "Change..."
- And type in the domain name
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_13_09_2025_21_09_25" src="https://github.com/user-attachments/assets/1bee9afa-7c58-4e20-aaca-ae1c43195786" />

- A popup will apear requiring the DC's username and password login, enter that and your machine will restart
- **Do that for your other machine and now you are set!**
- Now check "Computers" under ADUC (Active Directory Users and Computers) to make sure the machines are there
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_21_17_43" src="https://github.com/user-attachments/assets/008c3170-6871-4223-9806-6bc1a528985e" />

### Remember those users we created earlier? 
- Go back to ADUC, right click one of those users and select "Reset Password..."
- Set a password and deselect "User must change password at next logon"
> This is only temporary, we are doing this to make sure you're able to login
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_21_25_31" src="https://github.com/user-attachments/assets/a2616b56-4cf7-4793-8765-354bae933ce2" />
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_13_09_2025_21_29_27" src="https://github.com/user-attachments/assets/faf3e561-d4f7-4cda-bf00-7cec37f0c35d" />


### **Succesfuly logged in? Nice! Now we can get to adding these users to groups and configuring GPOs**
# Configuring GPOs and Creating OUs
> Before you start, enable "Advanced features" within ADUC from "View" (this will be helpful later) 
- In theory we could manually create users and add them to groups, but there are **more efficient** ways of doing that
- The first thing we are going to do is create 3 OUs, we do this by right clicking the domain within ADUC and selecting "New" -> "Organizational Unit"  
  - IT
  - HR
  - Accounting
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_22_29_20" src="https://github.com/user-attachments/assets/b3822c01-75e2-4a5e-bbd4-1f70ba4ebef2" />
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_22_45_31" src="https://github.com/user-attachments/assets/acac9b05-27d0-4f22-abde-1b57c64bf3b6" />

- Now with those three users we created earlier, move each one to a different OU
- You should have one user in each OU
## Before creating GPOs for each OU let's configure "Default Domain Policy" so that all of our OUs can inherit some base policies
- Within Server Manager go to "Tools" -> "Group Policy Management"
