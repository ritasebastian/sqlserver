### **🔹 Required Tools for Installing SQL Server, Always On Availability Groups (AG), and Failover Clustering (FCI) in Windows Server**
To set up **SQL Server with Always On Availability Groups (AG) and Failover Clustering (FCI)** in a **Windows Server environment**, you need to install multiple components.

---

## **1️⃣ Required Tools & Components for SQL Server Always On & Failover Clustering**
| **Tool/Component** | **Usage** | **Required For?** |
|------------------|----------|----------------|
| **SQL Server Database Engine** | ✅ Core SQL Server service for data storage & transactions. | ✅ Always On AG ✅ Failover Cluster |
| **SQL Server Management Studio (SSMS)** | ✅ GUI to manage databases, clusters, and Always On configurations. | ✅ Always On AG ✅ Failover Cluster |
| **SQL Server Agent** | ✅ Runs scheduled jobs like backups, failovers, and monitoring. | ✅ Always On AG ✅ Failover Cluster |
| **Windows Server Failover Clustering (WSFC)** | ✅ Provides failover support for SQL Server HA. | ✅ Always On AG ✅ Failover Cluster |
| **SQL Server Configuration Manager** | ✅ Manages SQL Server network protocols & services. | ✅ Always On AG ✅ Failover Cluster |
| **SQLCMD (Command Line Tool)** | ✅ Used to connect & manage SQL Server instances from CLI. | ✅ Always On AG ✅ Failover Cluster |
| **PowerShell Modules for SQL Server** | ✅ Automates SQL Server & WSFC configuration. | ✅ Always On AG ✅ Failover Cluster |
| **Windows Admin Center (Optional)** | ✅ Helps manage WSFC & SQL Server remotely. | ✅ Always On AG ✅ Failover Cluster |
| **Active Directory & DNS Server** | ✅ Required for authentication & name resolution. | ✅ Always On AG ✅ Failover Cluster |
| **File Share Witness or Cloud Witness (Optional)** | ✅ Ensures quorum availability in a 2-node cluster. | ✅ Always On AG ✅ Failover Cluster |

---

## **2️⃣ Installation Steps for SQL Server Always On & Failover Clustering**
### **✅ Step 1: Install Windows Server Failover Clustering (WSFC)**
1️⃣ Open **Server Manager** → Click **Add Roles & Features**.  
2️⃣ Select **Failover Clustering** → Click **Install**.  
3️⃣ After installation, open **Failover Cluster Manager** (`cluadmin.msc`).  

#### **Verify WSFC Installation**
Run this PowerShell command:
```powershell
Get-WindowsFeature -Name Failover-Clustering
```
✔ Expected output: **Installed ✅**

---

### **✅ Step 2: Install SQL Server Database Engine**
1️⃣ **Run SQL Server Setup** (`setup.exe`).  
2️⃣ Select **New SQL Server Standalone Installation**.  
3️⃣ Enable **Database Engine Services** & **SQL Server Agent**.  
4️⃣ Configure SQL Server service accounts (use **Domain Service Accounts**).  
5️⃣ Complete installation & restart the server.

#### **Verify SQL Server Installation**
Run this query in SSMS:
```sql
SELECT @@VERSION;
```
✔ Expected output: **SQL Server Installed ✅**

---

### **✅ Step 3: Enable Always On Availability Groups**
1️⃣ Open **SQL Server Configuration Manager**.  
2️⃣ Navigate to **SQL Server Services**.  
3️⃣ Right-click **SQL Server (MSSQLSERVER)** → Click **Properties**.  
4️⃣ Go to **Always On High Availability Tab** → Check **Enable Always On Availability Groups**.  
5️⃣ Restart SQL Server service.

#### **Verify Always On is Enabled**
Run this query in SSMS:
```sql
SELECT SERVERPROPERTY('IsHadrEnabled');
```
✔ Expected output: **1 (Enabled) ✅**

---

### **✅ Step 4: Configure Failover Clustering (FCI)**
1️⃣ Open **Failover Cluster Manager**.  
2️⃣ Click **Create Cluster** → Add your SQL Servers.  
3️⃣ Configure **Cluster Name & IP Address**.  
4️⃣ Add **Shared Storage (SAN, iSCSI, SMB Share)**.  
5️⃣ Install **SQL Server in Failover Cluster Mode**.

#### **Check Cluster Node Status**
Run this PowerShell command:
```powershell
Get-ClusterNode
```
✔ Expected output: **Cluster Nodes Active ✅**

---

### **✅ Step 5: Set Up File Share Witness (For 2-Node Cluster)**
1️⃣ Create a shared folder (`\\DC01\SQLWitness`).  
2️⃣ Run this command to set up the quorum:
```powershell
Set-ClusterQuorum -FileShareWitness "\\DC01\SQLWitness"
```

✔ **Ensures cluster stays online if one node fails.**

---

### **✅ Step 6: Set Up Availability Group & Listener**
1️⃣ Open **SQL Server Management Studio (SSMS)**.  
2️⃣ Navigate to **Always On High Availability → New Availability Group Wizard**.  
3️⃣ Select **Databases** to include.  
4️⃣ Configure **Primary & Secondary Replicas**.  
5️⃣ Set **Automatic Failover** (if using **Synchronous Mode**).  
6️⃣ Configure **AG Listener (DNS Name + Virtual IP Address)**.  
7️⃣ Click **Finish** → SQL Server will sync the replicas.

#### **Check AG Sync Status**
Run this query in SSMS:
```sql
SELECT role_desc, synchronization_health_desc 
FROM sys.dm_hadr_availability_replica_states;
```
✔ Expected output: **SYNCHRONIZED ✅**

---

## **3️⃣ Summary: What Tools You Need for SQL Server Always On & FCI**
| **Component** | **Usage** | **Required?** |
|--------------|----------|-------------|
| **Windows Server Failover Clustering (WSFC)** | Required for failover | ✅ Yes |
| **SQL Server Database Engine** | Core SQL Server service | ✅ Yes |
| **SQL Server Management Studio (SSMS)** | GUI for database management | ✅ Yes |
| **SQL Server Agent** | Schedules jobs for maintenance & failover | ✅ Yes |
| **SQLCMD (Command Line Tool)** | CLI for SQL Server | ✅ Yes |
| **PowerShell Modules for SQL** | Automates AG & cluster setup | ✅ Yes |
| **Active Directory & DNS Server** | Required for authentication & HA | ✅ Yes |
| **File Share Witness (FSW)** | Ensures quorum in a 2-node cluster | ✅ Yes |

🚀 **Final Answer:** **To set up SQL Server Always On and Failover Clustering, you need WSFC, SQL Server, SSMS, SQLCMD, Active Directory, and a File Share Witness.**  

Hope this helps! Let me know if you need more details. 😊🚀
