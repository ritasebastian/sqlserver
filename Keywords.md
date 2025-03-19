### **Specialized SQL Server Keywords for DBAs**  

As a **SQL Server DBA**, you should be familiar with the following specialized keywords categorized based on their functionality:

---

## **1️⃣ Database Management Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `CREATE DATABASE` | Creates a new database |
| `ALTER DATABASE` | Modifies database settings |
| `DROP DATABASE` | Deletes a database |
| `USE` | Switches to a different database |
| `DBCC CHECKDB` | Checks the integrity of the database |
| `DBCC SHRINKDATABASE` | Shrinks the database size |
| `DBCC FREEPROCCACHE` | Clears cached query plans |
| `DBCC DROPCLEANBUFFERS` | Clears data cache |
| `DBCC LOGINFO` | Views VLF (Virtual Log Files) in transaction logs |
| `DBCC OPENTRAN` | Checks for open transactions |
| `DBCC SHOW_STATISTICS` | Displays index statistics |
| `SP_HELPDB` | Provides database information |

---

## **2️⃣ User & Security Management Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `CREATE LOGIN` | Creates a new login at the server level |
| `CREATE USER` | Creates a new database user |
| `ALTER LOGIN` | Modifies login credentials |
| `DROP LOGIN` | Deletes a login |
| `GRANT` | Grants permissions |
| `REVOKE` | Removes granted permissions |
| `DENY` | Explicitly denies permissions |
| `SP_WHO2` | Displays current user connections |
| `SP_LOCK` | Shows active locks on database objects |
| `ALTER ROLE` | Modifies a database role |

---

## **3️⃣ Backup & Restore Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `BACKUP DATABASE` | Creates a full database backup |
| `BACKUP LOG` | Creates a transaction log backup |
| `RESTORE DATABASE` | Restores a database from a backup |
| `RESTORE FILELISTONLY` | Displays logical and physical file details from a backup |
| `RESTORE HEADERONLY` | Displays backup file metadata |
| `WITH RECOVERY` | Makes the database operational after restore |
| `WITH NORECOVERY` | Keeps the database in restoring state |
| `WITH REPLACE` | Overwrites an existing database during restore |
| `WITH CHECKSUM` | Ensures backup integrity |

---

## **4️⃣ Performance & Optimization Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `INDEX` | Creates and manages indexes |
| `FILLFACTOR` | Determines the amount of free space in an index page |
| `UPDATE STATISTICS` | Updates index and column statistics |
| `REBUILD INDEX` | Rebuilds fragmented indexes |
| `REORGANIZE INDEX` | Defragments indexes online |
| `SP_WHOISACTIVE` | Shows real-time active sessions |
| `SP_BLITZCACHE` | Analyzes query performance |
| `DBCC SQLPERF(LOGSPACE)` | Displays transaction log usage |

---

## **5️⃣ Transaction & Lock Management Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `BEGIN TRANSACTION` | Starts a transaction |
| `COMMIT` | Commits a transaction |
| `ROLLBACK` | Rolls back a transaction |
| `SAVEPOINT` | Creates a transaction savepoint |
| `NOLOCK` | Reads data without locking |
| `TABLOCK` | Locks the entire table |
| `ROWLOCK` | Locks only a single row |
| `XLOCK` | Acquires an exclusive lock |

---

## **6️⃣ High Availability (HA) & Disaster Recovery (DR) Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `LOG SHIPPING` | Transfers transaction logs to another server |
| `MIRRORING` | Configures database mirroring |
| `ALWAYS ON AVAILABILITY GROUPS` | Provides high availability solutions |
| `FAILOVER` | Switches to a standby database |
| `SYNCHRONOUS_COMMIT` | Ensures transaction consistency in HA setups |

---

## **7️⃣ System Administration Keywords**
| **Keyword** | **Purpose** |
|------------|------------|
| `SP_CONFIGURE` | Changes global SQL Server settings |
| `EXEC` | Executes stored procedures |
| `XP_CMDSHELL` | Runs Windows commands from SQL Server |
| `SERVERPROPERTY` | Retrieves SQL Server instance information |
| `RESOURCE GOVERNOR` | Limits resource usage per workload |
| `SESSION_ID` | Retrieves current session ID |
| `WAITFOR` | Delays query execution |
| `KILL` | Terminates a session |
| `DMV (Dynamic Management Views)` | Retrieves system performance insights |
| `ERRORLOG` | Views SQL Server error logs |

---

### **💡 Summary**
These SQL Server keywords are crucial for a **DBA** to manage **security, backup, performance tuning, transactions, high availability, and system administration.**  

Do you need a **step-by-step implementation guide** for any of these? 🚀
