## 🚀 Enterprise SQL Deployment & Application Onboarding Framework for Hybrid Environments 🚀



## 🧩 Virtual Machine (VM) and Network Setup 🧩

To simulate a customer’s IT environment for an enterprise application deployment, I built a dedicated virtual lab using Hyper‑V on my host PC. This provided a controlled hybrid environment where I could test the full SQL Server installation, configuration, and client‑side onboarding workflow end‑to‑end.

A fictional organisation, GreenfieldAccountancyLtd, was created to represent a typical mid‑sized accountancy firm. Two standard user accounts — John Smith and Jane Smith — were added to simulate real staff identities and to model how departmental users would interact with the system during an onboarding engagement.



### **1. Internal Network Configuration:**🌐


To create an isolated internal network for the lab, I configured a dedicated Hyper‑V internal switch and assigned a static IP to the host’s vEthernet adapter. NAT was then configured to provide internet access to all VMs.

**Configuration:**

- **Internal Switch Name:** `GF-Lab-Network`
- **Host vEthernet Adapter IP:** `192.168.2.1/24`
- **Lab Subnet:** `192.168.2.0/24`
- **NAT Name:** `GF-Lab-NAT`

#### **PowerShell NAT Configuration:**

```powershell
New-NetNat -Name "GF-Lab-NAT" -InternalIPInterfaceAddressPrefix 192.168.2.0/24
```

<img width="719" height="677" alt="Screenshot 2026-02-14 113059" src="https://github.com/user-attachments/assets/a6c4edae-5f86-4b84-b1f4-c5e466a6b2b4" />
<img width="733" height="469" alt="Screenshot 2026-02-14 113226" src="https://github.com/user-attachments/assets/de9e5dc1-aa4d-4b2d-91bc-495dc3a433a3" />



### **2. Domain Control Server (DC):** 🏢


| Service | Configuration | IP Address |
| :--- | :--- | :--- |
| **VM Name** | DC (Created first VM) | `192.168.2.2` (Static) |
| **Operating System** | Windows Server 2022 (ISO used) | N/A |
| **Gateway** | `192.168.2.1` | N/A |
| **Public DNS** | `8.8.8.8` (Used for Internet Connectivity) | N/A |
| **Domain** | **Root Domain:** `GreenfieldAccountancyLtd.com` | N/A |


<img width="1267" height="706" alt="Screenshot 2026-02-14 131956" src="https://github.com/user-attachments/assets/a56a721a-49c5-4584-aed9-b5c84e34ab9f" />



#### **Core Services Configuration:**

* **Active Directory:** Enabled and server promoted to a Domain Controller.


<img width="1050" height="753" alt="Screenshot 2026-02-14 132152" src="https://github.com/user-attachments/assets/6408c9b4-bfb3-4789-bf25-654a04ded478" />


* **DNS Server:** Enabled with Forward and Reverse Lookup Zones. Hostname and IP confirmed resolving.


<img width="750" height="536" alt="Screenshot 2026-02-14 132644" src="https://github.com/user-attachments/assets/f5bfdd08-29e6-46bc-99af-88201fc596f9" />


* **DHCP Server:** Enabled.
    * **Scope Range:** `192.168.2.4` to `192.168.2.10`.


<img width="955" height="469" alt="Screenshot 2026-02-14 133338" src="https://github.com/user-attachments/assets/189e0612-9fc7-4077-a5b5-5754adb85635" />

  

### **3. Windows Deployment Server (WDS):** 🖥️

* **VM Name:** GF-Deploy01
* **IP Address:** `192.168.2.3` (Static)
* **DNS:** `192.168.2.2` (Pointing to DC).


<img width="397" height="455" alt="Screenshot 2026-02-14 134207" src="https://github.com/user-attachments/assets/2191bae6-cfbb-4f3b-9bf5-c15f88b3199b" />


* **Domain Status:** Successfully joined to `GreenfieldAccountancyLtd.com` Domain.
* **Deployment Share:** Configured for PXE installation of Windows 11 unattended.


<img width="1110" height="803" alt="Screenshot 2026-02-14 141959" src="https://github.com/user-attachments/assets/cf960cda-4d77-42f6-89a0-4acfb53a0297" />


<img width="433" height="584" alt="Screenshot 2026-02-14 142035" src="https://github.com/user-attachments/assets/34fe4188-f38f-476a-bd7d-222251a7b366" />



### **4. Deployment Configuration (Unattended Settings):** 📡

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



### **5. Client Verification:** ⚙️

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



### **6. Active Directory Structure (User Accounts):** 🧑‍💼

* **Organizational Unit (OU) Created:** The **`GreenfieldAccountancyLtd`** OU was created directly under the domain root (`GreenfieldAccountancyLtd.com`). Within this Parent OU two Child OU's were then created `Users` and `Devices` and all devices were moved from the `Computers` Container to the newly created `Devices` Child OU. This dedicated OU ensures the structure is ready for precise Group Policy linking and simplified filtering for Microsoft Entra Connect synchronization.


<img width="806" height="509" alt="Screenshot 2026-02-14 152447" src="https://github.com/user-attachments/assets/c9fe2dae-6be8-41a7-9dfe-45cedfe0a8fd" />


* **Test Users Created:** Two test user accounts, **`Jane Smith`** and **`John Smith`**, were created and placed within the new **`Users`** OU.


<img width="1619" height="1000" alt="Screenshot 2026-02-14 161034" src="https://github.com/user-attachments/assets/ca5448f4-c182-451a-b455-131d58c4aded" />



### **7. Group Policy Management: Advanced User Desktop Policy:** 🧾

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



## **8. Cloud Identity Integration: Microsoft Entra Connect:** ☁️

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



### **9. Hybrid Entra ID Join and Group Writeback:** 🤝

The Entra Connect configuration was extended to enable **Hybrid Entra ID Join (HAADJ)** for devices and **Group Writeback** for cloud-created groups.

* **Service Connection Point (SCP):** The Entra Connect wizard successfully configured the Service Connection Point (SCP) within the on-premises Active Directory.
* **Group Writeback:** Confirmed the writeback feature was enabled and successfully synchronized groups created in Entra ID back to the on-premises Active Directory.
* **Device Verification:**
    * After an AD synchronization cycle and client restart, both **`GF-WINCLIENT1`** and **`GF-WINCLIENT2`** successfully registered their identity with Entra ID.
    * Devices appeared in the Microsoft Entra admin center with a **Join Type** of **"Microsoft Entra Hybrid Joined"**.


<img width="1898" height="404" alt="Screenshot 2026-02-14 210037" src="https://github.com/user-attachments/assets/22e81f29-67ce-411c-9d27-ef0f05483729" />



### **10. Intune Enrollment Prerequisites and GPO Fixes:** 🛡️

Settings were configured in the cloud to allow enrollment.

* **MDM User Scope:** Configured in the Microsoft Entra admin center under **Mobility (MDM and MAM)** to set the **MDM User Scope** to **"All"**, ensuring all licensed users can auto-enroll.
* **Licensing:** **Microsoft 365 E3** licenses were assigned to **`Jane Smith`** and **`John Smith`** for testing to meet the Intune management requirement.
* **GPO Timing Fix (Critical):** The **`Always wait for the network at computer startup and logon`** GPO was **Enabled** within the **Default Domain Policy** to prevent GPO failure due to network initialization timing issues.


<img width="1887" height="858" alt="Screenshot 2026-02-14 210341" src="https://github.com/user-attachments/assets/f9031396-852f-40cf-9820-06d032f5430b" />


<img width="1323" height="723" alt="Screenshot 2026-02-14 210551" src="https://github.com/user-attachments/assets/94a0ddfe-a1fd-4255-b047-0f531ff86f1a" />



### **11. Group Policy Management: Automatic Intune Enrollment:** ☁️

The GPO was configured to initiate the successful automatic enrollment of domain-joined devices into Microsoft Intune.

* **GPO Name:** `Intune Auto Enroll`
* **GPO Scope:** Linked directly to the **`Devices`** child OU.
* **Policy Path:** **Computer Configuration** $\rightarrow$ **Policies** $\rightarrow$ **Administrative Templates** $\rightarrow$ **Windows Components** $\rightarrow$ **MDM**
* **Configuration:** The setting **`Enable automatic MDM enrollment using default Azure AD credentials`** was set to **Enabled** with the credential type set to **`User Credential`**. The **MDM Application ID** was correctly left **blank**.


<img width="1907" height="995" alt="Screenshot 2026-02-15 074816" src="https://github.com/user-attachments/assets/85c91420-2397-4b23-8421-2b29a9f659c1" />



### **12. Verification: Intune Enrollment and Management:** ✅

This section confirms the successful automatic enrollment of the client devices into **Microsoft Intune**, completing the Hybrid Entra ID Join (HAADJ) and Intune co-management setup.

* **Intune Enrollment Status:** After a client reboot and user sign-in (using a licensed account like **`John or Jane Smith`**), both **`GF-WINCLIENT1`** and **`GF-WINCLIENT2`** devices successfully completed the MDM auto-enrollment process.
* **Verification (Client-Side):**
    * **Access Work or School:** Confirmed the devices show a successful connection to **`GreenfieldAccountancyLtd`**'s work or school account, managed by **Microsoft Intune**.
    * **Command Prompt:** Running the command `dsregcmd /status` confirms both **`AzureAdJoined: YES`** and **`MdmManaged`** is populated (indicating Intune enrollment).


<img width="1023" height="273" alt="Screenshot 2026-02-15 082108" src="https://github.com/user-attachments/assets/0c486fcd-0cb7-40c7-a071-bf477a1f6c45" />


<img width="1040" height="282" alt="Screenshot 2026-02-15 082054" src="https://github.com/user-attachments/assets/a9ea6086-097c-4737-8379-6148e143cf23" />



* **Verification (Intune Portal):**
    * The devices appeared in the **Microsoft Intune admin center** under **Devices** → **All devices**.
    * The **Managed by** column is confirmed as **"Intune"**.
    * The **Primary user** is correctly listed as the signed-in user (**`John Smith`** or **`Jane Smith`**).
    * The **Compliance** status is **"Compliant"**.


<img width="1903" height="436" alt="Screenshot 2026-02-15 081909" src="https://github.com/user-attachments/assets/449bbb57-0947-410f-8305-506847ce857f" />


<img width="1906" height="464" alt="Screenshot 2026-02-15 081854" src="https://github.com/user-attachments/assets/93cc616b-c775-473f-a9cd-10a6b5d78a30" />



### **13. SQL Server Host Preparation (GF‑SQL01):** 🗄️✨

Before deploying the dedicated SQL Server host, the network addressing scheme was updated to support the expansion of the environment. The original design reserved only a small number of static IP addresses (192.168.2.1–192.168.2.3), as the initial scope included just the Domain Controller and Deployment Server. With the introduction of additional server roles—specifically a dedicated SQL Server instance—the static range was expanded to ensure a clean, contiguous block for infrastructure components.
To accommodate this change, the DHCP scope was recreated to begin at 192.168.2.10, reserving 192.168.2.1–192.168.2.10 exclusively for static server assignments. Existing DHCP leases were cleared so that domain‑joined clients could obtain new addresses within the updated range.
Updated Client Addressing:
- GF-WINCLIENT1 now receives 192.168.2.10
- GF-WINCLIENT2 now receives 192.168.2.11

This adjustment mirrors real‑world environments where IP planning evolves as infrastructure grows, ensuring a clear separation between static and dynamic addressing while maintaining a scalable and professional network layout.
With the network foundation updated, the next step is to provision GF‑SQL01, the dedicated database server that will host the SQL Server instance required for backend application support.

Once the operating system installation was completed, GF-SQL01 was assigned its static IP address (192.168.2.4) and successfully joined to the GreenfieldAccountancyLtd.com domain. This ensured the server was fully integrated into the environment and ready for SQL Server deployment. 🔧


<img width="589" height="426" alt="Screenshot 2026-02-15 093202" src="https://github.com/user-attachments/assets/ed13f31f-3e9e-47e8-99aa-7fa9154272a8" />


<img width="1416" height="448" alt="Screenshot 2026-02-15 093555" src="https://github.com/user-attachments/assets/15d18885-4f96-4e8c-8f50-5dd40ace6949" />


<img width="1131" height="224" alt="Screenshot 2026-02-15 094402" src="https://github.com/user-attachments/assets/8358c8e9-ade1-4c00-bbb8-22cdf7d02884" />



### 14. SQL Server 2022 Installation on GF‑SQL01: 🗄️⚙️

With GF‑SQL01 fully joined to the domain and network addressing finalized, the next step was to deploy SQL Server 2022. This server will host the backend database required for the Greenfield Accountancy application stack, so the installation was performed using a clean, minimal, and production‑aligned configuration.

**Launching SQL Server Installation Center:**
I began by launching the SQL Server 2022 Installation Center and selecting **New SQL Server stand‑alone installation**.
This opened the SQL setup workflow and initiated the standard rule checks.

**Edition Selection:**
For this environment, I selected the Developer Edition.
This edition includes the full SQL feature set and is appropriate for non‑production lab environments while still mirroring enterprise deployments.

**Feature Selection:**
Only the required components were installed to keep the SQL footprint clean and aligned with real‑world onboarding practices:
- Database Engine Services
- SQL Server Browser (installed automatically)
- No additional services such as Analysis Services, Integration Services, or PolyBase were required for this deployment.

**Instance Configuration:**
GF‑SQL01 hosts a single SQL instance, so I deployed SQL Server as a **Default Instance (MSSQLSERVER)**.
This simplifies management and avoids unnecessary complexity in connection strings.

**Server Configuration:**
All SQL services were left on their recommended default service accounts, following Microsoft best practices:
- SQL Server Database Engine → NT Service\MSSQLSERVER
- SQL Server Agent → NT Service\SQLSERVERAGENT
- SQL Server Browser → NT AUTHORITY\LOCAL SERVICE
I also enabled:
- **Grant Perform Volume Maintenance Tasks privilege**
This enables Instant File Initialization (IFI), improving performance during database creation, growth, and restores.

**Database Engine Configuration:**
The Database Engine was configured using a secure and flexible authentication model:
Authentication Mode:
- Mixed Mode (Windows + SQL authentication)
SA Account

- A strong SA password was set for administrative fallback.
SQL Administrators

- My domain admin account was added as a SQL administrator.
This ensures full access via Windows Authentication and aligns with enterprise onboarding workflows.


**Installation Completion:**
SQL Server 2022 installed successfully with all selected features.
The summary screen confirmed a clean deployment with no warnings or failed components.


<img width="812" height="711" alt="Screenshot 2026-02-15 101507" src="https://github.com/user-attachments/assets/ace5d14f-6eb3-4ab7-98b7-6ec4d554ae28" />

**SQL Server Firewall Configuration (GF‑SQL01): 🔒🛡️**

With SQL Server 2022 successfully deployed, the next step was to ensure the server was accessible to domain‑joined clients and management tools in a secure and controlled manner.
In a production environment, SQL Server does not function correctly unless the appropriate inbound firewall rules are configured, as the Windows Firewall blocks SQL traffic by default.
To align GF‑SQL01 with real‑world enterprise practices, the following inbound rules were added using Windows Defender Firewall with Advanced Security:
Required SQL Ports:

1433 - TCP 
1434 - UDP 


These rules allow applications, administrative tools (such as SSMS), and future onboarding workloads to connect to the SQL instance securely.


<img width="1546" height="376" alt="Screenshot 2026-02-20 210259" src="https://github.com/user-attachments/assets/a162ce91-3be3-4157-ad30-b202a89336f8" />


<img width="851" height="299" alt="Screenshot 2026-02-20 210403" src="https://github.com/user-attachments/assets/94bc0fe7-c340-4a0a-818b-4bb7671f67a7" />

This configuration ensures that SQL Server remains secure while still being fully accessible to authorised systems within the Greenfield Accountancy hybrid environment.



### 15. Installing SQL Server Management Studio (SSMS): 🖥️📊

SQL Server Management Studio (SSMS) was installed using the Visual Studio Installer, which now distributes SSMS 22. This ensures the latest version is deployed with full SQL 2022 compatibility.


<img width="1287" height="716" alt="Screenshot 2026-02-15 102115" src="https://github.com/user-attachments/assets/5950acc1-7acb-4426-bffd-7f0f40a7c9f3" />


Once installed, SSMS was launched to perform the initial connection to the SQL instance.



### 16. First Connection to SQL Server: 🔐

When connecting to GF‑SQL01 for the first time, SQL Server enforced encrypted connections by default.
Because the server uses a self‑signed certificate, SSMS required:
- Trust server certificate → enabled
This behaviour is expected in internal lab environments.


<img width="485" height="587" alt="Screenshot 2026-02-15 102454" src="https://github.com/user-attachments/assets/cdc1138c-e51c-4e43-9efd-d68456888949" />


After enabling certificate trust and signing in using my domain admin account, the connection succeeded.



### 17. Successful SQL Server Connection: 🎉

Once authenticated, the SQL instance appeared in Object Explorer, confirming:
- SQL Server 2022 is running
- The instance is reachable
- Domain authentication is functioning
- SSMS is correctly installed and configured


<img width="1893" height="1005" alt="Screenshot 2026-02-15 103052" src="https://github.com/user-attachments/assets/f79614f6-d7bf-45ab-a588-5034a94f12c6" />


This completes the SQL Server installation and initial configuration phase. GF‑SQL01 is now fully prepared to host the Greenfield Accountancy application database.



### 18. Preparing the Greenfield Application Database: 🧱

With SQL Server installed, configured, and successfully connected via SSMS, the next step was to prepare the backend database that will support the Greenfield Accountancy application.

**Note on SQL Server Learning Approach:**

SQL Server is not an area I’ve previously worked with in depth, so I approached this part of the project as a structured learning exercise. To ensure the database was designed and configured correctly, I followed a combination of technical guides and reliable resources. This helped me validate each step, understand the workflow, and align the implementation with real‑world onboarding practices.
Using guided resources in this way reflects how modern engineers work: combining foundational knowledge with well‑established references to accelerate learning, reduce errors, and deliver accurate, production‑aligned outcomes.


**Creating the GreenfieldDB Database:**

With SSMS connected to GF‑SQL01, the first task was to create a dedicated database for the application:
- Database Name: GreenfieldDB
- Purpose: Store customer records, invoices, payments, and application‑level user data
- Location: Hosted on GF‑SQL01 under the default SQL Server instance
A new query window was opened, and the database was created using:

```
CREATE DATABASE GreenfieldDB;
GO
```

Once executed, the database appeared under Databases in Object Explorer, confirming successful creation.



**Creating the Application Schema:**

To keep the database organised and to separate application objects from system objects, an app schema was created:

```
USE GreenfieldDB;
GO
CREATE SCHEMA app;
GO
```

This schema will contain all tables related to the Greenfield Accountancy application.



**Creating SQL Logins for Departmental Users:**

Two fictional users — John Smith and Jane Smith — were created to represent staff within a department that requires direct SQL access for reporting and analysis.
Logins were created at the server level:

```
CREATE LOGIN JohnSmith WITH PASSWORD = 'P@ssw0rd123!';
CREATE LOGIN JaneSmith WITH PASSWORD = 'P@ssw0rd123!';
GO
```

These logins were then mapped to database users within ```GreenfieldDB```:

```
USE GreenfieldDB;
GO
CREATE USER JohnSmith FOR LOGIN JohnSmith;
CREATE USER JaneSmith FOR LOGIN JaneSmith;
GO
```


**Assigning Read‑Only Access:**

To follow least‑privilege principles, both users were granted read‑only access via the built‑in ```db_datareader``` role:

```
ALTER ROLE db_datareader ADD MEMBER JohnSmith;
ALTER ROLE db_datareader ADD MEMBER JaneSmith;
GO
```

This allows them to query data without modifying it — a common requirement for reporting teams.



***Creating the Application Tables:***

With the schema and users in place, the next step was to build the core tables required by the Greenfield Accountancy application. These were created inside the ``app`` schema.




**Customers Table:**

```
CREATE TABLE app.Customers (
    CustomerID INT IDENTITY(1,1) PRIMARY KEY,
    FirstName NVARCHAR(100) NOT NULL,
    LastName NVARCHAR(100) NOT NULL,
    Email NVARCHAR(255) UNIQUE,
    Phone NVARCHAR(50),
    CreatedAt DATETIME2 NOT NULL DEFAULT SYSDATETIME()
);
GO
```

**Invoices Table:**

```
CREATE TABLE app.Invoices (
    InvoiceID INT IDENTITY(1,1) PRIMARY KEY,
    CustomerID INT NOT NULL,
    InvoiceDate DATE NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    DueDate DATE NOT NULL,
    Amount DECIMAL(10,2) NOT NULL,
    Status NVARCHAR(50) NOT NULL DEFAULT 'Pending',
    CONSTRAINT FK_Invoices_Customers
        FOREIGN KEY (CustomerID) REFERENCES app.Customers(CustomerID)
);
GO
```

**Payments Table:**

```
CREATE TABLE app.Payments (
    PaymentID INT IDENTITY(1,1) PRIMARY KEY,
    InvoiceID INT NOT NULL,
    PaymentDate DATE NOT NULL DEFAULT CAST(GETDATE() AS DATE),
    Amount DECIMAL(10,2) NOT NULL,
    CONSTRAINT FK_Payments_Invoices
        FOREIGN KEY (InvoiceID) REFERENCES app.Invoices(InvoiceID)
);
GO
```

**Application Users Table:**


```
CREATE TABLE app.Users (
    UserID INT IDENTITY(1,1) PRIMARY KEY,
    Username NVARCHAR(100) NOT NULL UNIQUE,
    DisplayName NVARCHAR(200) NOT NULL,
    Role NVARCHAR(50) NOT NULL,
    CreatedAt DATETIME2 NOT NULL DEFAULT SYSDATETIME()
);
GO
```


**Populating the Database with Sample Data:**

To make the environment realistic and suitable for testing, sample data was inserted into each table.


**Customers:**

```
INSERT INTO app.Customers (FirstName, LastName, Email, Phone)
VALUES
('Michael', 'Anderson', 'michael.anderson@example.com', '555-1010'),
('Sarah', 'Thompson', 'sarah.thompson@example.com', '555-2020'),
('David', 'Reed', 'david.reed@example.com', '555-3030'),
('Emily', 'Clark', 'emily.clark@example.com', '555-4040');
GO
```


**Invoices:**


```
INSERT INTO app.Invoices (CustomerID, InvoiceDate, DueDate, Amount, Status)
VALUES
(1, '2026-01-10', '2026-02-10', 450.00, 'Pending'),
(1, '2026-01-15', '2026-02-15', 1200.00, 'Paid'),
(2, '2026-01-20', '2026-02-20', 300.00, 'Pending'),
(3, '2026-01-25', '2026-02-25', 750.00, 'Pending');
GO
```


**Payments:**


```
INSERT INTO app.Payments (InvoiceID, PaymentDate, Amount)
VALUES
(2, '2026-01-18', 1200.00),
(1, '2026-01-12', 200.00),
(1, '2026-01-20', 250.00);
GO
```


**Application Users:**


```
INSERT INTO app.Users (Username, DisplayName, Role)
VALUES
('admin', 'System Administrator', 'Administrator'),
('jdoe', 'John Doe', 'Staff'),
('asmith', 'Alice Smith', 'Manager');
GO
```


**Verifying the Database Structure:**

To confirm all tables were created successfully:

```
SELECT TABLE_SCHEMA, TABLE_NAME
FROM GreenfieldDB.INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = 'app';
```

All four tables appeared as expected:

- Customers
- Invoices
- Payments
- Users

This completes the database build phase.


<img width="1872" height="854" alt="Screenshot 2026-02-15 114514" src="https://github.com/user-attachments/assets/7b1ffdd7-a390-4ca2-921c-72949512f0d9" />



### 19. Deploying SSMS via Intune for SQL Administrative Users: 🛠️

With the GreenfieldDB backend created and validated, the next step was to prepare the SQL administrative environment. This involved creating dedicated SQL admin accounts in Active Directory, synchronising them to Entra ID, packaging SSMS for Intune deployment, and assigning the application to the SQL-Admins group so that SQL administrators receive SSMS automatically when signing in.
This mirrors a real onboarding workflow where administrative tools are deployed based on group membership rather than manual installation.

**Creating SQL Administrative Accounts in Active Directory:**

To support SQL Server administration, two dedicated SQL admin accounts were created in the on‑prem Active Directory:

• 	SQLAdmin01
• 	SQLAdmin02

These accounts were created under the Users container in ADUC.

A security group named SQL-Admins was also created to centrally manage access and application assignment. SQLAdmin01 and SQLAdmin02 were added as members of this group.


<img width="1467" height="344" alt="Screenshot 2026-02-15 143926" src="https://github.com/user-attachments/assets/0036518a-3540-4c32-8942-d0bc377ad912" />


<img width="1053" height="213" alt="Screenshot 2026-02-15 143940" src="https://github.com/user-attachments/assets/dfd742b8-e191-49ad-8e05-6f2be812a4f7" />


<img width="845" height="247" alt="Screenshot 2026-02-15 143953" src="https://github.com/user-attachments/assets/20562737-0078-4747-8479-3e658c69df6d" />



**Synchronising SQL Admin Accounts to Entra ID:**

Azure AD Connect was run to synchronise the new accounts and group to Entra ID.

Once the sync completed, the following objects appeared in Entra:
• 	SQLAdmin01
• 	SQLAdmin02
• 	SQL-Admins (Security Group, source: Windows Server AD)

This confirmed that the group could now be used for Intune application assignment.


<img width="1448" height="588" alt="Screenshot 2026-02-15 144433" src="https://github.com/user-attachments/assets/59f6f6ae-9c5b-4cbb-a295-d7e7300364b8" />


<img width="1800" height="638" alt="Screenshot 2026-02-15 144457" src="https://github.com/user-attachments/assets/c6e4c703-0dc7-4bf7-8b45-f378487a566a" />



**Preparing SSMS for Intune Deployment.**

All Intune application packaging is stored on GF‑DEPLOY01 under the B: drive, which also hosts PXE and deployment resources.
A clean folder structure was created to keep Intune and ConfigMgr application packaging organised:


<img width="1136" height="500" alt="Screenshot 2026-02-15 145411" src="https://github.com/user-attachments/assets/a77e63ed-34e9-43d6-9e9a-9afffbf4797c" />


<img width="1128" height="441" alt="Screenshot 2026-02-15 150152" src="https://github.com/user-attachments/assets/c08971c0-f8c9-4fdf-bbf3-5f4a461f83cd" />


The SSMS installer `SSMS-Setup-ENU.exe` was placed in the Intune Apps > Source folder.

```
B:\Intune Apps\
    └── SSMS\
         ├── Source
         └── Output
```



**Packaging SSMS Using the IntuneWinAppUtil Tool.**

To deploy SSMS via Intune, the installer must be converted into a package.

Using the Microsoft Win32 Content Prep Tool:
• 	Source folder: - `B:\Intune Apps\SSMS\Source`
• 	Setup file: `SSMS-Setup-ENU.exe`
• 	Output folder: `B:\Intune Apps\SSMS\Output`
Once the tool completed, the output folder contained:


<img width="1447" height="658" alt="Screenshot 2026-02-17 164931" src="https://github.com/user-attachments/assets/96244b69-e83c-49e7-829c-0a25a0226198" />


<img width="1058" height="445" alt="Screenshot 2026-02-17 165321" src="https://github.com/user-attachments/assets/86c8c33e-2527-47fd-94c1-60e072247d7c" />



**Uploading SSMS to Intune:**

With the package prepared, the next step was to upload and configure the application in Intune.

In the Intune admin center:
1. 	Navigate to Apps → Windows apps
2. 	Select Add
3. 	Choose Windows app (Win32)
4. 	Upload the  package
5. 	Configure install/uninstall commands
6. 	Set detection rules
7. 	Assign the app to the SQL-Admins group
This ensures that any device/user who is a member of SQL-Admins automatically receives SSMS on sign‑in.


<img width="1672" height="820" alt="Screenshot 2026-02-17 165547" src="https://github.com/user-attachments/assets/507c8515-a8f0-4856-84df-5bd90f40af52" />


**Confirmation of a successful deployment:**

I signed into both GF-WINCLIENT1 and GF-WINCLIENT2.
As expected:
• 	Intune detected group membership
• 	SSMS installed automatically
• 	No manual installation was required
This validated the deployment workflow and confirmed that the Intune Win32 app deployment was functioning correctly.


<img width="1715" height="852" alt="Screenshot 2026-02-17 165600" src="https://github.com/user-attachments/assets/28cd8b79-d2a2-4a53-b0a4-d2dbe0a710cd" />


<img width="1700" height="644" alt="Screenshot 2026-02-17 165618" src="https://github.com/user-attachments/assets/278aa78f-4907-42f1-b2a0-b613c6cbb0ee" />


<img width="1842" height="693" alt="Screenshot 2026-02-17 165630" src="https://github.com/user-attachments/assets/3a04b911-ec81-48e4-a78b-653c606cf6d3" />


<img width="1894" height="787" alt="Screenshot 2026-02-17 170103" src="https://github.com/user-attachments/assets/e7155da7-6261-4258-a912-dab333eb51c6" />


<img width="1835" height="862" alt="Screenshot 2026-02-17 170206" src="https://github.com/user-attachments/assets/863596fc-a525-4aab-b888-4cb8bed3dc64" />



### 20. Installing Microsoft Endpoint Configuration Manager (ConfigMgr): 🖥️

This section documents the installation and configuration of Microsoft Endpoint Configuration Manager within the Greenfield environment.
The installation was performed on GF‑DEPLOY01, with SQL hosted on GF‑SQL01, and this chapter explains the process as it was carried out.
Screenshots will be added afterwards to show the completed state of each stage.


**Preparing GF‑DEPLOY01 for ConfigMgr:**

Before running the ConfigMgr installer, GF‑DEPLOY01 was prepared with all required Windows Server roles and features.
These include IIS, BITS, .NET Framework, and other components required for the Management Point and Distribution Point roles.


**ConfigMgr Firewall Configuration (GF‑DEPLOY01): 🔒🛡️**

Before launching the ConfigMgr setup wizard, the Windows Firewall on GF‑DEPLOY01 was configured to allow inbound communication required by the Management Point (MP) and Distribution Point (DP) roles.
By default, Windows Server blocks these ports, which would prevent clients from communicating with the site.
To align with real‑world enterprise deployments, the following inbound rules were added using Windows Defender Firewall with Advanced Security:

Required ConfigMgr Ports:

`80 - TCP`
`443 - TCP`

These rules ensure that:

- clients can communicate with the Management Point
- Software Center can retrieve policy and application metadata
- the Distribution Point can serve content
- boundary groups function correctly
- client assignment to Site Code GAL succeeds


<img width="1522" height="389" alt="Screenshot 2026-02-20 210627" src="https://github.com/user-attachments/assets/ed36c06d-a8db-4846-82fe-1a646eef38b7" />


<img width="712" height="423" alt="Screenshot 2026-02-20 210747" src="https://github.com/user-attachments/assets/d9100812-33f2-473a-a958-700497c5fb7d" />


With the firewall configured, GF‑DEPLOY01 was fully prepared for the ConfigMgr installation.



**SQL Server Configuration (GF‑SQL01):**

ConfigMgr relies heavily on SQL Server, so GF‑SQL01 was configured to meet all requirements.

The following settings were applied:

- TCP/IP enabled for remote connectivity
- SQL memory limits adjusted to prevent resource exhaustion
- Correct collation (SQL_Latin1_General_CP1_CI_AS) verified


<img width="705" height="521" alt="image" src="https://github.com/user-attachments/assets/96c1eadd-532a-4a98-bf31-121028f98f8e" />


<img width="702" height="657" alt="Screenshot 2026-02-19 143127" src="https://github.com/user-attachments/assets/a3b5cb69-ab1f-486e-983f-1d847228a3ca" />


<img width="705" height="521" alt="Screenshot 2026-02-19 143152" src="https://github.com/user-attachments/assets/078a9acb-f3f1-4de5-88da-ac356357a466" />


These settings ensure SQL is fully compatible with ConfigMgr.



**Active Directory Integration:**

The AD schema was extended using the ConfigMgr setup files.
After this, the System Management container was created under `CN=System` and delegated to `GF‑DEPLOY01`.
This allows ConfigMgr to publish site information into Active Directory.


<img width="872" height="386" alt="Screenshot 2026-02-19 143742" src="https://github.com/user-attachments/assets/3aa0c1ab-51a7-4317-97ea-d36c68747bd7" />


<img width="967" height="529" alt="Screenshot 2026-02-19 144023" src="https://github.com/user-attachments/assets/2b5b9c63-892b-49b0-8b8f-9e2bd45bdb98" />


<img width="1653" height="915" alt="Screenshot 2026-02-17 193837" src="https://github.com/user-attachments/assets/8439c411-0164-45d4-b669-0a3578280ae4" />



**Installing the GAL Primary Site:**

With prerequisites complete, the ConfigMgr setup wizard was launched on GF‑DEPLOY01.
The following configuration was applied:
- Site Type: `Primary Site`
- Site Code: `GAL`
- Site Name: `Greenfield`
- SQL Server: `GF‑SQL01`
- Database: `CM_GAL`
- Install Directory: `E:\SCCM `
- Management Point: `GF‑DEPLOY01`
- Distribution Point: `GF‑DEPLOY01`


<img width="744" height="558" alt="Screenshot 2026-02-17 230057" src="https://github.com/user-attachments/assets/7c6b8951-19be-48fd-85f8-7343cf77b88b" />


**Post‑Installation Validation:**

After installation, the following checks were performed to confirm the GAL site is healthy and operational.

**Site Configuration**

ConfigMgr Console → Administration → Site Configuration → Sites
(Showing Site Code GAL, Site Name Greenfield, Server GF‑DEPLOY01

<img width="1498" height="541" alt="Screenshot 2026-02-19 144449" src="https://github.com/user-attachments/assets/0215a856-5157-4eb9-9498-8466d841381b" />



**Site Status**

Monitoring → System Status → Site Status


<img width="1331" height="515" alt="Screenshot 2026-02-20 202620" src="https://github.com/user-attachments/assets/ab1430d5-2f0a-4a55-9c78-2154c93cf85d" />


**Management Point Health**

Monitoring → Component Status → SMS_MP_CONTROL_MANAGER = OK


<img width="1796" height="782" alt="Screenshot 2026-02-20 203241" src="https://github.com/user-attachments/assets/edacc5d7-cbd5-43b3-a67b-9d076719f5b2" />



**Distribution Point Health**

Monitoring → Distribution Point Configuration Status → Success
These checks confirm the core site roles are functioning correctly.


<img width="1388" height="345" alt="Screenshot 2026-02-20 203541" src="https://github.com/user-attachments/assets/c3ad34c5-4fb0-4920-864d-1d492bd64439" />


**Distribution Point & Content Library:**

ConfigMgr automatically creates several system‑critical directories at the root of the deployment drive (B:).
These folders are required for the Distribution Point and Content Library and must remain at the root.

The following folders were created:

- `SCCMContentLib`
- `SMSPKG`
- `SMSPKGB$`
- `SMSPKGSIG`
- `SMSSIGS`
- `SMS_DPS`
- The `ConfigMgr` installation directory


E: Install
B: Distrubution and Content Library


<img width="884" height="498" alt="Screenshot 2026-02-20 203746" src="https://github.com/user-attachments/assets/e438be5c-8783-439b-bfe4-2a953eb50597" />


<img width="899" height="417" alt="Screenshot 2026-02-20 203735" src="https://github.com/user-attachments/assets/acd5f8cd-3e95-4864-814a-efac3a6d42be" />



**Boundaries & Boundary Groups:**

A boundary was created to define the Greenfield network space, and a Boundary Group was configured to assign clients to the GAL site and provide content location services.


**Application Deployment Validation (Software Center Test):**

After confirming that the Management Point, Distribution Point, boundaries, and client assignment were all healthy, a test application deployment was performed to validate end‑to‑end functionality.

Software Center was installed on `GF‑WINCLIENT1` and `GF‑WINCLIENT2`, and `VLC Media Player` was deployed using the official MSI installer.

The application appeared in Software Center on both clients and installed successfully.

This confirmed that:
- the Distribution Point is distributing content correctly
- the Management Point is communicating with clients
- boundaries and boundary groups are configured properly
- clients are assigned to Site Code GAL
- MSI‑based application deployment is fully operational

This validated that the ConfigMgr environment is functioning as intended.


<img width="1426" height="702" alt="Screenshot 2026-02-20 204337" src="https://github.com/user-attachments/assets/cdb4b088-7704-4f50-ae70-e6a19292bb38" />


<img width="1422" height="738" alt="Screenshot 2026-02-20 204357" src="https://github.com/user-attachments/assets/5d9f4587-5253-4e8c-80d7-61a7f053f43f" />



###21. Deploying and Validating the Greenfield SQL Connectivity Test Application: 🧪💻


With SQL Server fully deployed and the GreenfieldDB schema in place, the next step was to validate that a domain‑joined client could authenticate to the database using Windows Integrated Authentication.

To achieve this, I created a lightweight .NET console application designed specifically for connectivity testing within the Greenfield Accountancy environment.



**Creating the Application:**

I developed a small .NET 10 console application named Greenfield Dummy App.

The purpose of this application is intentionally narrow and controlled: it executes a single predefined SQL query against the app.Customers table to confirm that:

- The client can reach `GF‑SQL01`
- Windows authentication is functioning
- The logged‑in domain user has the correct SQL permissions
- The database and schema are accessible
- The .NET runtime is installed and working on the client

The application is not interactive and does not accept custom SQL input.
This design ensures predictable, repeatable validation aligned with real‑world onboarding workflows.


<img width="1215" height="734" alt="Screenshot 2026-02-20 213917" src="https://github.com/user-attachments/assets/28337d93-e41e-4e61-a428-d0146b1d50fa" />


<img width="1494" height="769" alt="Screenshot 2026-02-20 214117" src="https://github.com/user-attachments/assets/e93112bd-68cf-4aaf-b2e4-7ef560280045" />


<img width="1298" height="490" alt="Screenshot 2026-02-20 214215" src="https://github.com/user-attachments/assets/a54b1d96-efbb-4fde-9b96-26c24e7e9371" />


<img width="1150" height="613" alt="Screenshot 2026-02-20 214322" src="https://github.com/user-attachments/assets/90d5770b-ecad-4cd3-8a87-925addcf14df" />


Once compiled, the application produced the following output files:

- `GreenfieldApp.exe`
- `GreenfieldApp.dll`
- `GreenfieldApp.runtimeconfig.json`
- `GreenfieldApp.deps.json`

These were published into a dedicated application source folder for deployment.

<img width="1603" height="606" alt="Screenshot 2026-02-20 223447" src="https://github.com/user-attachments/assets/c37b3768-d9ed-44bc-ba3d-4fc659dd176a" />



**Packaging and Deployment via ConfigMgr:**

To mirror enterprise deployment practices, the application was packaged and deployed using Microsoft Endpoint Configuration Manager.

Deployment steps included:

- Creating a new Application in ConfigMgr
- Adding a Deployment Type pointing to the published source folder
- Configuring the install command to copy the application into: ```C:\Program Files\GreenfieldApp```
- Creating a file‑based detection rule targeting GreenfieldApp.exe
- Distributing the content to the Distribution Point
- Deploying the application to the test device collection containing `GF‑WINCLIENT01`

Because the application targets .NET 10, the .NET Desktop Runtime 10 was also installed on the client to ensure compatibility.
Once deployed, the application appeared in Software Center and installed successfully.


<img width="1412" height="396" alt="Screenshot 2026-02-20 215146" src="https://github.com/user-attachments/assets/565a3243-5c3c-41e0-8ca9-cc7c03e57dbc" />


<img width="1395" height="682" alt="Screenshot 2026-02-20 215258" src="https://github.com/user-attachments/assets/5f03d77b-4276-4e34-a8fe-2c91b902b1fe" />


<img width="1721" height="771" alt="Screenshot 2026-02-20 215407" src="https://github.com/user-attachments/assets/50245ef9-8005-49d4-a62f-ba9a57a447b6" />




**Configuring SQL Permissions: 🔐**


Since the application uses Integrated Security, SQL Server authenticates the currently logged‑in Windows user.
For this test, the user logged into GF‑WINCLIENT01 was: `GREENFIELDACCOU\JaneSmith`


To allow the application to run its predefined query, I added this user as a Windows login in SQL Server and mapped it to the GreenfieldDB database with the following role:

- `db_datareader`

This provides read‑only access to the app.Customers table, which is sufficient for connectivity validation.


<img width="1483" height="754" alt="Screenshot 2026-02-20 223706" src="https://github.com/user-attachments/assets/6167eac5-469c-45c2-b6b0-0ed4e600523f" />



**Running the Application on GF‑WINCLIENT01:**


With deployment and permissions complete, I launched the application from:

```C:\Program Files\GreenfieldApp\GreenfieldApp.exe```


The application successfully:

- Connected to `GF‑SQL01`
- Authenticated using the logged‑in Windows identity
- Executed the predefined SELECT query
- Returned the expected dummy customer records seeded during database creation

This confirmed that SQL connectivity, authentication, permissions, and application deployment were all functioning correctly.


<img width="1837" height="672" alt="Screenshot 2026-02-20 222434" src="https://github.com/user-attachments/assets/410dc7d4-526e-4a6b-963e-183e57f06ba7" />



**Outcome: 🎉**

The Greenfield Dummy App performed exactly as intended.
By returning the predefined dataset, it validated:

- Network reachability
- SQL Server availability
- Correct domain authentication
- Proper SQL permissions
- Successful ConfigMgr deployment
- Correct .NET runtime configuration

This completes the SQL connectivity validation phase and confirms that the Greenfield Accountancy environment is fully integrated end‑to‑end.



###22. Final Summary & Outcomes: 🏁📘

The Greenfield Accountancy hybrid environment project successfully delivered a fully integrated, enterprise‑grade infrastructure that mirrors real‑world onboarding, identity, deployment, and application delivery workflows. This environment was built from the ground up using industry‑standard technologies, including Active Directory, Microsoft Entra ID, Intune, SQL Server, and Microsoft Endpoint Configuration Manager (ConfigMgr), and demonstrates a complete end‑to‑end lifecycle for device provisioning, identity synchronization, application deployment, and backend system integration.


**End‑to‑End Infrastructure Achievements**


**On‑Premises Core Services:**

A complete on‑premises foundation was deployed, including:

- Windows Server 2022 Domain Controller
- DNS, DHCP, and AD DS
- WDS for automated Windows 11 deployment
- A structured OU hierarchy for users and devices
- Group Policy for desktop standardisation and security

This provided a stable, enterprise‑aligned base for hybrid identity and device management.


**Hybrid Identity Integration:**

Microsoft Entra Connect was deployed to synchronize on‑premises identities and devices to the cloud, enabling:

- Password Hash Synchronization
- Hybrid Entra ID Join
- Group Writeback
- Automatic Intune enrollment

This created a seamless hybrid identity model consistent with modern enterprise environments.


**Intune Device Management:**

Intune was configured to manage domain‑joined Windows 11 clients, providing:

- Automatic MDM enrollment
- Compliance evaluation
- Application deployment
- Device visibility and reporting

This validated the full hybrid management lifecycle.


**SQL Server Deployment & Application Backend:**

A dedicated SQL Server 2022 instance was deployed on GF‑SQL01, including:

- Database Engine configuration
- Mixed‑mode authentication
- Firewall hardening
- SSMS installation
- Creation of the GreenfieldDB database
- Application schema, tables, and sample data

This provided a realistic backend for application testing and onboarding workflows.


**ConfigMgr Deployment & Application Delivery:**

Microsoft Endpoint Configuration Manager was installed on GF‑DEPLOY01, with:

- Primary Site (GAL)
- Management Point
- Distribution Point
- Boundaries and Boundary Groups
- Content Library configuration
- MSI‑based application deployment validation

This enabled enterprise‑grade Win32 application deployment and lifecycle management.


**Custom .NET Application Integration:**

A lightweight .NET 10 console application was developed to validate SQL connectivity using:

- Windows Integrated Authentication
- Predefined SQL queries
- Controlled, non‑interactive execution
- Deployment via ConfigMgr
- SQL permissions configured for domain users

This confirmed end‑to‑end integration between:

- Domain identity
- Device management
- SQL Server
- Application deployment
- Network connectivity
- Permissions and access control



**Skills Demonstrated:**

This project demonstrates hands‑on capability across a wide range of enterprise technologies:

- Active Directory architecture & administration
- Hybrid identity (Entra Connect, HAADJ)
- Intune MDM & Win32 app deployment
- ConfigMgr installation, configuration, and application delivery
- SQL Server installation, configuration, and database design
- Firewall and network configuration
- Automated OS deployment (WDS)
- .NET application development and SQL integration
- End‑to‑end troubleshooting and validation
- Enterprise documentation and workflow design


**Project Outcome: 🎉🎉🎉🎉**

The Greenfield Accountancy environment now represents a fully functional, production‑aligned hybrid infrastructure capable of:

- Deploying Windows devices automatically
- Synchronizing identities between on‑prem and cloud
- Managing devices through Intune and ConfigMgr
- Hosting enterprise SQL workloads
- Delivering applications through modern and traditional channels
- Validating backend connectivity through a custom application
- Demonstrating real‑world onboarding workflows from start to finish

This project showcases a complete, modern IT ecosystem — designed, deployed, validated, and documented to professional standards.
