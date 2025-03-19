### **🔹 SQL Server Always On Availability Groups (AG): Behavior During Primary Node Failure**
When the **primary node fails** in an **Always On Availability Group (AG)**, SQL Server follows a **failover process** based on the configuration of the AG and **Windows Server Failover Clustering (WSFC)**.

---

## **1️⃣ What Happens When the Primary Node Fails?**
| Scenario | **Behavior** |
|----------|-------------|
| **Automatic Failover Enabled?** | ✅ Yes → Failover occurs immediately |
| **Synchronous Mode Used?** | ✅ Yes → No data loss |
| **Asynchronous Mode Used?** | ⚠️ Yes → Potential data loss |
| **Manual Failover Required?** | ✅ Yes (if configured for manual failover) |
| **Quorum Lost?** | ❌ AG Becomes Unavailable (Manual recovery needed) |

🔹 **Automatic failover happens if:**  
✔ The AG is in **synchronous mode**.  
✔ A **secondary replica is configured for automatic failover**.  
✔ WSFC **votes to promote the secondary as primary**.

---

## **2️⃣ Failover Behavior Based on Availability Mode**
| **Availability Mode** | **Failover Type** | **Data Loss?** | **Failover Speed** |
|----------------------|-----------------|------------|-----------------|
| **Synchronous Commit** | ✅ Automatic | ❌ No Data Loss | ✅ Fast (~Seconds) |
| **Asynchronous Commit** | ❌ Manual | ⚠️ Possible Data Loss | ❌ Slower (~Minutes) |
| **Synchronous with Manual Failover** | ✅ Manual | ❌ No Data Loss | ⏳ Requires Admin Action |

🔹 **Synchronous mode ensures zero data loss, but requires acknowledgment from the secondary before committing transactions.**  
🔹 **Asynchronous mode is faster, but can lead to potential data loss.**

---

## **3️⃣ How the Failover Process Works**
### **✅ Step 1: WSFC Detects Primary Node Failure**
- The cluster checks the **health of the primary replica**.
- If the primary does not respond, WSFC **initiates failover**.

### **✅ Step 2: Failover Decision Based on Quorum Votes**
- WSFC **votes to promote** the best available **secondary replica**.
- If a **quorum is lost**, the AG becomes **unavailable**.

### **✅ Step 3: Secondary Replica Becomes New Primary**
- SQL Server **redirects traffic to the new primary**.
- Applications automatically **connect to the new primary via AG Listener**.

### **✅ Step 4: Old Primary Becomes Secondary (When Recovered)**
- When the **failed node comes back online**, it **syncs with the new primary**.
- It can either **remain a secondary** or **be manually failed back**.

---

## **4️⃣ What Happens to Read-Only Queries During Failover?**
| **Read Mode** | **Behavior During Primary Failure** |
|--------------|-----------------------------------|
| **Read-Intent Only (`ApplicationIntent=ReadOnly`)** | ✅ Redirected to another readable secondary |
| **Yes (Allow All Read-Only Connections)** | ❌ Users must manually connect to another replica |

🔹 **If Read-Only Routing is enabled, read queries continue automatically.**  
🔹 **If Read-Only Routing is NOT enabled, connections must be reconfigured manually.**

---

## **5️⃣ How to Check Current Failover Status**
### **✅ Check Current Primary Node**
```sql
SELECT role_desc FROM sys.dm_hadr_availability_replica_states WHERE is_local = 1;
```
### **✅ Check Replica Failover Readiness**
```sql
SELECT replica_id, role_desc, synchronization_health_desc 
FROM sys.dm_hadr_availability_replica_states;
```
### **✅ Check WSFC Cluster State**
```powershell
Get-ClusterNode
```

---

## **6️⃣ Summary: SQL Server Behavior When Primary Node Fails**
| Scenario | **Behavior** |
|----------|-------------|
| **Primary Node Fails** | ✅ WSFC detects failure & initiates failover |
| **Automatic Failover Enabled?** | ✅ Yes → Secondary becomes primary instantly |
| **Asynchronous Mode Used?** | ⚠️ Yes → Data loss possible |
| **Quorum Lost?** | ❌ AG becomes unavailable (manual intervention needed) |
| **Read-Only Queries?** | ✅ Redirected if Read-Intent Routing is enabled |

🚀 **For best failover performance:**  
✔ **Use Synchronous Commit Mode** for **zero data loss**.  
✔ **Enable Read-Intent Routing** to keep **read-only queries running**.  
✔ **Ensure Quorum is properly configured** to avoid cluster downtime.  

Hope this helps! Let me know if you need further details. 😊🚀
