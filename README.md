# Workstation Hardening using GPOs

### Introduction

This project focuses on Workstation Hardening—the process of securing a computer system by reducing its surface of vulnerability. By using Microsoft Active Directory and Group Policy Objects (GPOs), I implemented a robust security framework based on the Principle of Least Privilege (PoLP) and Identity and Access Management (IAM) best practices.

### Lab network diagram
<img width="544" height="344" alt="image" src="https://github.com/user-attachments/assets/927aa5d2-7d33-4fc8-b968-7cec26bb4fbb" />


<img width="250" height="250" alt="image" src="https://github.com/user-attachments/assets/d209c881-23ae-4a1b-a3cf-618818666e9f" /> <br />
<i>List of GPOs created in AD</i>

#### Technologies used
  VMware Workstation 25H2, Windows Server 2022, Windows 10 64bit, Active Directory, Group Policy, Event Viewer.

#### Key Concepts
  GPO, IAM, RBAC, Least Privilege, System Hardening, Security Auditing.

### Step-by-step Implementation

### Key Notes for this lab
- Best practice is to create a GPO for each configuration. This way, if it is necessary to disable just one configuration, it can be done easily by disabling the corresponding GPO.
- In a production network, it is important to always test the GPOs with some users and computers before enabling them for the entire network.

#### Step 1. Restrict Removable Storage Access (USB Block)
Prevents data exfiltration and the introduction of unauthorized malware by disabling the use of external USB drives.

•	<b>Path:</b> Computer Configuration > Policies > Administrative Templates > System > Removable Storage Access

•	<b>Setting:</b> All Removable Storage classes: Deny all access

<img width="678" height="630" alt="image" src="https://github.com/user-attachments/assets/dc6f0a96-4715-4cbf-b485-719d6a47a1fc" />


#### Step 2. Prohibit Non-Administrative Software Installation
Ensures that only authorized IT staff can modify system files, preventing the installation of unapproved applications.

•	<b>Path:</b> Computer Configuration > Policies > Administrative Templates > Windows Components > Windows Installer

•	<b>Setting:</b> Turn off Windows Installer (Set to "Always")

<img width="1061" height="986" alt="image" src="https://github.com/user-attachments/assets/f389fa34-b85e-4338-b157-54cd887a781a" />


#### Step 3. Enforce Windows Defender Firewall
Provides a critical layer of network defense by controlling inbound and outbound traffic based on security rules.

•	<b>Path:</b> Computer Configuration > Policies > Windows Settings > Security Settings > Windows Firewall with Advanced Security

•	<b>Setting:</b> Windows Firewall: On (for Domain, Private, and Public profiles)

<img width="617" height="706" alt="image" src="https://github.com/user-attachments/assets/a86b672a-6737-42ee-86d3-a0171588d7c9" />


#### Step 4. Enable Advanced Security Auditing
Records critical system events—such as logon attempts and object access—to provide a forensic trail for security monitoring.

•	<b>Path:</b> Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration

•	<b>Setting:</b> Audit Logon, Account Management, and Object Access (Success & Failure)

<img width="696" height="335" alt="image" src="https://github.com/user-attachments/assets/5049a0d7-2c53-40bb-9018-87b329c69532" />

Note: Already done in previous project

#### Step 5. Disable the Built-in Guest Account
Eliminates a common entry point for unauthenticated users, reducing the overall attack surface of the workstation.

•	<b>Path:</b> Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options

•	<b>Setting:</b> Accounts: Guest account status (Disabled)

<img width="644" height="783" alt="image" src="https://github.com/user-attachments/assets/484397bb-4778-462a-8733-eba8d180a51e" />


#### Step 6. Rename the Built-in Administrator Account
Mitigates automated brute-force attacks by obscuring the default administrative username.

•	<b>Path:</b> Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options

•	<b>Setting:</b> Accounts: Rename administrator account

<img width="645" height="784" alt="image" src="https://github.com/user-attachments/assets/3d30af4b-e219-4430-82c7-eb1e2c6fdd75" />


#### Step 7. Implement Application Control (AppLocker)
Enforces a "whitelist" approach, ensuring that only digitally signed or pre-approved applications are permitted to execute.

Note: First, enable Application Identity Service to start automatically. Computer Configuration > Windows Settings > Security Settings > System Services

•	<b>Path:</b> Computer Configuration > Policies > Windows Settings > Security Settings > Application Control Policies > AppLocker

•	<b>Setting:</b> Executable, Windows Installer, and Packaged app Rules (Create Default Rules)

<img width="555" height="961" alt="image" src="https://github.com/user-attachments/assets/c67e11af-f737-40a5-8c43-ee17b579f6c1" />


#### Step 8. Prevent Access to Registry Editing Tools (Regedit)
Stops users from making unauthorized changes to the system configuration that could lead to privilege escalation.

•	<b>Path:</b> User Configuration > Policies > Administrative Templates > System

•	<b>Setting:</b> Prevent access to registry editing tools

<img width="1056" height="980" alt="image" src="https://github.com/user-attachments/assets/53375374-daf4-427d-b88e-9d1468616d09" />


#### Step 9. Prohibit Access to Control Panel and PC Settings
Prevents users from modifying hardware settings, network configurations, or security features.

•	<b>Path:</b> User Configuration > Policies > Administrative Templates > Control Panel

•	<b>Setting:</b> Prohibit access to Control Panel and PC settings

<img width="1064" height="988" alt="image" src="https://github.com/user-attachments/assets/d782efa7-088f-477e-887c-f8cad3b7539f" />


#### Step 10. Restrict Command Prompt (CMD) and PowerShell Access
Blocks "living-off-the-land" attacks by preventing standard users from executing scripts or advanced command-line tools.

•	<b>Path:</b> User Configuration > Policies > Administrative Templates > System

•	<b>Setting:</b> Prevent access to the command prompt (Set "Disable the command prompt script processing" to Yes)

<img width="1058" height="983" alt="image" src="https://github.com/user-attachments/assets/312e6299-a36a-4de6-9746-f7acccfe1992" />



### Lessons Learned
#### 1. Strategic GPO Granularity
Creating individual GPOs for each security setting is a professional best practice. While it takes more time initially, it offers much higher flexibility. In a production environment, this allows an administrator to troubleshoot or roll back a specific restriction (like a USB block) for a specific department without compromising the entire security stack.

#### 2. Whitelisting over Blacklisting
Implementing AppLocker was key because trying to block "bad" software is an endless task. By moving to a "Default Deny" or whitelisting approach, I shifted the security burden: instead of the system trying to recognize every virus, it only recognizes what is explicitly trusted. This is a far more effective defense against Zero-Day attacks.

#### 3. Obscurity as a Layer of Defense
While renaming the built-in Administrator account and disabling the Guest account won't stop a determined hacker, they break the automated scripts and "Living-off-the-Land" techniques that rely on default Windows configurations.

#### 4. Balancing Security and Usability
Restricting CMD, PowerShell, and the Registry Editor highlighted the inherent tension between security and IT support productivity. For a policy to be successful, it must be targeted. For example, using User Configuration for these GPOs ensures that standard employees are protected, while IT Administrators can still access the tools they need to perform their jobs.

#### 5. Visibility through Auditing
By integrating Advanced Security Auditing, if a user attempts to bypass a GPO—such as trying to open the Registry Editor—the system must not only block the action but also log the event. Reviewing these logs in Event Viewer confirmed that the hardening measures were actively working and providing the data necessary for a Security Operations Center (SOC) to respond.
