# Active Directory Home Lab
This repository showcases a walkthrough on creating and configuring an Active Directory lab environment within VirtualBox.
### Goals for this lab
- Setup a virtual enviornment with Windows Server and Windows 11 client devices
- Install and configure Active Directory
- Add users and computers to the domain
- Create OUs and configure Security groups, GPOs and manual home folder mapping
- Drive mapping automation using GPOs
 
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
> I set the perferred DNS and gateway to the Main-DC IPv4 address, because it will act as both\
> We are only creating an internal network, we will not be connecting to the internet

- Now configure IP addressing for the client machines using the same method
  - Set a different address within the IP scope (not using X.X.X.0,1,2,255)
  - Keep the same DNS address
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_13_09_2025_21_02_48" src="https://github.com/user-attachments/assets/68056f2c-aaf6-41e9-86e2-0302aaa7787b" />


- After both machines are configured run the ping command to test connectivity
  - ping cubelab.local
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_13_09_2025_21_02_17" src="https://github.com/user-attachments/assets/082ade29-337c-4654-8938-4645cefb5a88" />

### FINALY, we can add the machines to the domain
- Go to your client machines "System Properties" -> "Change..."
- And type in the domain name
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_13_09_2025_21_09_25" src="https://github.com/user-attachments/assets/1bee9afa-7c58-4e20-aaca-ae1c43195786" />

- A popup will appear requiring the DC's username and password login, enter that and your machine will restart
- **Do that for your other machine and now you are set!**
- Now check "Computers" under ADUC (Active Directory Users and Computers) to make sure the machines are there
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_21_17_43" src="https://github.com/user-attachments/assets/008c3170-6871-4223-9806-6bc1a528985e" />

> If you want to, you can disable the local account within "Computer management" using DC Admin privileges
> <img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_13_00_56" src="https://github.com/user-attachments/assets/ee76adf5-676b-46f2-a8e0-c6c3eda828a2" />


### Remember those users we created earlier? 
- Go back to ADUC, right click one of those users and select "Reset Password..."
- Set a password and deselect "User must change password at next logon"
> This is only temporary, we are doing this to make sure you're able to login
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_21_25_31" src="https://github.com/user-attachments/assets/a2616b56-4cf7-4793-8765-354bae933ce2" />
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_13_09_2025_21_29_27" src="https://github.com/user-attachments/assets/faf3e561-d4f7-4cda-bf00-7cec37f0c35d" />

- Once that's complete, go to the three users you created and reset their passwords so that next login they have to reset their password

### **Succesfuly logged in? Nice! Now we can get to adding these users to groups and configuring GPOs**
# Creating OUs and Configuring GPOs
> Before you start, enable "Advanced features" within ADUC from "View" (this will be helpful later) 
- The first thing we are going to do is create 3 OUs, we do this by right clicking the domain within ADUC and selecting "New" -> "Organizational Unit"  
  - IT
  - HR
  - Accounting
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_22_29_20" src="https://github.com/user-attachments/assets/b3822c01-75e2-4a5e-bbd4-1f70ba4ebef2" />
<img width="1053" height="791" alt="VirtualBox_Server 2022_13_09_2025_22_45_31" src="https://github.com/user-attachments/assets/acac9b05-27d0-4f22-abde-1b57c64bf3b6" />

- Now with those three users we created earlier, move each one to a different OU
- You should have one user in each OU
## Now let's configure "Default Domain Policy" so that all of our OUs can inherit some base policies
- Within Server Manager go to "Tools" -> "Group Policy Management"
- Expand "Forest" -> "Domains" -> "{DOMAIN}" until you see "Default Domain"
- Right click that and select "Edit"
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_12_25_49" src="https://github.com/user-attachments/assets/4176d48d-7c09-48ab-be6c-6c9984e7e7c3" />

- Now in the editor window, expand "Policies" -> "Windows Settings" and left click "Security Settings"
- Now in the right window double click "Account Policies"
- Here we will configure both "Password" and "Account Lockout" policy
- For Password Policy we are going to change Maximum password age to 90 days
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_12_49_06" src="https://github.com/user-attachments/assets/a601a010-8254-4251-ab79-d648bf8b3662" />

- And For Account Lockout policy we will change the duration to 30 minutes
 - The threshold to 5 invalid attemps
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_12_50_45" src="https://github.com/user-attachments/assets/c63ab3ee-23d4-4c49-938c-cdf76d57c45f" />

- Now exit out of the editor, right click "Default Domain" and select "Enforced"
- Now check within Default Domain Policy settings
 - **Did the policy change?**
- If so, you have succesfully configured the group policy!
- Now go back to a machine, login and use the gpupdate command and restart
 - Then log back in and do gpresult /USER {USERNAME} /v, 
 - You can check if an account has applied the the password policy
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_14_09_2025_13_32_11" src="https://github.com/user-attachments/assets/fa3fcdc3-62c5-4357-b1c8-552ec454d2a5" />

## Mapping share drives
- We are first going to create a our shares
- First go back to Server Manager and go to "File and Storage Services" -> "Shares"
- Right click the share window and select "New Share..." - We are going to do quick share
- Once you get here name the drive to "Personal"
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_13_51_28" src="https://github.com/user-attachments/assets/290ac3fe-2a11-45d9-9b61-5a813ef2ce13" />

- Now using that method, create shares for "IT", "Accounting" and "HR"
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_13_54_59" src="https://github.com/user-attachments/assets/8dd45674-6385-4ef5-bd21-e1891f68ad42" />

- To give users permission to a share drive we need to add them to a Security group

## Creating Security groups
- Inside of ADUC, go to Users and create a new security group called "Personal"
- Within personal we are going to set the description to the share file path (\\MAIN-DC\Personal)
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_23_57" src="https://github.com/user-attachments/assets/607944ef-bedb-456f-858f-682725833fe6" />

- And add all of our members we created to the group
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_26_52" src="https://github.com/user-attachments/assets/3daca2e3-7d92-46be-a7bd-50faaeb7b739" />

- Now within the other OUs, create a security group for each one
 - Add the user inside that OU to the group
 - And set the description to the **share** file path
- **Should look something like this**
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_35_08" src="https://github.com/user-attachments/assets/ee1a972b-3b30-43ca-9e5a-ba5a1b3edc88" />

- Now go to the share folders location within Server 2022
 - C:\Shares
- Now right click a share and go to "Properties" -> "Security" tab -> "Advanced"
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_39_44" src="https://github.com/user-attachments/assets/290e6428-0454-4b0d-99d5-6bd46f86cc72" />

- Here we will disable inheritance, remove both Users principals
- And then add the security group that we would want to have permissions
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_41_57" src="https://github.com/user-attachments/assets/d14db454-d2a7-4271-ad02-baa83e6face3" />

> Make sure to check the "Modify box"
- Now apply those permissions to every share we created
- Now we have to actually **share** the drives
 - Go to properties and under "Sharing", go to "Share..." and change the Permission level to "Read/Write" under the specified security group
<img width="1053" height="791" alt="VirtualBox_Server 2022_14_09_2025_14_55_39" src="https://github.com/user-attachments/assets/fbdae16e-7d3f-4a1f-afe7-7758670ee018" />
 
### All folders should now have the correct permissions and are now shared
- Now it's time to verify if it's done correctly
- In each share (except Personal, we will deal with that later) create a uniqe text file with some text
- Then sign into any user you created on a machine
- Go to File explorer and type in share path
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_15_11_59" src="https://github.com/user-attachments/assets/9d5d2d31-1b16-4045-844d-0d3b144cc931" />

- If done correctly you should be able to access the share!
- Now try to access a share that your user isn't given permission for
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_15_13_55" src="https://github.com/user-attachments/assets/41ed07af-b971-4c07-aac1-688cdeb7446c" />

- ### If given this error message, you have successfuly set file permissions and shares!!!
- Now you can right click "This PC" go to "Map Network Drive" and map your network drive
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_15_16_59" src="https://github.com/user-attachments/assets/c3530dc2-b45b-4e2c-8856-5945c8d4c2de" />

- It should now show up under "This PC"
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_15_17_34" src="https://github.com/user-attachments/assets/3ab4f96e-95a0-42ce-a4bf-978e79f594f7" />

### Personal Shares
- We are setting the home folder, to our share drive
- Go back to Server 2022 and go to ADUC
- Right click a user go to "Properties" -> "Profile" tab
 - Click connect and set the Home folder to the personal share path
 - **Make sure to include \%username% (this will automatically create a folder for the user in the "Personal" folder**)
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_15_46_23" src="https://github.com/user-attachments/assets/b3cc6c91-cca8-4163-b27b-78bf0208b478" />

- Once you apply it, go to Shares and you should see a folder with that username
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_15_46_42" src="https://github.com/user-attachments/assets/10061234-5122-4905-b68b-13ffbfd756d2" />

- Now do this for the other two users we created
- Even if you sign out, the drive is still there
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_16_10_02" src="https://github.com/user-attachments/assets/d0c20aef-1c11-44a7-8e11-b26150d2bd96" />

> Keep in mind, in a real enviornment you may not need to manually set the home folder

### Drive Mapping Automation 
- To make our lives easier, we are going to map drives using GPOs
- First go to Group Policy Management
- Expand until you get to the OUs we created earlier for our users
- Right click and select "Create a new GPO in this domain, and link it here..."
 - Name it whatever you like
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_16_15_14" src="https://github.com/user-attachments/assets/53b29406-713e-4c6c-bda3-57dda8786858" />

- Now right clock the policy and click "Edit"
- From here navigate to "User Configuration" -> "Preferences" -> "Drive Maps"
- Right click it and do "New" -> "Mapped Drive"
- Specify drive location set drive letter
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_16_18_58" src="https://github.com/user-attachments/assets/4cb89ff0-7c48-4cfe-9109-5c19e6c69565" />
 
- Then go to the "Common Tab" and select the second and the fifth option
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_16_19_08" src="https://github.com/user-attachments/assets/2e828b76-c536-4b10-9384-39b5eeb6a854" />

- Click the "Targeting..." button
- Click "New Item" -> "Organizational Unit" and type in the OU you want to target
<img width="1055" height="792" alt="VirtualBox_Server 2022_14_09_2025_16_22_30" src="https://github.com/user-attachments/assets/9bc6073b-e2c3-4fe3-8dc5-39ffb2fddeba" />

- Click "ok" and "apply"
- Once your back GPM, make sure to enforce that GPO
- **Now you are set! Users within that OU will now have the drive automatically mapped!**
> It does not mean new users have access to it, they need to be added to the security group first
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-1_14_09_2025_16_31_31" src="https://github.com/user-attachments/assets/50fd2926-b1c9-4acd-9b37-3246c81378f6" />

# Remoting Into a Computer with Remote Desktop
- First we need to enable Remote Desktop
> In a real enviornment, you will have already set this up
- On your client machine you want to go to "System properties"
 - Using admin privileges
- Under the "Remote" tab you want to Allow Remote Assistance and Allow Remote connections
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_14_09_2025_16_53_16" src="https://github.com/user-attachments/assets/87295664-ef6d-4be4-b9dd-cc73a55063e5" />

- Do that for both machines
- Now on one machine sign into any user account
- Now on the other machine, sign into another user account
- On the first client, run Remote Deskop as administrator
> For lab purposes we are using admin privileges on a user account to make things simpler, in a real enviornment you will have your own credentials and permissions
- And type in either the name of the machine or the IP address of that machine
> Use the ipconfig command if you forgot
<img width="1061" height="804" alt="VirtualBox_Windows 11 M-1_14_09_2025_17_13_16" src="https://github.com/user-attachments/assets/a23f7490-49db-4184-af94-37dcd7171483" />

- Once you click connect, the other machine should display this message
<img width="1024" height="768" alt="VirtualBox_Windows 11 M-2_14_09_2025_17_18_18" src="https://github.com/user-attachments/assets/1ef48f79-d6fd-4481-b502-21684fe8339d" />

- Click "OK"
- Now on your remoting machine will ask you if you want to sign in anyway
 - Click Yes
- If you are at this screen congrats!
<img width="1061" height="804" alt="VirtualBox_Windows 11 M-1_14_09_2025_17_18_48" src="https://github.com/user-attachments/assets/6527be37-93c1-42f3-bbce-070d011ecfe4" />

- You have now remoted into the computer with your login
