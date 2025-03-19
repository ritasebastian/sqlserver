### **🔹 Major Issues in SQL Server Always On & Failover Cluster (FCI) and How to Resolve Them**
Here’s a list of **common issues** encountered in **Always On Availability Groups (AGs) and Failover Cluster Instances (FCI)**, along with **troubleshooting steps and resolutions**.

---

## **1️⃣ Common Issues in SQL Server Always On Availability Groups (AGs)**
| **Issue** | **Cause** | **How to Resolve?** |
|-----------|----------|---------------------|
| **Secondary Replica Not Synchronizing** | - Network latency <br> - Asynchronous mode | - Check AG synchronization mode (`sys.dm_hadr_database_replica_states`) <br> - Verify network speed <br> - Restart `SQLServerAgent` |
| **Automatic Failover Not Working** | - Secondary is not in synchronous mode <br> - No quorum | - Check failover mode: `SELECT * FROM sys.availability_replicas` <br> - Ensure **quorum is online** (`Get-ClusterNode`) |
| **Database in Restoring State on Secondary** | - Incorrect `RESTORE WITH NORECOVERY` | - Re-restore database with full backup using `WITH NORECOVERY` |
| **Data Latency in Readable Secondary** | - Read-Only routing not set <br> - Long-running queries | - Configure **Read-Only Routing** (`sys.availability_read_only_routing_lists`) <br> - Tune queries |
| **AG Listener Not Redirecting Connections** | - DNS cache issue <br> - Incorrect `ApplicationIntent=ReadOnly` | - Flush DNS: `ipconfig /flushdns` <br> - Ensure correct **connection string** |
| **Quorum Lost – AG Becomes Unavailable** | - Odd number of quorum votes not maintained | - Add a **File Share Witness** (`Set-ClusterQuorum`) |
| **Automatic Seeding Fails** | - Low disk space <br> - Network failure | - Check `sys.dm_hadr_physical_seeding_stats` for errors <br> - Increase disk space |
| **High Log File Growth on Primary** | - Secondary disconnected <br> - Long-running transactions | - Check `sys.dm_hadr_database_replica_states` for lag <br> - Manually truncate logs |

🔹 **Best Practice:** Always monitor AG status using  
```sql
SELECT * FROM sys.dm_hadr_availability_replica_states;
```

---

## **2️⃣ Common Issues in SQL Server Failover Cluster Instances (FCI)**
| **Issue** | **Cause** | **How to Resolve?** |
|-----------|----------|---------------------|
| **Cluster Failover Takes Too Long** | - Cluster service delay <br> - SQL instance restart time | - Check **failover logs** in WSFC (`Get-ClusterLog`) <br> - Optimize `SQL Server startup parameters` |
| **SQL Server Fails to Start After Failover** | - SQL Service Account does not have permissions | - Grant **Log on as a Service** rights to `SQLServerAgent` |
| **Virtual SQL Server Name Not Available** | - DNS issue <br> - Network failure | - Check **Cluster Name Object (CNO)** permissions in AD |
| **Failover Cluster Quorum Lost** | - Node failure <br> - Quorum misconfiguration | - Add a **File Share Witness or Cloud Witness** (`Set-ClusterQuorum`) |
| **Disk Not Coming Online After Failover** | - Shared disk issue | - Run `diskmgmt.msc` and check **SAN storage** |
| **Clients Not Connecting After Failover** | - SQL Virtual Network Name (VNN) issue <br> - Application connection timeout | - Check **listener and failover IP** using `nslookup SQLListener` <br> - Increase **timeout settings in connection string** |
| **SQL Agent Jobs Fail After Failover** | - Jobs pointing to old instance name | - Modify job server reference using:  
```sql
UPDATE msdb.dbo.sysjobs SET originating_server = 'NewServer';
```
| **FCI Stuck in Pending State** | - Windows Cluster issue | - Restart Cluster Service:  
```powershell
Restart-Service ClusSvc
```

---

## **3️⃣ Key Troubleshooting Commands**
### **✅ Check AG Replica Status**
```sql
SELECT replica_server_name, role_desc, synchronization_health_desc 
FROM sys.dm_hadr_availability_replica_states;
```

### **✅ Check Cluster Quorum Votes**
```powershell
Get-ClusterNode | Format-Table Name, State, NodeWeight
```

### **✅ Check Failover Logs in Windows Failover Cluster**
```powershell
Get-ClusterLog -Node Node1 -TimeSpan 5
```

### **✅ Restart Cluster Service in Case of Failover Issues**
```powershell
Restart-Service ClusSvc
```

---

## **4️⃣ Summary: Always On vs. Failover Cluster Issues**
| Issue Type | **Always On (AGs)** | **Failover Cluster (FCI)** |
|------------|------------------|------------------|
| **Failover Speed** | ✅ Fast (Seconds) | ❌ Slow (Minutes) |
| **Quorum Dependency** | ✅ Yes (WSFC) | ✅ Yes (WSFC) |
| **Read-Only Secondary Issues?** | ✅ Yes | ❌ No |
| **Disk Dependencies?** | ❌ No | ✅ Yes (Shared Storage) |
| **Application Redirect Issues?** | ✅ Yes (Listener) | ✅ Yes (VNN) |

---

## **🚀 Final Recommendations**
✔ **For Always On AGs:** Always configure **Quorum**, **Read-Only Routing**, and **Auto-Seeding** correctly.  
✔ **For Failover Clusters (FCI):** Ensure **Cluster Network Name (CNO)** and **shared storage** are configured properly.  
✔ **For Both:** **Monitor logs** (`sys.dm_hadr_availability_replica_states`, `Get-ClusterLog`).  

🚀 **Always On AG = Best for Readable Secondaries** | **FCI = Best for Full Instance HA**  

### **🔹 More Major Issues in Always On Availability Groups (AG) & Failover Clusters (FCI) and How to Resolve Them**

Here are **additional critical issues** that you might face in **SQL Server Always On AGs and Failover Clusters**, along with **detailed resolutions**.

---

## **1️⃣ Additional SQL Server Always On Availability Group (AG) Issues**
| **Issue** | **Possible Causes** | **Resolution Steps** |
|-----------|---------------------|----------------------|
| **Data Mismatch Between Primary and Secondary** | - Secondary Replica lagging behind <br> - Network latency | - Check synchronization status using:  
```sql
SELECT * FROM sys.dm_hadr_database_replica_states;
```  
- Manually **resume data movement**:  
```sql
ALTER DATABASE MyDB SET HADR RESUME;
```
| **AG Fails to Create Listener** | - Listener name conflict in DNS <br> - Permissions issue on cluster | - Check **DNS records**: `nslookup ListenerName` <br> - Verify **Cluster Name Object (CNO) permissions** in Active Directory |
| **AG Synchronous Mode Still Shows Data Loss Risk** | - Transaction logs accumulating <br> - Secondary not responding | - Check log send queue size:  
```sql
SELECT * FROM sys.dm_hadr_database_replica_states;
```  
- Restart SQL Server Agent on secondary |
| **Backup on Secondary Replica Fails** | - Incorrect backup preference setting <br> - Secondary is not readable | - Check **backup preferences** in AG settings <br> - Run:  
```sql
SELECT backup_preference_desc FROM sys.availability_groups;
```
| **AG Automatic Failover Delayed** | - Quorum voting delay <br> - WSFC misconfiguration | - Run:  
```powershell
Get-ClusterQuorum
```  
- Check logs in WSFC: `Get-ClusterLog -TimeSpan 5` |
| **Read-Only Routing Not Working** | - Incorrect routing list configuration <br> - Connection string missing `ApplicationIntent=ReadOnly` | - Configure routing list:  
```sql
ALTER AVAILABILITY GROUP MyAG 
MODIFY REPLICA ON 'Replica2' 
WITH (READ_ONLY_ROUTING_LIST = ('Replica1','Replica3'));
```  
- Use correct connection string:  
```plaintext
Server=MyAGListener; Database=MyDB; ApplicationIntent=ReadOnly;
```
| **High Transaction Log Growth on Primary** | - Secondary replica disconnected | - Check log file growth:  
```sql
SELECT log_send_queue_size FROM sys.dm_hadr_database_replica_states;
```  
- Manually truncate logs:  
```sql
BACKUP LOG MyDB TO DISK = 'NUL';
```

---

## **2️⃣ Additional SQL Server Failover Cluster Instance (FCI) Issues**
| **Issue** | **Possible Causes** | **Resolution Steps** |
|-----------|---------------------|----------------------|
| **Failover Cluster Node Fails to Join Cluster** | - Network connectivity issue <br> - Node eviction | - Check cluster health:  
```powershell
Get-ClusterNode
```  
- Try **re-adding the node**:  
```powershell
Add-ClusterNode -Name Node2
```
| **SQL Server Virtual Network Name (VNN) Not Available After Failover** | - DNS record not updated <br> - Network adapter issues | - Flush DNS cache:  
```powershell
ipconfig /flushdns
```  
- Restart cluster service:  
```powershell
Restart-Service ClusSvc
```
| **Failover Cluster Takes Too Long to Fail Over** | - SQL Server service restart delay <br> - Storage failover issues | - Optimize startup parameters (`-T845`) for **fast SQL boot** <br> - Check WSFC logs:  
```powershell
Get-ClusterLog
```
| **SQL Server FCI Keeps Failing Back to Old Node** | - Automatic failback enabled | - Disable automatic failback in WSFC:  
```powershell
(Get-ClusterGroup "SQL Server (MSSQLSERVER)").FailbackType = 0
```
| **Cluster Quorum Keeps Losing Votes** | - Odd number of quorum votes not maintained | - Use **File Share Witness or Cloud Witness**:  
```powershell
Set-ClusterQuorum -FileShareWitness "\\FS01\Witness"
```
| **Storage (SAN) Not Mounting After Failover** | - Disk paths not properly assigned | - Check disk status:  
```powershell
Get-PhysicalDisk
```  
- Try mounting manually in **Disk Management** (`diskmgmt.msc`) |
| **Failover Cluster Reports Split-Brain Scenario** | - Quorum misconfiguration | - Restart cluster service on **one** node at a time |
| **Clients Cannot Connect After Failover** | - TCP/IP Protocol Disabled on New Primary | - Enable TCP/IP via **SQL Server Configuration Manager** |

---

## **3️⃣ Key Troubleshooting Queries & Commands**
### **✅ Check SQL Server Always On Health**
```sql
SELECT replica_server_name, role_desc, synchronization_health_desc 
FROM sys.dm_hadr_availability_replica_states;
```
### **✅ Check WSFC Cluster Node Status**
```powershell
Get-ClusterNode | Format-Table Name, State, NodeWeight
```
### **✅ Restart WSFC Cluster Service**
```powershell
Restart-Service ClusSvc
```
### **✅ Force Failover in Always On AG (Manual)**
```sql
ALTER AVAILABILITY GROUP MyAG FAILOVER;
```
### **✅ Force FCI Failover to Another Node**
```powershell
Move-ClusterGroup "SQL Server (MSSQLSERVER)" -Node Node2
```

---

## **4️⃣ Summary: Additional Issues in Always On vs. FCI**
| **Issue** | **Always On (AGs)** | **Failover Cluster (FCI)** |
|------------|-----------------|-----------------|
| **Automatic Failover Delayed** | ✅ Yes (WSFC Voting Issues) | ✅ Yes (Cluster Service Delays) |
| **Transaction Log Growth** | ✅ Yes (Secondary Disconnect) | ❌ No |
| **Read-Only Routing Not Working** | ✅ Yes | ❌ No |
| **Storage Failover Problems** | ❌ No | ✅ Yes (SAN Required) |
| **Clients Failing to Connect** | ✅ Yes (AG Listener Issue) | ✅ Yes (VNN Issue) |

---

## **🚀 Final Recommendations**
### **🛠 Best Practices for Always On AG**
✔ **Monitor AG Health** using `sys.dm_hadr_availability_replica_states`  
✔ **Use Quorum Configuration Correctly** to prevent cluster downtime  
✔ **Enable Read-Only Routing** for better load balancing  

### **🛠 Best Practices for Failover Cluster (FCI)**
✔ **Use File Share Witness** to avoid quorum loss  
✔ **Optimize Storage Paths** for better failover speed  
✔ **Monitor Cluster Logs** using `Get-ClusterLog -TimeSpan 5`  

🚀 **AG = Best for Fast Database Failover** | **FCI = Best for Full Instance Failover**  

---
Hope this helps! Let me know if you need further details. 😊🚀
