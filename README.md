# 🔐 Access Control & Data Support Automation Platform

A Flask-based role-based access control (RBAC) system for managing user permissions, validating data integrity, and automating routine maintenance tasks — backed by MySQL.

---

## 📌 Features

- **Role-Based Access Control (RBAC)** — Define roles, assign granular permissions, and enforce access policies per user
- **User Management** — Register users, assign roles, deactivate accounts, and audit login activity
- **Access Verification** — Real-time permission checks with full audit trail logging
- **Data Validation & Correction** — SQL-driven checks to detect and fix data inconsistencies
- **Automated Maintenance Scripts** — Python scripts for log purging, stale account cleanup, and daily reporting
- **Live Dashboard** — User overview with role badges, status indicators, and denied-access counters

---

## 🛠️ Tech Stack

| Layer      | Technology              |
|------------|-------------------------|
| Backend    | Python 3.10+, Flask 3.x |
| Database   | MySQL 8.x               |
| ORM/Driver | mysql-connector-python  |
| Frontend   | HTML5, CSS3 (Jinja2)    |
| Auth       | SHA-256 + salt hashing  |

---

## 📁 Project Structure

```
project2/
├── app.py                    # Main Flask application (RBAC + API)
├── maintenance_scripts.py    # Automated maintenance & log processing
├── schema.sql                # Database schema + seeded roles & permissions
├── requirements.txt          # Python dependencies
├── access_control.log        # Runtime log output (auto-generated)
├── maintenance.log           # Maintenance task log (auto-generated)
└── templates/
    └── dashboard.html        # Access control dashboard UI
```

---

## ⚙️ Setup & Installation

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/access-control-platform.git
cd access-control-platform
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure the database
Edit `app.py` and `maintenance_scripts.py`, updating `DB_CONFIG`:
```python
DB_CONFIG = {
    "host": "localhost",
    "user": "your_mysql_user",
    "password": "your_mysql_password",
    "database": "access_control_db"
}
```

### 4. Initialize the database
```bash
mysql -u root -p < schema.sql
```

### 5. Run the application
```bash
python app.py
```

Visit: [http://localhost:5001](http://localhost:5001)

---

## 🔌 API Endpoints

### Roles & Permissions
| Method | Endpoint                              | Description                     |
|--------|---------------------------------------|---------------------------------|
| POST   | `/api/roles`                          | Create a new role               |
| GET    | `/api/roles`                          | List all roles with counts      |
| POST   | `/api/permissions`                    | Create a new permission         |
| POST   | `/api/roles/<id>/permissions`         | Assign permission to a role     |

---

### Users
| Method | Endpoint                          | Description                        |
|--------|-----------------------------------|------------------------------------|
| POST   | `/api/users`                      | Register a new user                |
| GET    | `/api/users`                      | List users (filter by role/status) |
| PUT    | `/api/users/<id>/role`            | Update user's role                 |
| PUT    | `/api/users/<id>/deactivate`      | Deactivate a user account          |

**POST `/api/users` — Example payload:**
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "SecurePass123",
  "role_id": 2
}
```

---

### Access Control
| Method | Endpoint              | Description                          |
|--------|-----------------------|--------------------------------------|
| POST   | `/api/access/check`   | Verify if a user can perform an action |

**POST `/api/access/check` — Example payload:**
```json
{
  "user_id": 5,
  "resource": "reports",
  "action": "READ"
}
```

---

### Data Validation & Correction
| Method | Endpoint               | Description                          |
|--------|------------------------|--------------------------------------|
| GET    | `/api/data/validate`   | Run data integrity checks            |
| POST   | `/api/data/correct`    | Apply a data correction task         |

**Available correction tasks:**
- `deactivate_users_no_role`
- `trim_whitespace_usernames`
- `lowercase_emails`

---

### Maintenance & Logs
| Method | Endpoint                              | Description                  |
|--------|---------------------------------------|------------------------------|
| POST   | `/api/maintenance/cleanup-logs`       | Purge old access log records |
| GET    | `/api/maintenance/report`             | View maintenance task history|
| GET    | `/api/access-logs`                    | Query access audit logs      |

---

## 🗄️ Database Schema

```
roles               — id, role_name, description, created_at
permissions         — id, permission_name, resource, action, description
role_permissions    — role_id, permission_id  (many-to-many)
users               — id, username, email, password_hash, role_id, is_active, last_login
access_logs         — id, user_id, username, action, resource, status, ip_address, details
maintenance_logs    — id, task_name, task_type, status, result_summary, executed_by
```

### Seeded Roles

| Role      | Permissions                              |
|-----------|------------------------------------------|
| ADMIN     | All permissions                          |
| DEVELOPER | users:read, reports:read/write, logs:read|
| ANALYST   | reports:read, logs:read                  |
| SUPPORT   | users:read/write, logs:read              |
| VIEWER    | users:read, reports:read                 |

---

## 🤖 Automated Maintenance

Run all maintenance tasks manually:
```bash
python maintenance_scripts.py
```

**Tasks included:**
- `purge_old_access_logs(days=30)` — Delete logs older than N days
- `validate_user_data()` — Check for missing roles, duplicate emails, suspicious activity
- `generate_daily_report()` — Summarize access activity for the past 24 hours
- `deactivate_stale_accounts(inactive_days=90)` — Auto-disable accounts with no recent login

**Schedule via cron (example — run daily at 2 AM):**
```
0 2 * * * /usr/bin/python3 /path/to/maintenance_scripts.py
```

---

## 🚀 Deployment Notes

- Set `debug=False` in `app.run()` for production
- Store `secret_key` and DB credentials in environment variables
- Use Gunicorn + Nginx for production serving
- Schedule `maintenance_scripts.py` via cron or a task scheduler

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.
