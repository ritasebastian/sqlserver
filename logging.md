### **Different Types of Logging in SQL Server**
SQL Server provides various types of logging mechanisms for **performance monitoring, troubleshooting, auditing, and disaster recovery**. Below are the main types:

---

## **1️⃣ Transaction Logging (T-Log)**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | Transaction Log (`.ldf` file) |
| **Purpose** | Captures all database transactions to ensure ACID compliance |
| **Used For** | Crash recovery, point-in-time recovery, rollback, and replication |
| **How to View Logs** | `DBCC LOGINFO` / `fn_dblog()` |
| **Backup Command** | `BACKUP LOG <DBName> TO DISK = 'path'` |
| **Example Query** | `SELECT * FROM fn_dblog(NULL, NULL)` |

---

## **2️⃣ Error Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | SQL Server Error Log (`ERRORLOG` file) |
| **Purpose** | Stores system messages, errors, login failures, startup information |
| **Used For** | Debugging, server crash analysis |
| **How to View Logs** | `sp_readerrorlog` / SSMS Log Viewer |
| **Change Error Log Size** | `EXEC sp_cycle_errorlog;` |
| **Example Query** | `EXEC sp_readerrorlog 0, 1, 'error'` |

---

## **3️⃣ SQL Server Agent Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | SQL Server Agent Log (`SQLAGENT.OUT`) |
| **Purpose** | Records job history, alerts, and failures |
| **Used For** | Debugging failed SQL Agent jobs |
| **How to View Logs** | SSMS → SQL Server Agent → Error Logs |
| **Example Query** | `EXEC msdb.dbo.sp_help_jobhistory @job_name = 'JobName'` |

---

## **4️⃣ Query Execution & Performance Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | Query Store & Extended Events |
| **Purpose** | Tracks query execution plans, CPU usage, and performance metrics |
| **Used For** | Identifying slow queries, troubleshooting performance issues |
| **Enable Query Store** | `ALTER DATABASE [DBName] SET QUERY_STORE = ON;` |
| **View Query Store Data** | `SELECT * FROM sys.query_store_query` |

---

## **5️⃣ Audit Logging (Security)**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | SQL Server Audit (`.sqlaudit` file) |
| **Purpose** | Tracks user logins, schema changes, DDL/DML operations |
| **Used For** | Compliance with regulations (e.g., GDPR, HIPAA, SOX) |
| **Enable Auditing** | `CREATE SERVER AUDIT` |
| **View Audit Logs** | `SELECT * FROM sys.fn_get_audit_file('auditpath', NULL, NULL)` |

---

## **6️⃣ Profiler & Trace Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | SQL Trace & Profiler Logs |
| **Purpose** | Captures detailed real-time SQL execution, deadlocks, and index usage |
| **Used For** | Analyzing performance bottlenecks |
| **Enable Tracing** | `sp_trace_create` |
| **View Trace Data** | `SELECT * FROM sys.traces` |

---

## **7️⃣ Windows Event Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | Windows Event Viewer |
| **Purpose** | Stores system-wide SQL Server errors, crashes, and service restarts |
| **Used For** | Debugging OS-level SQL Server issues |
| **Access Logs** | `eventvwr.msc → Application Logs` |
| **Example Query** | `Get-EventLog -LogName Application -Source "MSSQLSERVER"` |

---

## **8️⃣ Deadlock Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | Deadlock Graph |
| **Purpose** | Captures deadlocks between queries |
| **Used For** | Troubleshooting concurrency issues |
| **Enable Deadlock Logging** | `DBCC TRACEON (1222, -1)` |
| **View Deadlock Logs** | `SELECT * FROM sys.dm_tran_locks` |

---

## **9️⃣ TempDB Logging**
| **Feature** | **Details** |
|------------|------------|
| **Log Type** | TempDB Usage Logs |
| **Purpose** | Tracks temporary table usage, spills, and session temp tables |
| **Used For** | Identifying high TempDB usage queries |
| **View Logs** | `SELECT * FROM sys.dm_db_file_space_usage` |

---

## **🔹 Summary**
| **Log Type** | **Purpose** |
|-------------|------------|
| **Transaction Log** | Tracks all DB transactions, used for recovery |
| **Error Log** | Stores system errors and startup messages |
| **SQL Agent Log** | Records job execution details |
| **Query Store** | Tracks query performance & execution plans |
| **Audit Log** | Captures security and compliance events |
| **Profiler Log** | Captures real-time query executions |
| **Windows Event Log** | Stores system-wide SQL errors and crashes |
| **Deadlock Log** | Detects and logs deadlocks |
| **TempDB Log** | Monitors TempDB usage |

💡 **Which one do you need help with?** 🚀
