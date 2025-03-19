### **SQL Server Tooling - Interview Questions & Answers**  

Below are some commonly asked **SQL Server DBA tooling** interview questions along with answers.

---

## **1️⃣ SQL Server Management Studio (SSMS)**
### **Q1: What is SSMS, and what are its key features?**
**Answer:**
SSMS (**SQL Server Management Studio**) is a graphical tool for managing **SQL Server databases**. Key features include:
- **Query Editor** – Execute SQL queries.
- **Object Explorer** – Manage databases, security, and logs.
- **Profiler** – Monitor query execution.
- **SQL Agent** – Schedule jobs and automate tasks.
- **Import/Export Wizard** – Data migration between SQL databases.
- **Activity Monitor** – Track performance metrics.

---

### **Q2: How do you script a database in SSMS?**
**Answer:**
1. Open **SSMS** → **Right-click the database**.
2. Select **Tasks → Generate Scripts**.
3. Choose **Objects** (Tables, Views, Stored Procedures).
4. Select **"Save to file" or "Clipboard"**.
5. Execute the script on another server.

---

## **2️⃣ SQL Server Profiler & Extended Events**
### **Q3: What is SQL Server Profiler, and when do you use it?**
**Answer:**
SQL Server Profiler is a **monitoring tool** used to track real-time queries, long-running processes, and deadlocks.  
**Use Cases:**
- Debugging slow queries.
- Identifying blocking or deadlocks.
- Monitoring user activity.
- Capturing SQL Server audit logs.

---

### **Q4: What is the difference between SQL Profiler and Extended Events?**
| Feature | SQL Profiler | Extended Events |
|---------|------------|----------------|
| Performance Impact | High | Low |
| Real-time Monitoring | Yes | Yes |
| Custom Filters | Limited | Advanced |
| Logging Mechanism | Trace Files | Event Storage |
| Recommended? | **Deprecated** | **Preferred** |

**🔹 Use Extended Events instead of Profiler for performance reasons.**

---

## **3️⃣ SQL Server Agent & Job Management**
### **Q5: What is SQL Server Agent, and what is it used for?**
**Answer:**
SQL Server Agent is a job scheduler used to **automate database maintenance tasks**, such as:
- Running **backups**.
- Automating **index rebuilds**.
- Scheduling **ETL tasks**.
- Sending **alerts and notifications**.

---

### **Q6: How do you check failed jobs in SQL Server Agent?**
**Answer:**
1. Open **SSMS → SQL Server Agent → Jobs**.
2. Right-click on the job → **View History**.
3. Look for **failed steps** (Red X).
4. Use **T-SQL**:
   ```sql
   SELECT job_id, name, last_run_outcome 
   FROM msdb.dbo.sysjobs 
   WHERE last_run_outcome = 0;
   ```
   **(0 = Failed, 1 = Succeeded, 2 = Retry, 3 = Canceled)**.

---

## **4️⃣ Performance Monitoring Tools**
### **Q7: What are the built-in SQL Server tools for performance monitoring?**
**Answer:**
1. **Activity Monitor** – Tracks CPU, I/O, Memory.
2. **Performance Monitor (PerfMon)** – Monitors Windows-level performance counters.
3. **DMVs (Dynamic Management Views)** – Analyzes live performance data.
4. **Query Store** – Tracks execution plans and slow queries.
5. **SQL Server Profiler/Extended Events** – Captures slow queries and deadlocks.

---

### **Q8: How do you identify long-running queries in SQL Server?**
**Answer:**
1. **Using DMV (Live Monitoring)**:
   ```sql
   SELECT session_id, blocking_session_id, start_time, status, command, wait_type, wait_time
   FROM sys.dm_exec_requests
   ORDER BY start_time ASC;
   ```
2. **Using Query Store** (SQL Server 2016+):
   ```sql
   SELECT * FROM sys.query_store_query ORDER BY last_execution_time DESC;
   ```
3. **Using Profiler/Extended Events** to capture slow queries.

---

## **5️⃣ Backup & Restore Tools**
### **Q9: How do you automate backups using SQL Server Agent?**
**Answer:**
1. Open **SSMS → SQL Server Agent → Jobs**.
2. Right-click **Jobs → New Job**.
3. Under **Steps**, add:
   ```sql
   BACKUP DATABASE MyDB TO DISK = 'C:\Backups\MyDB.bak' WITH FORMAT, INIT;
   ```
4. Schedule **Daily/Weekly** backups.
5. Enable **Alerts** for failed jobs.

---

### **Q10: How do you restore a database from a backup using SSMS?**
**Answer:**
1. **SSMS → Right-click on Databases → Restore Database**.
2. Select **From Device → Locate .bak file**.
3. Choose **Full, Differential, or Log backups**.
4. **Check Options:**
   - `WITH RECOVERY` → Bring DB online.
   - `WITH NORECOVERY` → Keep DB in restoring state.
5. Click **OK**.

**T-SQL Command:**
```sql
RESTORE DATABASE MyDB FROM DISK = 'C:\Backups\MyDB.bak' WITH RECOVERY;
```

---

## **6️⃣ SQL Server Data Migration Tools**
### **Q11: What tools are available for SQL Server data migration?**
**Answer:**
1. **SQL Server Import/Export Wizard** – GUI for simple data migration.
2. **BCP (Bulk Copy Program)** – Exports/imports large datasets.
3. **SSIS (SQL Server Integration Services)** – ETL tool for complex data transfers.
4. **Linked Servers** – Connects and migrates data between SQL instances.
5. **Azure Data Migration Assistant (DMA)** – Used for migrating databases to Azure.

---

### **Q12: How do you migrate a database from SQL Server 2014 to 2022?**
**Answer:**
1. **Check Compatibility**:
   ```sql
   SELECT compatibility_level FROM sys.databases WHERE name = 'MyDB';
   ```
2. **Back Up the Old Database**:
   ```sql
   BACKUP DATABASE MyDB TO DISK = 'C:\Backups\MyDB.bak';
   ```
3. **Restore on SQL Server 2022**:
   ```sql
   RESTORE DATABASE MyDB FROM DISK = 'C:\Backups\MyDB.bak' WITH RECOVERY;
   ```
4. **Run Upgrade Advisor** for compatibility issues.

---

## **7️⃣ Security & Auditing Tools**
### **Q13: How do you audit user activity in SQL Server?**
**Answer:**
- Use **SQL Server Audit**:
  ```sql
  CREATE SERVER AUDIT MyAudit TO FILE (FILEPATH = 'C:\AuditLogs\');
  ```
- Use **SQL Profiler** (Deprecated).
- Enable **C2 Auditing** (for compliance).
- Monitor **Login Audits**:
  ```sql
  SELECT event_time, database_name, session_id, server_principal_name 
  FROM sys.fn_get_audit_file ('C:\AuditLogs\*.sqlaudit', NULL, NULL);
  ```

---

### **Q14: How do you secure SQL Server against unauthorized access?**
**Answer:**
1. **Enforce Least Privilege** (RBAC).
2. **Use Encrypted Connections (SSL/TLS).**
3. **Enable Transparent Data Encryption (TDE).**
4. **Disable SA Login (or rename it).**
5. **Apply Firewall Rules & IP Whitelisting.**
6. **Use Multi-Factor Authentication (MFA) for Remote Access.**

---

## **💡 Summary: SQL Server Tooling Areas to Focus On**
| **Tooling Area** | **Example Questions** |
|----------------|----------------|
| **SSMS** | Scripting, database management |
| **Profiler & XE** | Monitoring slow queries |
| **SQL Agent** | Job automation, alerts |
| **Performance Tools** | DMVs, Query Store, Activity Monitor |
| **Backup & Restore** | Automating backups, PITR recovery |
| **Migration Tools** | BCP, SSIS, Azure DMA |
| **Security & Auditing** | Auditing logins, TDE, SSL/TLS |

🚀 **Need in-depth help with any of these tools for your interview?** Let me know!
