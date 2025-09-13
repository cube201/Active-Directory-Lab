# Active Directory Home Lab
This repository showcases a walkthrough on creating and configuring an Active Directory lab environment within VirtualBox.  
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
