# Active Directory Security & Administration Home Lab

## Overview

This project demonstrates the deployment, administration, and security configuration of a Windows Server Active Directory environment in a controlled virtual lab.

The lab was designed to build hands-on experience with Active Directory Domain Services (AD DS), Group Policy, user and group administration, delegated administrative permissions, PowerShell, and NTFS/share access controls.

Rather than only configuring the environment, I also tested the implemented security controls to verify that delegated permissions and file-access restrictions behaved as expected.

---

## Lab Objectives

- Configure a Windows Server environment for Active Directory
- Promote Windows Server to a Domain Controller
- Create an Active Directory forest and domain
- Create and manage Organizational Units (OUs)
- Create and manage users and security groups
- Configure and test Group Policy
- Practice delegated administration
- Apply least-privilege concepts
- Configure network shares and NTFS permissions
- Validate access-control restrictions
- Use PowerShell to verify server and Active Directory configuration

---

## Technologies & Concepts

- Windows Server
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- Group Policy Management Console (GPMC)
- PowerShell
- Organizational Units (OUs)
- Security Groups
- Group Policy Objects (GPOs)
- Delegated Administration
- NTFS Permissions
- Share Permissions
- Access Control Lists (ACLs)
- Identity & Access Management (IAM)
- Least Privilege
- TCP/IP Networking
- NAT
- Virtualization

---

## Lab Architecture

The lab consisted of a Windows Server virtual machine configured to provide Active Directory services.

The server was configured with the hostname:

`MEETYP-Server1`

Network connectivity was configured using NAT so that the virtual machine could access the internet while maintaining communication with the host system.

After networking was configured, Windows updates were installed and the server was promoted to a Domain Controller.

A new Active Directory forest and domain were then created for the lab environment.

---

## 1. Domain Controller Configuration

I began by configuring the Windows Server system and verifying network connectivity.

After installing the latest Windows updates, I promoted the server to a Domain Controller using Server Manager and created a new Active Directory forest and domain.

PowerShell was used to verify the server hostname and review the installed Windows Server roles and features.

### PowerShell Verification

```powershell
$env:computername

Import-Module ServerManager

Get-WindowsFeature |
Where-Object {$_.Installed -eq $True}
```

This allowed me to verify the system configuration and confirm that the required server roles and features were installed.

---

## 2. Active Directory Verification with PowerShell

PowerShell was also used to retrieve computer objects from Active Directory and review information about the systems registered in the directory.

The following command was used to retrieve computer names, IPv4 addresses, operating systems, and service-pack information:

```powershell
Get-ADComputer -Filter * -Properties ipv4Address, OperatingSystem, OperatingSystemServicePack |
Format-List name, ipv4*, oper*
```

This provided command-line verification of Active Directory computer objects and their associated system information.

---

## 3. Organizational Unit and Identity Management

Several Organizational Units were created to simulate different administrative and organizational requirements.

The lab included the following OUs:

- Kiosk
- Head Office
- Montreal
- Managed Groups

Within these OUs, I created users and security groups to practice identity administration and role-based access management.

### Head Office Groups

The Head Office OU included groups such as:

- IT
- HR
- Finance
- Head Office Users
- Managers

Users were assigned to groups according to their intended roles.

### Montreal Groups

The Montreal OU included:

- Montreal Users
- Montreal-IT

The Montreal-IT group was later used to test delegated administration.

### Managed Groups

The Managed Groups OU included:

- `HR_Share_RO`
- `HR_Share_ReadWrite`

These security groups were used to control access to shared HR resources.

---

## 4. Kiosk Group Policy

To practice Group Policy administration, I created a dedicated Organizational Unit called `Kiosk` and created a kiosk user account for testing.

Using the Group Policy Management Console (GPMC), I created a Group Policy Object called:

`Kiosk Policy`

The GPO was linked to the Kiosk OU.

Settings were configured under both Computer Configuration and User Configuration to create a more restricted kiosk environment.

The objective was to limit access to system settings and administrative functionality while still allowing the kiosk user to perform required basic tasks.

### Policy Testing

After configuring the GPO, I logged in using the kiosk user account and verified that the restrictions were being applied correctly.

I also confirmed that the user could still access the functions required for the kiosk environment.

Where necessary, I adjusted the Group Policy settings and tested the configuration again.

Once testing was complete, I exported the Kiosk Policy GPO to create a backup that could be reviewed or imported into another environment.

---

## 5. Delegated Active Directory Administration

The Montreal OU was used to practice delegated administration.

I created the `Montreal-IT` security group and delegated control of the Montreal OU to this group.

The objective was to allow members of Montreal-IT to perform specific administrative tasks within their assigned OU without providing full Domain Administrator privileges.

This demonstrates the principle of least privilege by limiting administrative permissions to the resources required for a particular role.

### Delegation Validation Test

To test the delegated permissions, I logged into the server using an account belonging to the Montreal-IT group.

I then opened Active Directory Users and Computers and attempted to reset the password of a user located outside the Montreal OU.

The operation was denied.

This demonstrated that the delegated account could not modify an Active Directory object outside its authorized administrative scope.

The test helped validate that the delegated permissions were restricting administrative access as intended.

---

## 6. Network Share and Security Groups

I created a folder named `SHARE` on the server and shared it across the network.

Inside the shared folder, I created an `HR` folder and an additional restricted folder called:

`HR Managers`

Two security groups were used to manage access:

| Security Group | Access Level |
| --- | --- |
| `HR_Share_RO` | Read-only |
| `HR_Share_ReadWrite` | Full Control |

Using security groups instead of assigning permissions directly to individual users provided a more manageable approach to controlling resource access.

---

## 7. NTFS Permissions and Access Control

NTFS permissions were configured on the HR folder according to the security groups.

The permissions included:

- `HR_Share_RO` — Read-only access
- `HR_Share_ReadWrite` — Full Control

The `HR Managers` folder required more restrictive access.

Permission inheritance was disabled on this folder, and permissions were configured so that only members of the Managers group had Full Control.

This created a more restricted resource within the broader HR share.

---

## 8. NTFS Permission Validation

After configuring the permissions, I tested the access-control configuration using an account that belonged to the `HR_Share_ReadWrite` group but was not a member of the Managers group.

The account was able to access the appropriate HR resources but was denied access when attempting to open the restricted `HR Managers` folder.

This confirmed that the NTFS permissions and inheritance configuration were enforcing the intended access restrictions.

The test demonstrated the difference between having access to a parent resource and having authorization to access a more restricted child resource.

---

## Security Concepts Demonstrated

This project provided hands-on practice with several important security and system-administration concepts:

### Identity & Access Management

- Active Directory user administration
- Security group management
- Organizational Units
- Group membership
- Role-based access

### Least Privilege

Administrative permissions were delegated only to the required organizational scope rather than providing unrestricted Domain Administrator access.

### Group Policy

Group Policy was used to centrally apply restrictions to a dedicated kiosk environment.

### Delegated Administration

Administrative responsibilities were assigned to a security group and restricted to a specific Organizational Unit.

### Authentication & Authorization

User accounts and group memberships were used to determine which resources and administrative actions users were authorized to access.

### NTFS Access Control

Security groups, NTFS permissions, ACLs, and inheritance settings were used to control access to shared resources.

### Security Validation

Access controls were tested after implementation instead of assuming that the configuration worked correctly.

This included:

- Testing Group Policy restrictions
- Testing delegated Active Directory permissions
- Testing access outside a delegated OU
- Testing NTFS folder permissions
- Verifying denied access to restricted resources

---

## Key Takeaways

This lab strengthened my understanding of how Active Directory can be used to centrally manage users, computers, groups, policies, administrative privileges, and access to organizational resources.

One of the most valuable parts of the project was testing security controls after they were configured.

The delegated administration test demonstrated how administrative responsibilities can be limited to a specific organizational scope without providing unrestricted domain privileges.

The NTFS permission testing also demonstrated how security groups, permission inheritance, and ACLs can be combined to control access to sensitive resources.

Overall, the project provided practical experience with Windows Server administration, Active Directory, Group Policy, PowerShell, Identity & Access Management, least privilege, and access-control validation.

---

## Skills Demonstrated

`Active Directory` · `Windows Server` · `Group Policy` · `PowerShell` · `Identity & Access Management` · `Access Control` · `NTFS Permissions` · `Security Groups` · `Organizational Units` · `Delegated Administration` · `Least Privilege` · `TCP/IP` · `Networking` · `Troubleshooting`

---

## Project Documentation

The complete project report contains additional configuration details and screenshots documenting the lab.

📄 **Full Lab Report:**  
[`docs/Active-Directory-Home-Lab-Report.pdf`](docs/Active-Directory-Home-Lab-Report.pdf)

---

## About This Project

This project was completed in a controlled lab environment for educational and hands-on learning purposes.

It demonstrates practical experience with Windows Server and Active Directory administration while applying security concepts including least privilege, role-based access, administrative delegation, and access-control validation.
