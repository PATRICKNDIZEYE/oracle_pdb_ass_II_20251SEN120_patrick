# Oracle PDB Assignment II — Patrick (20251SEN120)

## Overview of Tasks

This assignment covers Oracle Multitenant Architecture:

1. **Task 1:** Create a new pluggable database (`pa_pdb_20251SEN120`) and a user inside it (`patrick_plsqlauca_20251SEN120`).
2. **Task 2:** Create a temporary PDB (`pa_to_delete_pdb_20251SEN120`), verify it, then delete it completely.
3. **Task 3:** Access Oracle Enterprise Manager Database Express and show the dashboard for my PDB.
4. **Task 4:** Document everything in this public GitHub repository.

## Oracle Environment Used

| Item | Value |
|---|---|
| Host | MacBook (Apple Silicon, macOS) |
| Database | Oracle Database 21c Express Edition 21.3.0.0.0 |
| Container DB | `XE` (CDB$ROOT, PDB$SEED, XEPDB1) |
| Runtime | Docker image `gvenzl/oracle-xe:21-full` on a Colima x86_64 virtual machine (QEMU) |
| Client | SQL*Plus (`docker exec -it oracle-xe sqlplus / as sysdba`) |
| OEM | Enterprise Manager Database Express at `https://localhost:5500/em` |

## Task 1: Create a New Pluggable Database

Connected to the root container as SYSDBA and created `pa_pdb_20251SEN120` from the seed, with a separate PDB admin (`pa_admin`). `FILE_NAME_CONVERT` places its datafiles in their own folder. Then I opened the PDB and saved its state so it reopens automatically after a restart.

Next I switched into the PDB, created the user `patrick_plsqlauca_20251SEN120`, and granted it `CONNECT` and `RESOURCE`. The PDB had no `USERS` tablespace, so I created one, made it the default tablespace, and gave the user unlimited quota on it. This user will be reused for future class work.

![Create PDB](screenshots/pdb_creation/01_create_pdb.png)
![PDB open](screenshots/pdb_creation/02_pdb_open_read_write.png)
![Switch to PDB](screenshots/pdb_creation/03_switch_to_pdb.png)
![User tablespace and quota](screenshots/pdb_creation/04_user_tablespace_quota.png)
![User in PDB](screenshots/pdb_creation/05_user_in_pdb.png)

## Task 2: Create and Delete a PDB

Created `pa_to_delete_pdb_20251SEN120` from the seed, opened it, and confirmed with `SHOW PDBS` that it was `READ WRITE`. Then I closed it with `CLOSE IMMEDIATE` and dropped it with `INCLUDING DATAFILES`, so its files were removed from disk too. A final `SHOW PDBS` confirms it no longer exists.

![Create temp PDB](screenshots/pdb_deletion/01_create_temp_pdb.png)
![Temp PDB exists](screenshots/pdb_deletion/02_temp_pdb_exists.png)
![Close and drop temp PDB](screenshots/pdb_deletion/03_close_and_drop_temp_pdb.png)
![Temp PDB gone](screenshots/pdb_deletion/04_temp_pdb_gone.png)

## Task 3: Oracle Enterprise Manager (OEM)

I enabled the EM Express global port so PDBs can be accessed on port 5500. Then I logged in as `system` with the container `PA_PDB_20251SEN120`. The dashboard header shows `XE / PA_PDB_20251SEN120`, the username `system` appears at the top right, and the Status panel shows the instance details (21.3.0.0.0 Express Edition, Linux x86 64-bit).

![OEM dashboard](screenshots/oem_dashboard/01_oem_dashboard_pa_pdb.png)

## Challenges Faced and Solutions

| Challenge | Cause | Solution |
|---|---|---|
| `ORA-00442: single instance violation` when starting Oracle XE | Oracle XE fails its CPU/licensing check under Rosetta emulation on Apple Silicon | Ran Docker in a full x86_64 virtual machine (Colima with QEMU) instead of Rosetta |
| Very slow VM startup and image download | Slow internet connection; the VM updated its packages on every boot | Waited for the boot to finish, then restarted Colima so Docker could start |
| `SP2-0382: The SHOW PDBS command is not available` | `SHOW PDBS` only works when connected as SYSDBA | Reconnected with `sqlplus / as sysdba` |
| First PDB used my assignment username as its admin user | That made a separate `CREATE USER` impossible | Dropped the PDB and recreated it with a separate admin, `pa_admin` |
| `ORA-00959: tablespace 'USERS' does not exist` | A PDB created from the seed has no `USERS` tablespace | Created a `USERS` tablespace in the PDB and set it as the default |
| EM Express could not log in to the PDB | The EM port was only enabled for the root container | Enabled the global port with `DBMS_XDB_CONFIG.SETGLOBALPORTENABLED(TRUE)` |

## Integrity Statement

I executed all database tasks in this assignment myself, on my own machine. The screenshots are from my own sessions. I used an AI assistant to help set up the Oracle environment on macOS and to explain Oracle command syntax and errors as I worked.

## Submission Details

- **Repository Link:** https://github.com/PATRICKNDIZEYE/oracle_pdb_ass_II_20251SEN120_patrick
- **PDB Name Created:** pa_pdb_20251SEN120
- **Issues Encountered:** Yes
