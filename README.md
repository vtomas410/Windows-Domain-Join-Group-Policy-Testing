# Windows Domain Join & Group Policy Testing

## Project Overview

In this phase of the Active Directory home lab, I connected a Windows client computer to the Active Directory domain created on the Windows Server.

After joining the client computer to the domain, I tested the Group Policy Objects (GPOs) created in the previous phase of the lab.

The goal was to verify that policies configured on the Domain Controller were successfully being applied to domain users and computers.

---

# 1. Lab Environment

The lab consisted of two virtual machines:

### Domain Controller

- Windows Server
- Active Directory Domain Services
- DNS
- Group Policy Management
- Domain Controller
<img width="1254" height="899" alt="2 Log in to admin" src="https://github.com/user-attachments/assets/acbb972f-fef5-4492-9de9-3a410814ad76" />

### Client Computer

- Windows 10/Windows 11
- VMware virtual machine
- Domain-joined workstation
<img width="841" height="523" alt="4 Create User1 For Joing Domain" src="https://github.com/user-attachments/assets/0cf094ac-fcd3-473c-8eda-7275aa071f5a" />

Example network:

```text
Windows Server
Domain Controller
       │
       │
       └── Active Directory Domain
                    │
                    │
              Windows Client
              Domain Joined
```

---

# 2. Configure the Windows Client Network

Before joining the computer to the domain, the client needed to communicate with the Domain Controller.

1. Start the Windows client virtual machine.
2. Open **Network & Internet Settings**.
3. Open the network adapter settings.
<img width="514" height="109" alt="5 Network Conections Change adapter settings" src="https://github.com/user-attachments/assets/44179f7d-c31a-48d6-9f82-53b2f5585d18" />

5. Locate the IPv4 configuration.<img width="1088" height="812" alt="6 Internet Protocol Version 4 TCP" src="https://github.com/user-attachments/assets/36cfddb7-ad27-4704-920a-3a06166aca8b" />

6. Configure the client with an IP address on the same network as the Domain Controller.

The client's DNS server should point to the **Domain Controller's IP address**.<img width="1046" height="818" alt="7 Set up IP adress Properties" src="https://github.com/user-attachments/assets/a9fd68a4-547c-4b54-a1fa-171b5924fcb0" />


Example:

```text
Client IP:        192.168.1.129
Domain Controller: 192.168.1.128
DNS Server:       192.168.1.128
```
<img width="1113" height="799" alt="8 ipconfig all to check" src="https://github.com/user-attachments/assets/4c22a76c-e2cc-4c12-a45b-0210064b7af9" />

> The actual IP addresses used in the lab may be different.

This is important because Active Directory relies heavily on DNS for domain discovery and authentication.

---

# 3. Verify Network Connectivity

Before attempting to join the domain, verify communication between the client and Domain Controller.

Open Command Prompt on the Windows client.

Test the Domain Controller:

```cmd
ping <Domain-Controller-IP>
```

Example:

```cmd
ping 192.168.125.128
```
<img width="1041" height="820" alt="10 Ping DNS address_2" src="https://github.com/user-attachments/assets/3c44390b-c18f-4cbc-8b5e-ff769817bacf" />

A successful response confirms basic network connectivity.

---

# 4. Verify DNS Resolution

Test DNS by attempting to resolve the Active Directory domain.

```cmd
nslookup <domain-name>
```

Example:

```cmd
nslookup example.local
```
<img width="546" height="123" alt="11 5 Nslook up" src="https://github.com/user-attachments/assets/235033da-12e6-4643-a329-2d049f40d18d" />

The DNS server returned should be the Domain Controller.

If the client is using an external DNS server such as Google's `8.8.8.8`, domain joining may fail because the client needs to use the Active Directory DNS server.

---

# 5. Verify the Domain Controller

On the Windows Server:

1. Open **Server Manager**.
2. Verify that Active Directory Domain Services is installed.
3. Open **Tools**.
4. Open **Active Directory Users and Computers**.
5. Verify that the domain is available.
6. Open **Group Policy Management**.
7. Verify that the previously created GPOs are present.
<img width="1240" height="882" alt="11 verify GPO" src="https://github.com/user-attachments/assets/5dd41561-2dd8-4aa8-b7b6-ddc7b14cf402" />


---

# 6. Join the Windows Client to the Domain

On the Windows client:

1. Open **Settings**.
2. Navigate to **System → About**.
3. Open the system/domain settings.

Alternatively:

1. Press:

```text
Windows Key + R
```

2. Enter:

```text
sysdm.cpl
```

3. Open the **Computer Name** tab.
4. Select **Change**.
5. Select **Domain**.
6. Enter the Active Directory domain name.

Example:

```text
example.local
```

7. Select **OK**.
<img width="1242" height="924" alt="12 renaming client" src="https://github.com/user-attachments/assets/1d28521f-f4e9-45d9-b82f-60ee16aa6666" />


---

# 7. Authenticate to the Domain

Windows will request credentials with permission to join the computer to the domain.

Enter the credentials of a domain account with the appropriate permissions.

Example:

```text
Username: Administrator
Password: ********
```

If successful, Windows should display a message indicating that the computer has joined the domain.
<img width="381" height="231" alt="14 welcome to domain" src="https://github.com/user-attachments/assets/ab319c6a-5cf8-41c5-a4e4-a9da41b5b9e8" />

---

# 8. Restart the Client

After joining the domain:

1. Close the system configuration windows.
2. Restart the Windows client.
3. Wait for Windows to boot.

The computer is now a member of the Active Directory domain.

---

# 9. Log Into the Domain

At the Windows login screen, select **Other User** if necessary.
<img width="1037" height="287" alt="15 log into user" src="https://github.com/user-attachments/assets/8cce5bd0-dd97-4650-9a0d-79aad10a4b4a" />

Enter a domain account.

Example:

```text
DOMAIN\jsmith
```

or:

```text
jsmith@example.local
```

After successful authentication, the user will log into the domain-connected workstation.

---

# 10. Verify the Computer in Active Directory

Return to the Domain Controller.

Open:

**Server Manager → Tools → Active Directory Users and Computers**

Navigate to the appropriate computer container or OU.

The newly joined Windows client should appear as a computer object.

Example:

```text
Computers
│
└── WINCLIENT01
```

The computer object can then be moved into the appropriate Organizational Unit.

---

# 11. Move the Computer Into an OU

For better organization and Group Policy management:

1. Right-click the computer object.
2. Select **Move**.
3. Select the appropriate OU.
<img width="1244" height="794" alt="17 Move Comp1" src="https://github.com/user-attachments/assets/d7bf41f7-7a68-4c00-86b7-1528f2f99bf2" />
<img width="518" height="518" alt="18 Move Comp1 verfied" src="https://github.com/user-attachments/assets/360e4d61-55de-4e25-8763-c8aa3ae524a8" />

Example:

```text
example.local
│
├── Users
├── Groups
├── Servers
│
└── Workstations
      │
      └── WINCLIENT01
```

This becomes especially important when applying computer-based GPOs.

---

# 12. Force a Group Policy Update

After joining the domain, open Command Prompt on the client.

Run:

```cmd
gpupdate /force
```
<img width="578" height="393" alt="19 gpuupdate force" src="https://github.com/user-attachments/assets/d0ee1125-b764-4ac2-a932-5b486a6f26ad" />

This forces Windows to retrieve the latest Group Policy settings from the Domain Controller.

The command should report that the computer and user policies were updated successfully.


---

# 13. Verify Applied Group Policies

Run:

```cmd
gpresult /r
```

This displays the Group Policy information applied to the current user and computer.

Look for:

### Applied Group Policy Objects

The GPOs created during the previous lab should appear here.

Example:

```text
Applied Group Policy Objects
----------------------------
Password Policy
Desktop Wallpaper
Drive Mapping
Restrict Control Panel
USB Devices
```

The exact list will depend on which GPOs were linked to the user's OU or computer's OU.


---

# 14. Final Lab Structure

At this point, the home lab should resemble:

```text
                    Active Directory
                          │
                   Domain Controller
                          │
              ┌───────────┴───────────┐
              │                       │
        Group Policy              DNS
              │
              │
       ┌──────┴──────┐
       │             │
     Users        Computers
       │             │
   jsmith        WINCLIENT01
                     │
                     │
              Windows Client
              Domain Joined
```

---

# 15. Skills Demonstrated

This lab demonstrates hands-on experience with:

- Windows domain joining
- Active Directory
- Windows Server
- DNS configuration
- Domain authentication
- Domain users
- Domain computers
- Organizational Units
- Group Policy
- GPO testing
- `gpupdate`
- `gpresult`
- Network troubleshooting
- DNS troubleshooting
- Windows client administration
- Endpoint security controls

---

# Project Outcome

The Windows client was successfully connected to the Active Directory domain and used to test the Group Policy configuration created in the previous phase.

This allowed me to move beyond simply creating GPOs and verify how they behave on an actual domain-joined workstation.

The lab provided practical experience with the complete workflow:

```text
Create GPO
     ↓
Link GPO to OU
     ↓
Join Windows Client to Domain
     ↓
Log in with Domain User
     ↓
Force Group Policy Update
     ↓
Verify Applied Policies
     ↓
Test GPO Functionality
     ↓
Troubleshoot if Necessary
```

This represents a more realistic IT support environment where an administrator must not only configure policies but also verify that they are successfully reaching and affecting client machines.
