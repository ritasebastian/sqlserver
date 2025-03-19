### **🔹 How to Set Failover Priority in Always On Availability Groups (AGs) with 8 Replicas?**
When you have **multiple secondary replicas (up to 8 in SQL Server Always On)**, you need to configure **failover priority** to control **which replica becomes the new primary** in case of failure.

---

## **1️⃣ How to Set Failover Priority for Replicas**
### **✅ Configure Failover Priority Using T-SQL**
You can assign a **failover priority (1-100, lower number = higher priority)** using:
```sql
ALTER AVAILABILITY GROUP MyAG 
MODIFY REPLICA ON 'ReplicaServer2' WITH (FAILOVER_MODE = AUTOMATIC, AVAILABILITY_MODE = SYNCHRONOUS_COMMIT, FAILOVER_PRIORITY = 10);
```
✔ **Replica with the lowest priority number will take over first.**  

### **✅ Configure Failover Priority in SSMS**
1️⃣ **Go to SSMS** → Expand **Always On High Availability**.  
2️⃣ **Right-click Availability Group** → Click **Properties**.  
3️⃣ **Go to Replicas Tab** → Adjust the **Failover Priority**.  
4️⃣ Click **OK** to save the changes.

✔ **Primary will fail over to the highest-priority synchronous replica.**  

---

## **2️⃣ Can I Manually Fail Over to Any Replica?**
✅ **Yes, you can manually failover to any secondary replica** using SSMS or T-SQL.

### **✅ Manually Failover to a Specific Replica**
```sql
ALTER AVAILABILITY GROUP MyAG 
FAILOVER TO 'ReplicaServer5';
```
✔ This **forces** SQL Server to switch to `ReplicaServer5` manually.

### **✅ Manually Failover Using SSMS**
1️⃣ **Right-click Availability Group** → Select **Failover**.  
2️⃣ **Choose the target secondary replica**.  
3️⃣ Click **OK** to force failover.

✔ **Used when you want full control over failover (e.g., planned maintenance).**  

---

## **3️⃣ How Does Automatic Failover Work with 8 Replicas?**
| **Replica** | **Failover Mode** | **Failover Priority** | **Can Become Primary?** |
|------------|-----------------|------------------|------------------|
| **Primary (Current Node)** | **Automatic** | **1** | ✅ Yes |
| **Replica 1** | **Automatic (Sync)** | **10** | ✅ Yes |
| **Replica 2** | **Automatic (Sync)** | **20** | ✅ Yes |
| **Replica 3** | **Manual (Async)** | **N/A** | ❌ No |
| **Replica 4** | **Manual (Async)** | **N/A** | ❌ No |
| **Replica 5** | **Automatic (Sync)** | **30** | ✅ Yes |
| **Replica 6** | **Manual (Async)** | **N/A** | ❌ No |
| **Replica 7** | **Manual (Async)** | **N/A** | ❌ No |

🔹 **Only synchronous replicas (Sync Commit) with `Automatic Failover` enabled can take over automatically.**  
🔹 **Asynchronous replicas require a manual failover.**  

---

## **4️⃣ Best Practices for Failover Priority in an 8-Replica AG**
✅ **Set at least 2 replicas with `Automatic Failover` in `Synchronous Commit Mode`**.  
✅ **Prioritize failover to the nearest, lowest-latency replica**.  
✅ **Use `Manual Failover` for replicas in remote locations.**  
✅ **Monitor failover behavior using `sys.dm_hadr_availability_replica_states`.**

---

## **5️⃣ Summary: Configuring Failover Priority in an 8-Replica AG**
| Feature | **Automatic Failover** | **Manual Failover** |
|---------|------------------|----------------|
| **Failover Priority Control?** | ✅ Yes (Set via T-SQL/SSMS) | ✅ Yes (Force failover) |
| **Works on Async Replicas?** | ❌ No | ✅ Yes |
| **Best for High Availability?** | ✅ Yes | ❌ No |
| **Best for Disaster Recovery?** | ❌ No | ✅ Yes |

🚀 **Use Automatic Failover for HA | Manual Failover for DR.**  
Hope this helps! Let me know if you need further details. 😊🚀
