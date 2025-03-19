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

Hope this helps! Let me know if you need further details. 😊🚀
