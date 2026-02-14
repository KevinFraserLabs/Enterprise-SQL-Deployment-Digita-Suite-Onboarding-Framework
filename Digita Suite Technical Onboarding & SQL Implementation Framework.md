## 🚀 Enterprise SQL Deployment & Digita Suite Onboarding Framework With Environment Setup 🚀


## 🧩 Virtual Machine (VM) and Network Setup

To simulate the customer’s IT environment for the PracticeSuite Pro deployment, I built a dedicated virtual lab using Hyper‑V on my host PC. This provides a controlled setup where I can test the full installation and configuration process end‑to‑end.

---

### **1. Internal Network Configuration**

To create an isolated internal network for the lab, I configured a dedicated Hyper‑V internal switch and assigned a static IP to the host’s vEthernet adapter. NAT was then configured to provide internet access to all VMs.

**Configuration:**

- **Internal Switch Name:** `GF-Lab-Network`
- **Host vEthernet Adapter IP:** `192.168.2.1/24`
- **Lab Subnet:** `192.168.2.0/24`
- **NAT Name:** `GF-Lab-NAT`

#### **PowerShell NAT Configuration**

```powershell
New-NetNat -Name "GF-Lab-NAT" -InternalIPInterfaceAddressPrefix 192.168.2.0/24
```

<img width="719" height="677" alt="Screenshot 2026-02-14 113059" src="https://github.com/user-attachments/assets/a6c4edae-5f86-4b84-b1f4-c5e466a6b2b4" />
<img width="733" height="469" alt="Screenshot 2026-02-14 113226" src="https://github.com/user-attachments/assets/de9e5dc1-aa4d-4b2d-91bc-495dc3a433a3" />

### **2. Domain Control Server (DC)**

| Service | Configuration | IP Address |
| :--- | :--- | :--- |
| **VM Name** | DC (Created first VM) | `192.168.2.2` (Static) |
| **Operating System** | Windows Server 2022 (ISO used) | N/A |
| **Gateway** | `192.168.2.1` | N/A |
| **Public DNS** | `8.8.8.8` (Used for Internet Connectivity) | N/A |
| **Domain** | **Root Domain:** `GreenfieldAccountancyLtd.com` | N/A |


<img width="1267" height="706" alt="Screenshot 2026-02-14 131956" src="https://github.com/user-attachments/assets/a56a721a-49c5-4584-aed9-b5c84e34ab9f" />




#### **Core Services Configuration**

* **Active Directory:** Enabled and server promoted to a Domain Controller.

<img width="1050" height="753" alt="Screenshot 2026-02-14 132152" src="https://github.com/user-attachments/assets/6408c9b4-bfb3-4789-bf25-654a04ded478" />


* **DNS Server:** Enabled with Forward and Reverse Lookup Zones. Hostname and IP confirmed resolving.


<img width="750" height="536" alt="Screenshot 2026-02-14 132644" src="https://github.com/user-attachments/assets/f5bfdd08-29e6-46bc-99af-88201fc596f9" />


* **DHCP Server:** Enabled.
    * **Scope Range:** `192.168.2.4` to `192.168.2.10`.


<img width="955" height="469" alt="Screenshot 2026-02-14 133338" src="https://github.com/user-attachments/assets/189e0612-9fc7-4077-a5b5-5754adb85635" />

  

---

### **3. Windows Deployment Server (WDS)**

* **VM Name:** GF-Deploy01
* **IP Address:** `192.168.2.3` (Static)
* **DNS:** `192.168.2.2` (Pointing to DC).


<img width="397" height="455" alt="Screenshot 2026-02-14 134207" src="https://github.com/user-attachments/assets/2191bae6-cfbb-4f3b-9bf5-c15f88b3199b" />


* **Domain Status:** Successfully joined to `GreenfieldAccountancyLtd.com` Domain.
* **Deployment Share:** Configured for PXE installation of Windows 11 unattended.

<img width="1110" height="803" alt="Screenshot 2026-02-14 141959" src="https://github.com/user-attachments/assets/cf960cda-4d77-42f6-89a0-4acfb53a0297" />

<img width="433" height="584" alt="Screenshot 2026-02-14 142035" src="https://github.com/user-attachments/assets/34fe4188-f38f-476a-bd7d-222251a7b366" />

---

### **4. Deployment Configuration (Unattended Settings)**

The deployment process uses an unattended install with the following key settings:

| Setting | Value | Rationale |
| :--- | :--- | :--- |
| `Skip*` options | `YES` | Enables fully unattended installation. |
| `TaskSequenceID` | `Win11` | Specifies the installation task. |
| `JoinDomain` | `GreenfieldAccountancyLtd.com` | Joins the new domain. |
| `DomainAdmin` | `Administrator` | Domain administrator account used for joining. |
| `MachineObjectOU` | `CN=Computers,DC=GreenfieldAccountancyLtd,DC=com` | Ensures deployed clients land in the standard **Computers** container. |


<img width="1022" height="759" alt="Screenshot 2026-02-14 143548" src="https://github.com/user-attachments/assets/c463dcc7-7d11-4947-8f4b-62c8ed9bac4a" />

<img width="1015" height="788" alt="Screenshot 2026-02-14 143622" src="https://github.com/user-attachments/assets/d342c32d-3d0e-4c7d-a513-36cb1b2e559f" />

<img width="1018" height="794" alt="Screenshot 2026-02-14 143903" src="https://github.com/user-attachments/assets/a955f998-bbac-4eec-9909-b9224d7fa7c4" />

<img width="584" height="688" alt="Screenshot 2026-02-14 144705" src="https://github.com/user-attachments/assets/fac7bb33-c91d-4aaf-940b-df12cea5efba" />

<img width="1227" height="810" alt="Screenshot 2026-02-14 144833" src="https://github.com/user-attachments/assets/ce64a423-8d68-43ae-b597-12749b4d67f8" />

---

### **5. Client Verification**

* **Client VMs:** `GF-WINCLIENT01` and `GF-WINCLIENT02`.
* **Deployment Status:** Installed Windows 11 unattended (only required entering the computer name).
* **Domain Status:** Successfully auto-joined the domain.
* **IP Leases Confirmed:** `GF-WINCLIENT01` leased `192.168.2.5`, `GF-WINCLIENT02` leased `192.168.2.4`.


<img width="1102" height="622" alt="Screenshot 2026-02-14 145515" src="https://github.com/user-attachments/assets/6ba414d0-b623-45f4-bee7-5fef9596c39f" />

<img width="895" height="544" alt="Screenshot 2026-02-14 145656" src="https://github.com/user-attachments/assets/97f53b1b-a4e7-4558-b4bb-96b6eb1b63b1" />

<img width="1033" height="793" alt="Screenshot 2026-02-14 151100" src="https://github.com/user-attachments/assets/4b98ed13-9da1-4fce-b028-a7940845cdc4" />

<img width="857" height="585" alt="Screenshot 2026-02-14 151142" src="https://github.com/user-attachments/assets/6913b6c8-d1bc-4274-9794-00258e06b3ad" />

<img width="1503" height="686" alt="Screenshot 2026-02-14 151233" src="https://github.com/user-attachments/assets/a1d54c4b-0652-487e-90a2-8ada93d9d5f1" />

<img width="1335" height="852" alt="Screenshot 2026-02-14 151312" src="https://github.com/user-attachments/assets/7e48418f-50c9-49f9-a846-7112eb75343a" />


### **6. Active Directory Structure (User Accounts)**

* **Organizational Unit (OU) Created:** The **`GreenfieldAccountancyLtd`** OU was created directly under the domain root (`GreenfieldAccountancyLtd.com`). Within this Parent OU two Child OU's were then created `Users` and `Devices` and all devices were moved from the `Computers` Container to the newly created `Devices` Child OU. This dedicated OU ensures the structure is ready for precise Group Policy linking and simplified filtering for Microsoft Entra Connect synchronization.

<img width="806" height="509" alt="Screenshot 2026-02-14 152447" src="https://github.com/user-attachments/assets/c9fe2dae-6be8-41a7-9dfe-45cedfe0a8fd" />


* **Test Users Created:** Two test user accounts, **`Jane Smith`** and **`John Smith`**, were created and placed within the new **`Users`** OU.

<img width="1619" height="1000" alt="Screenshot 2026-02-14 161034" src="https://github.com/user-attachments/assets/ca5448f4-c182-451a-b455-131d58c4aded" />


### **7. Group Policy Management: Advanced User Desktop Policy** 🖼️

The first GPO was implemented to enforce corporate desktop standards, display dynamic user context, and secure the desktop environment against user modifications.

* **GPO Name:** `BGInfo`
* **GPO Scope:** Linked to the **`Devices`** OU.
* **Wallpaper Deployment:** Configured via a **User Logon Script** (`set_bg.bat`) running from **NETLOGON** to execute **BGInfo.exe** silently, displaying dynamic information (e.g., user name, IP address) on the desktop background.
* **Desktop Restrictions:**
    * **User Configuration** policies were applied to prevent changing the desktop background.
    * Additional policies were set to **lock down desktop personalization** and prevent users from accessing system settings (e.g., Registry Editor, specific Control Panel applets).
* **Verification:** Confirmed dynamic wallpaper applied and desktop personalization was fully restricted.


<img width="1288" height="676" alt="Screenshot 2026-02-14 180104" src="https://github.com/user-attachments/assets/9831af06-a628-4856-9aa0-35fa868a4b54" />

<img width="1531" height="876" alt="Screenshot 2026-02-14 175913" src="https://github.com/user-attachments/assets/168a07e3-762d-462d-bd8c-020fbeda7dda" />


## **8. Cloud Identity Integration: Microsoft Entra Connect** ☁️

The synchronization software was installed directly on the **Domain Controller** (`GF-DC01` - `192.168.2.2`) to facilitate the hybrid identity infrastructure, synchronizing the on-premises Active Directory with Microsoft Entra ID (formerly Azure AD).

* **Installation Location:** Domain Controller (GF-DC01 - `192.168.2.2`)
* **Software:** Installed the latest version of **Microsoft Entra Connect**.
* **Synchronization Configuration:**
    * **Authentication Method:** Configured for **Password Hash Synchronization (PHS)**.
    * **Synchronization Scope:** Set to synchronize the entire `GreenfieldAccountancyLtd` forest, specifically including the **`Users`** OU for identity and the **`Devices`** OU for device objects.
* **Verification:**
    * Confirmed both **`John Smith `** and **`Jane Smith`** accounts appeared in the Microsoft Entra admin center with **"Windows Server AD"** as the source.

<img width="283" height="366" alt="Screenshot 2026-02-14 201238" src="https://github.com/user-attachments/assets/fc8e7ace-a573-47b9-9e33-64b5a86d80ff" />

<img width="1903" height="511" alt="Screenshot 2026-02-14 201258" src="https://github.com/user-attachments/assets/6540212e-74ef-4f34-94ad-7dfed26ff79d" />


---

### **9. Hybrid Entra ID Join and Group Writeback** 🤝

The Entra Connect configuration was extended to enable **Hybrid Entra ID Join (HAADJ)** for devices and **Group Writeback** for cloud-created groups.

* **Service Connection Point (SCP):** The Entra Connect wizard successfully configured the Service Connection Point (SCP) within the on-premises Active Directory.
* **Group Writeback:** Confirmed the writeback feature was enabled and successfully synchronized groups created in Entra ID back to the on-premises Active Directory.
* **Device Verification:**
    * After an AD synchronization cycle and client restart, both **`GF-WINCLIENT1`** and **`GF-WINCLIENT2`** successfully registered their identity with Entra ID.
    * Devices appeared in the Microsoft Entra admin center with a **Join Type** of **"Microsoft Entra Hybrid Joined"**.


<img width="1898" height="404" alt="Screenshot 2026-02-14 210037" src="https://github.com/user-attachments/assets/22e81f29-67ce-411c-9d27-ef0f05483729" />


### **10. Intune Enrollment Prerequisites and GPO Fixes** 🛡️

Settings were configured in the cloud to allow enrollment.

* **MDM User Scope:** Configured in the Microsoft Entra admin center under **Mobility (MDM and MAM)** to set the **MDM User Scope** to **"All"**, ensuring all licensed users can auto-enroll.
* **Licensing:** An **Enterprise Mobility + Security E5** license was assigned to **`Jane Smith`** (and **`John Smith`** for testing) to meet the Intune management requirement.
* **GPO Timing Fix (Critical):** The **`Always wait for the network at computer startup and logon`** GPO was **Enabled** within the **Default Domain Policy** to prevent GPO failure due to network initialization timing issues.

<img width="1887" height="858" alt="Screenshot 2026-02-14 210341" src="https://github.com/user-attachments/assets/f9031396-852f-40cf-9820-06d032f5430b" />

<img width="1323" height="723" alt="Screenshot 2026-02-14 210551" src="https://github.com/user-attachments/assets/94a0ddfe-a1fd-4255-b047-0f531ff86f1a" />

