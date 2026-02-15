## 🚀 Enterprise SQL Deployment & Application Onboarding Framework for Hybrid Environments 🚀


## 🧩 Virtual Machine (VM) and Network Setup

o simulate a customer’s IT environment for an enterprise application deployment, I built a dedicated virtual lab using Hyper‑V on my host PC. This provided a controlled hybrid environment where I could test the full SQL Server installation, configuration, and client‑side onboarding workflow end‑to‑end.

A fictional organisation, GreenfieldAccountancyLtd, was created to represent a typical mid‑sized accountancy firm. Two standard user accounts — John Smith and Jane Smith — were added to simulate real staff identities and to model how departmental users would interact with the system during an onboarding engagement.


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
* **Licensing:** **Microsoft 365 E3** licenses were assigned to **`Jane Smith`** and **`John Smith`** for testing to meet the Intune management requirement.
* **GPO Timing Fix (Critical):** The **`Always wait for the network at computer startup and logon`** GPO was **Enabled** within the **Default Domain Policy** to prevent GPO failure due to network initialization timing issues.

<img width="1887" height="858" alt="Screenshot 2026-02-14 210341" src="https://github.com/user-attachments/assets/f9031396-852f-40cf-9820-06d032f5430b" />

<img width="1323" height="723" alt="Screenshot 2026-02-14 210551" src="https://github.com/user-attachments/assets/94a0ddfe-a1fd-4255-b047-0f531ff86f1a" />

---

### **11. Group Policy Management: Automatic Intune Enrollment** ☁️

The GPO was configured to initiate the successful automatic enrollment of domain-joined devices into Microsoft Intune.

* **GPO Name:** `Intune Auto Enroll`
* **GPO Scope:** Linked directly to the **`Devices`** child OU.
* **Policy Path:** **Computer Configuration** $\rightarrow$ **Policies** $\rightarrow$ **Administrative Templates** $\rightarrow$ **Windows Components** $\rightarrow$ **MDM**
* **Configuration:** The setting **`Enable automatic MDM enrollment using default Azure AD credentials`** was set to **Enabled** with the credential type set to **`User Credential`**. The **MDM Application ID** was correctly left **blank**.

<img width="1907" height="995" alt="Screenshot 2026-02-15 074816" src="https://github.com/user-attachments/assets/85c91420-2397-4b23-8421-2b29a9f659c1" />


---


### **12. Verification: Intune Enrollment and Management** ✅

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


---

### **13. SQL Server Host Preparation (GF‑SQL01)** 🗄️✨

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

---

### 14. SQL Server 2022 Installation on GF‑SQL01 🗄️⚙️

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

---


### 15. Installing SQL Server Management Studio (SSMS) 🖥️📊

SQL Server Management Studio (SSMS) was installed using the Visual Studio Installer, which now distributes SSMS 22. This ensures the latest version is deployed with full SQL 2022 compatibility.

<img width="1287" height="716" alt="Screenshot 2026-02-15 102115" src="https://github.com/user-attachments/assets/5950acc1-7acb-4426-bffd-7f0f40a7c9f3" />

Once installed, SSMS was launched to perform the initial connection to the SQL instance.

---

### 16. First Connection to SQL Server 🔐
When connecting to GF‑SQL01 for the first time, SQL Server enforced encrypted connections by default.
Because the server uses a self‑signed certificate, SSMS required:
- Trust server certificate → enabled
This behaviour is expected in internal lab environments.

<img width="485" height="587" alt="Screenshot 2026-02-15 102454" src="https://github.com/user-attachments/assets/cdc1138c-e51c-4e43-9efd-d68456888949" />


After enabling certificate trust and signing in using my domain admin account, the connection succeeded.

---

### 17. Successful SQL Server Connection 🎉

Once authenticated, the SQL instance appeared in Object Explorer, confirming:
- SQL Server 2022 is running
- The instance is reachable
- Domain authentication is functioning
- SSMS is correctly installed and configured

<img width="1893" height="1005" alt="Screenshot 2026-02-15 103052" src="https://github.com/user-attachments/assets/f79614f6-d7bf-45ab-a588-5034a94f12c6" />

This completes the SQL Server installation and initial configuration phase. GF‑SQL01 is now fully prepared to host the Greenfield Accountancy application database.

---

### 18. Preparing the Greenfield Application Database 🧱

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

