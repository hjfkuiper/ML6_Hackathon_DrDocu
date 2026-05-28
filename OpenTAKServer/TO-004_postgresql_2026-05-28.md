# OpenTAKServer — Technical Design: PostgreSQL Database

| | |
|---|---|
| **Document ID** | TO-004 |
| **Version** | 0.2 |
| **Status** | DRAFT |
| **Date** | 2026-05-28 |
| **Author** | DrDocu Agent |
| **Owner** | ML6 |
| **Related TLD/LD** | TLD-001 / LD-001 |
| **Classification** | Internal |

---

## 1. Purpose

This document describes the installation and configuration of PostgreSQL as the relational database for OpenTAKServer (OTS). PostgreSQL stores all persistent data: EUD registrations, CoT messages, missions, user accounts, certificates, and configuration data. The procedure covers database user creation, schema initialisation via Flask-Migrate, and connection verification.

---

## 2. Applicability

| Environment | Version | Valid From |
|---|---|---|
| Ubuntu 22.04 LTS | PostgreSQL 14+ | 2026-05-28 |
| Ubuntu 24.04 LTS | PostgreSQL 16+ | 2026-05-28 |
| OpenTAKServer | 1.3.x | 2026-05-28 |

---

## 3. Reference Documents

| ID | Title | Location |
|---|---|---|
| REF-001 | OpenTAKServer GitHub Repository | https://github.com/brian7704/OpenTAKServer |
| REF-002 | PostgreSQL Documentation | https://www.postgresql.org/docs/ |
| REF-003 | Flask-Migrate Documentation | https://flask-migrate.readthedocs.io |
| REF-004 | OTS config.yml Reference | https://docs.opentakserver.io/config |

---

## 4. Prerequisites

- [ ] Ubuntu 22.04 or 24.04 LTS installed and updated
- [ ] Root or sudo access available
- [ ] Port 5432 available (not in use by another process)
- [ ] Minimum 1 GB free disk space for database
- [ ] OpenTAKServer installed (Python environment active)
- [ ] `psql` client available after installation

---

## 5. Procedure

### Step 1 — Install PostgreSQL

**Action:** Install PostgreSQL via apt.

```bash
sudo apt update && sudo apt install -y postgresql postgresql-contrib
```

**Expected Result:** PostgreSQL installed and service started.

**Verification:** `systemctl status postgresql` shows `active (running)`; `psql --version` shows installed version.

---

### Step 2 — Create OTS database user

**Action:** Create a dedicated PostgreSQL user for OTS.

```bash
sudo -u postgres psql -c "CREATE USER ots WITH PASSWORD 'REPLACE_WITH_STRONG_PASSWORD';"
```

**Expected Result:** User `ots` created.

**Verification:** `sudo -u postgres psql -c "\du"` shows `ots` in the list.

---

### Step 3 — Create OTS database

**Action:** Create the OTS database owned by the `ots` user.

```bash
sudo -u postgres psql -c "CREATE DATABASE opentakserver OWNER ots;"
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE opentakserver TO ots;"
```

**Expected Result:** Database `opentakserver` created and accessible by the `ots` user.

**Verification:** `sudo -u postgres psql -l` shows `opentakserver` with owner `ots`.

---

### Step 4 — Update OTS configuration

**Action:** Set PostgreSQL connection details in the OTS config file.

```bash
sudo nano /etc/opentakserver/config.yml
```

Relevant fields:

```yaml
SQLALCHEMY_DATABASE_URI: "postgresql://ots:REPLACE_WITH_STRONG_PASSWORD@127.0.0.1/opentakserver"
```

**Expected Result:** Configuration file updated.

**Verification:** `sudo cat /etc/opentakserver/config.yml | grep SQLALCHEMY`

---

### Step 5 — Run database migrations

**Action:** Initialise the database schema using Flask-Migrate.

```bash
cd /opt/opentakserver  # or your OTS install directory
source venv/bin/activate
flask db upgrade
```

**Expected Result:** All migration scripts applied, tables created.

**Verification:** Connect to the database and list tables:
```bash
sudo -u postgres psql -d opentakserver -c "\dt"
```
Expected: tables such as `cot`, `eud`, `user`, `mission`, `certificate` visible.

---

### Step 6 — Restart OTS and verify connection

**Action:** Restart OTS and confirm database connectivity in the logs.

```bash
sudo systemctl restart ots && sudo journalctl -u ots -n 50 --no-pager
```

**Expected Result:** No PostgreSQL connection errors in the log.

**Verification:** Log shows successful startup without `OperationalError` or `connection refused` messages.

---

## 6. Verification and Acceptance Criteria

| Criterion | Verification Method | Acceptance Value |
|---|---|---|
| PostgreSQL service active | `systemctl status postgresql` | `active (running)` |
| OTS user exists | `sudo -u postgres psql -c "\du"` | `ots` listed |
| Database exists | `sudo -u postgres psql -l` | `opentakserver` listed with owner `ots` |
| Schema migrated | `psql -d opentakserver -c "\dt"` | Core tables present (cot, eud, user, etc.) |
| OTS connects to DB | OTS startup log | No connection errors |
| CoT data persisted | Send CoT from ATAK, query DB | Record present in `cot` table |

---

## 7. Rollback Procedure

1. Stop OTS: `sudo systemctl stop ots`
2. Roll back the last migration if needed:
   ```bash
   source venv/bin/activate && flask db downgrade
   ```
3. Drop and recreate the database if a clean start is required:
   ```bash
   sudo -u postgres psql -c "DROP DATABASE opentakserver;"
   sudo -u postgres psql -c "CREATE DATABASE opentakserver OWNER ots;"
   ```
4. Restore OTS config to previous database URI if changed.
5. Restart OTS: `sudo systemctl start ots`

---

## 8. Known Issues and Workarounds

| Issue | Circumstance | Workaround |
|---|---|---|
| `connection refused` on OTS start | PostgreSQL not running | `sudo systemctl start postgresql` |
| `permission denied` on database | User not granted privileges | Re-run `GRANT ALL PRIVILEGES ON DATABASE opentakserver TO ots;` |
| Migration fails with `relation already exists` | Partial previous migration | Check `flask db history`; manually mark migration as applied with `flask db stamp head` |
| Slow queries under load | Missing indexes on `cot` table | Run `VACUUM ANALYZE cot;` periodically |
| Disk full due to CoT history | High-volume deployments | Implement a data retention policy; purge old CoT records with a scheduled job |

---

## 9. Change History

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-28 | DrDocu Agent | Initial document |
| 0.2 | 2026-05-28 | DrDocu Agent | Translated to English, full procedure added, restructured to new TO template |
