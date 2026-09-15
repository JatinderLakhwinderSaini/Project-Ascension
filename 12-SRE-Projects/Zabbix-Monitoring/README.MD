Absolutely. Here's a polished **GitHub portfolio-style README** documenting the Zabbix installation, troubleshooting, and configuration you completed on Ubuntu 26.04.

 Zabbix 7.4 Monitoring Server — Ubuntu 26.04

# Zabbix 7.4 Monitoring Server on Ubuntu 26.04

 ## 📌 Project Overview

 This project documents the installation and configuration of **Zabbix 7.4.14** on **Ubuntu 26.04 LTS**, using:

 - Zabbix Server
- Zabbix Agent 2
- MySQL 8.4
- Apache HTTP Server
- PHP 8.5-FPM
- Zabbix Web Frontend

 The goal was to build a functional Zabbix monitoring server and make the web interface accessible through Apache.

---

 ## 🏗️ Architecture

```
                    ┌──────────────────────┐
                    │      Web Browser     │
                    │   http://localhost/  │
                    │        zabbix        │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │        Apache        │
                    │      HTTP Server     │
                    └──────────┬───────────┘
                               │
                         Proxy / FastCGI
                               │
                               ▼
                    ┌──────────────────────┐
                    │     PHP 8.5-FPM      │
                    │   zabbix.sock pool   │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │   Zabbix Frontend    │
                    │      Web UI          │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │    Zabbix Server     │
                    │       7.4.14         │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │      MySQL 8.4       │
                    │   Database: zabbix   │
                    └──────────────────────┘
```

---

 # 🛠️ Technologies Used

 | Component | Version / Details |
| --- | --- |
| Operating System | Ubuntu 26.04 LTS |
| Zabbix | 7.4.14 |
| Database | MySQL 8.4.11 |
| Web Server | Apache 2.4 |
| PHP | 8.5.4 |
| PHP Handler | PHP-FPM |
| Monitoring Agent | Zabbix Agent 2 |
| Database | `zabbix` |
| DB User | `zabbix` |

---

 # 1\. Operating System Verification

 The installation was performed on Ubuntu 26.04 LTS.

```
cat /etc/os-release
```

 Example:

```
PRETTY_NAME="Ubuntu 26.04 LTS"
VERSION_ID="26.04"
VERSION_CODENAME=resolute
```

---

 # 2\. Zabbix Repository Configuration

 The Zabbix repository was configured for Ubuntu 26.04.

 During troubleshooting, multiple Zabbix repository files were identified:

```
/etc/apt/sources.list.d/zabbix-unstable.sources
/etc/apt/sources.list.d/zabbix.sources
/etc/apt/sources.list.d/zabbix-tools.sources
/etc/apt/sources.list.d/zabbix-release.sources
```

 The repository package was updated from the Ubuntu 24.04 build to the Ubuntu 26.04 build:

```
sudo dpkg -i zabbix-release_latest_7.4+ubuntu26.04_all.deb
```

 This ensured the Zabbix repository matched the operating system.

---

 # 3\. Installing Zabbix Components

 The required packages were installed:

```
sudo apt install \
zabbix-server-mysql \
zabbix-frontend-php \
zabbix-apache-conf \
zabbix-sql-scripts \
zabbix-agent2
```

 Installed SQL scripts were verified with:

```
dpkg -l | grep zabbix-sql-scripts
```

 Result:

```
zabbix-sql-scripts  1:7.4.14-1+ubuntu26.04
```

---

 # 4\. MySQL Verification

 MySQL was verified to be running:

```
sudo systemctl status mysql
```

 The service was:

```
Active: active (running)
```

 MySQL version:

```
8.4.11
```

---

 # 5\. Creating the Zabbix Database

 A dedicated database was created for Zabbix.

```
CREATE DATABASE zabbix
CHARACTER SET utf8mb4
COLLATE utf8mb4_bin;
```

 A dedicated database user was created:

```
CREATE USER 'zabbix'@'localhost'
IDENTIFIED BY 'YOUR_PASSWORD';
```

 Privileges were granted:

```
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
```

 For the initial schema import, the following MySQL setting was enabled:

```
SET GLOBAL log_bin_trust_function_creators = 1;
```

---

 # 6\. Database Verification

 The database was verified using:

```
sudo mysql -uroot -p -e "SHOW DATABASES;"
```

 The `zabbix` database was present.

 The Zabbix database account was also tested:

```
mysql -uzabbix -p -e "SELECT 1;"
```

 Successful result:

```
+---+
| 1 |
+---+
| 1 |
+---+
```

 This confirmed that the Zabbix user could successfully authenticate to MySQL.

---

 # 7\. Zabbix SQL Schema

 The installed package was inspected to determine the correct schema location:

```
dpkg -L zabbix-sql-scripts | grep -E 'server.sql|mysql'
```

 The correct schema file was:

```
/usr/share/zabbix/sql-scripts/mysql/server.sql.gz
```

 The schema was imported using:

```
sudo zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | \
mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

 An initial attempt produced:

```
ERROR 1050 (42S01): Table 'role' already exists
```

 Instead of importing the database repeatedly, the database version was checked:

```
mysql -uzabbix -p -e "SELECT * FROM zabbix.dbversion;"
```

 Result:

```
+-------------+-----------+----------+
| dbversionid | mandatory | optional |
+-------------+-----------+----------+
|           1 |   7040000 |  7040011 |
+-------------+-----------+----------+
```

 This confirmed that the Zabbix 7.4 database schema was already initialized.

---

 # 8\. Zabbix Server Configuration

 The Zabbix server configuration file is:

```
/etc/zabbix/zabbix_server.conf
```

 The database password was configured using:

```
DBPassword=YOUR_PASSWORD
```

 The actual password should never be committed to Git.

---

 # 9\. Zabbix Server

 The Zabbix server was verified:

```
sudo systemctl status zabbix-server --no-pager
```

 Result:

```
Active: active (running)
```

 Zabbix Server version:

```
7.4.14
```

 The server was configured to start automatically:

```
sudo systemctl enable zabbix-server
```

---

 # 10\. Zabbix Agent 2

 Zabbix Agent 2 was verified:

```
sudo systemctl status zabbix-agent2 --no-pager
```

 Result:

```
Active: active (running)
```

 The agent reported:

```
Zabbix Agent2 hostname: [Zabbix server]
```

 Agent 2 was configured to start automatically:

```
sudo systemctl enable zabbix-agent2
```

---

 # 11\. Apache Configuration Troubleshooting

 Initially, Apache failed to start because of:

```
Invalid command 'ProxyTimeout'
```

 The Apache proxy module was enabled:

```
sudo a2enmod proxy
```

 Then FastCGI support was enabled:

```
sudo a2enmod proxy_fcgi
```

 Apache configuration was validated:

```
sudo apache2ctl configtest
```

 Result:

```
Syntax OK
```

 Apache was then restarted:

```
sudo systemctl restart apache2
```

 Final status:

```
Active: active (running)
```

---

 # 12\. PHP-FPM Configuration

 PHP CLI version was verified:

```
php -v
```

 Result:

```
PHP 8.5.4
```

 PHP-FPM was running:

```
systemctl status php8.5-fpm --no-pager
```

 Result:

```
Active: active (running)
```

 A dedicated Zabbix PHP-FPM pool was also present:

```
php-fpm: pool zabbix
```

---

 # 13\. Zabbix Apache Configuration

 The Zabbix Apache configuration was located at:

```
/etc/apache2/conf-enabled/zabbix.conf
```

 The configuration uses PHP-FPM through the Zabbix socket:

```
<FilesMatch \.(php|phar)$>
    SetHandler "proxy:unix:/var/run/php/zabbix.sock|fcgi://localhost"
</FilesMatch>
```

 The PHP-FPM socket was verified:

```
ls -l /var/run/php/zabbix.sock
```

 Result:

```
srw-rw---- 1 www-data www-data ... /var/run/php/zabbix.sock
```

 This confirmed that Apache could use the PHP-FPM socket configured by Zabbix.

---

 # 14\. PHP Source Code Issue

 Initially, accessing the Zabbix frontend displayed raw PHP source code instead of the web interface.

 The issue was caused by Apache not having the required FastCGI proxy module enabled.

 The following fixed the problem:

```
sudo a2enmod proxy
sudo a2enmod proxy_fcgi
sudo systemctl restart apache2
```

 After enabling `proxy_fcgi`, Apache correctly passed PHP requests to PHP-FPM.

---

 # 15\. Final Service Status

 The main components were verified as operational:

```
systemctl status mysql
systemctl status zabbix-server
systemctl status zabbix-agent2
systemctl status apache2
systemctl status php8.5-fpm
```

 Expected state:

```
MySQL              → active (running)
Zabbix Server      → active (running)
Zabbix Agent 2     → active (running)
Apache             → active (running)
PHP 8.5-FPM        → active (running)
```

---

 # 16\. Accessing the Zabbix Web Interface

 The Zabbix frontend was successfully accessed through:

```
http://localhost/zabbix
```

 The web GUI loaded successfully, confirming that:

 - Apache is serving the Zabbix frontend.
- PHP-FPM is processing PHP requests.
- The Zabbix frontend is installed correctly.
- The Zabbix server is running.
- The MySQL backend is available.

---

 # 🔧 Troubleshooting Summary

 | Problem | Root Cause | Solution |
| --- | --- | --- |
| Zabbix package dependency conflicts | Incorrect/old repository configuration | Updated Zabbix repository for Ubuntu 26.04 |
| SQL file not found | Incorrect path | Located schema with `dpkg -L` |
| `Table 'role' already exists` | Schema already initialized | Verified `dbversion` instead of re-importing |
| Apache `ProxyTimeout` error | Apache proxy module missing | Enabled `a2enmod proxy` |
| PHP source displayed in browser | FastCGI module missing | Enabled `a2enmod proxy_fcgi` |
| Apache configuration warning | Missing global `ServerName` | Non-critical warning; Apache still operates |

---

 # 🔐 Security Notes

 Sensitive credentials should **never** be committed to this repository.

 For example, do not commit:

```
DBPassword=actual_password
```

 Instead, document it as:

```
DBPassword=YOUR_PASSWORD
```

 A `.gitignore` file should also be used to prevent accidental credential/configuration leaks.

 Example:

```
.env
*.secret
*.key
*.pem
credentials/
secrets/
```

---

 # 🚀 Skills Demonstrated

 This project demonstrates practical experience with:

 - Linux system administration
- Ubuntu Server
- APT package management
- Repository configuration
- Zabbix monitoring
- MySQL database administration
- SQL database initialization
- Apache HTTP Server
- PHP 8.5
- PHP-FPM
- FastCGI
- systemd service management
- Linux troubleshooting
- Service dependency troubleshooting
- Web server configuration
- Application/database integration

---

 # 📚 Useful Commands

 Check Zabbix server:

```
sudo systemctl status zabbix-server
```

 Check Agent 2:

```
sudo systemctl status zabbix-agent2
```

 Check Apache:

```
sudo systemctl status apache2
```

 Check PHP-FPM:

```
sudo systemctl status php8.5-fpm
```

 Check MySQL:

```
sudo systemctl status mysql
```

 Check Apache configuration:

```
sudo apache2ctl configtest
```

 Check Zabbix database:

```
mysql -uzabbix -p -e "SELECT * FROM zabbix.dbversion;"
```

 Check PHP version:

```
php -v
```

---

 # ✅ Project Status

 **Completed successfully.**

 The Zabbix 7.4.14 monitoring server was deployed on Ubuntu 26.04 LTS with MySQL 8.4, Apache, PHP 8.5-FPM, and Zabbix Agent 2.

 The Zabbix web interface is accessible and the core Zabbix services are operational.

---

 ## 👨‍💻 Portfolio Highlights

 > **Deployed and troubleshot a production-style Zabbix monitoring stack on Ubuntu 26.04, integrating Zabbix Server with MySQL 8.4, Apache, PHP 8.5-FPM, and Zabbix Agent 2. Diagnosed repository dependency conflicts, database schema initialization issues, Apache module errors, and PHP-FPM/FCGI configuration problems to achieve a fully functional monitoring web interface.**
