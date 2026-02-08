# Download / Copy through FTP

	##Install Package📦 
			
      sudo apt update
      sudo apt install lftp -y
      
    ##Connect through ftp credentials
        
	  lftp -u username,password ftp.yourhost.com
	  
	##fetch the desired file on location
        
	  get /rkgtechportal.zip -o /usr/backups/rkgtechportal.zip
		bye
		EOF
     
# Command to delete ssh details     

      del C:\Users\gupta\.ssh\known_hosts
      
# Useful Ubuntu Command   
   
- `uptime` → Shows how long the system has been running and current load averages.  
- `htop` → Interactive process viewer with real‑time CPU, memory, and process monitoring.  
- `systemctl status` → Displays overall systemd health and active service information.  
- `free -h` → Reports memory usage in human‑readable format.  
- `df -h` → Shows disk space usage across mounted filesystems in human‑readable format.  
- `vmstat 1` → Provides continuous CPU, memory, and I/O statistics updated every second.
-  `ps aux` → Lists all running processes with details.  
- `kill -9 <pid>` → Forcefully terminates a process by its PID.  
- `journalctl -xe` → Shows detailed system logs for troubleshooting.  
- `tail -f /var/log/syslog` → Continuously monitors system log updates in real time.  
- `du -sh *` → Displays disk usage of files/folders in the current directory.  
- `netstat -tulpn` → Lists active network connections and listening ports.  
- `ss -tulpn` → Modern alternative to netstat for socket and port info.  
- `ping <host>` → Tests connectivity to a host.  
- `traceroute <host>` → Shows the path packets take to reach a host.  
- `curl -I http://localhost` → Fetches HTTP headers to identify web server type.  
- `docker ps` → Lists running Docker containers.  
- `docker logs <container>` → Shows logs of a specific Docker container.  
- `crontab -l` → Lists scheduled cron jobs for the current user.  
- `uname -a` → Displays kernel version and system information.  
- `whoami` → Prints the current logged‑in user.  
- `hostname -I` → Shows the server’s IP addresses.  


# ZIP / UNZIP Commands

-   `zip file.zip myfile.txt`  → Creates a ZIP archive from a single file.​
    
-   `zip -r site.zip /var/www/mysite`  → Zips entire directory recursively (use  `-r`  flag)​
    
-   `zip site.zip *.js *.css`  → Zips multiple specific files or patterns.​
    
-   `zip -r backup.zip . -x "node_modules/*"`  → Zips current directory, excludes node_modules.​
    
-   `unzip file.zip`  → Extracts ZIP contents to current directory​
    
-   `unzip file.zip -d /var/www/html`  → Extracts to specific directory (creates if needed)​
    
-   `unzip -o file.zip`  → Extracts and overwrites files without prompting​
    
-   `unzip -l file.zip`  → Lists contents of ZIP without extracting​
    
-   `unzip -t file.zip`  → Tests ZIP file integrity (no extraction)​
    
-   `sudo apt install zip unzip -y`  → Installs ZIP tools if missing on Ubuntu

# Kill ALL web servers

`sudo systemctl stop nginx apache2 || true`
`sudo fuser -k 80/tcp 80/udp 443/tcp 443/udp`
`sudo fuser -k [::]:80 [::]:443`

**Verify ports FREE**
`sudo ss -tulpn | grep -E ':(80|443)'`
 *Expected: NOTHING returned*

 **Check no services running**
`sudo systemctl status nginx apache2 --no-pager`

# Install / Verify nginx
`sudo  apt update `
`sudo  apt  install nginx certbot python3-certbot-nginx -y `
`sudo systemctl enable nginx `
`sudo systemctl start nginx`
 `sudo systemctl status nginx`

#  Firewall - Open Web Ports

`sudo ufw allow 'Nginx Full'` (nginix must be installed)
`sudo ufw allow 80/tcp`
`sudo ufw allow 443/tcp`
`sudo ufw status`
***Expected: 80,443 ALLOW***

# Upload file via SCP
`scp -r . user@Ip:/var/www/masapi`

# Deploy React App (Zip Method)
**Create directory**
`sudo mkdir -p /var/www/appname`
`sudo chown -R $USER:$USER /var/www/appname`

**Upload your build.zip to server (via SFTP/SCP)
Then extract:**

`unzip /path/to/react-build.zip -d /var/www/appname/`
`sudo chown -R www-data:www-data /var/www/appname`
`sudo chmod -R 755 /var/www/appname`

**Verify**
`ls -la /var/www/appname/`  # index.html exists?

Configure Nginx
`sudo nano /etc/nginx/sites-available/appname`

Config

    server {
    listen 80;
    server_name domain.com www.domain.com;
    
    root /var/www/appname;
    index index.html;
    
    # React SPA routing (critical!)
    location / {
        try_files $uri $uri/ /index.html;
	    }
	}

**Enable site**

    sudo ln -s /etc/nginx/sites-available/appname /etc/nginx/sites-enabled/
    sudo rm -f /etc/nginx/sites-enabled/default
    sudo nginx -t
    sudo systemctl reload nginx
    
**Test HTTP**

    curl -I http://localhost
    curl -I http://domain.com
    curl ifconfig.me
   
   **SSL**
  ` sudo certbot --nginx -d domain.com -d www.domain.com`

## Install MSSQL on Docker
**Install Docker**

    sudo apt update && sudo apt install docker.io -y
    sudo systemctl enable --now docker

 **Create data directory**

    sudo mkdir -p /var/opt/mssql
    sudo chown 10001:0 /var/opt/mssql
    sudo chmod 777 /var/opt/mssql

# Install MSSQL Tools

 **Import Microsoft repo key**
`curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -`

 **Add Microsoft SQL Server tools repo**
`curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list`

 **Update and install**

    sudo apt-get update
    sudo apt-get install -y mssql-tools unixodbc-dev 

 **Run MSSQL 2022 (Ubuntu 22.04 base - 100% stable)**

    sudo docker run -d \
      --name mssql \
      -e "ACCEPT_EULA=Y" \
      -e "MSSQL_SA_PASSWORD=StrongPass123" \
      -p 1433:1433 \
      -v /var/opt/mssql:/var/opt/mssql \
      mcr.microsoft.com/mssql/server:2022-latest
   **Firewall**
  

     sudo ufw allow 1433/tcp
     sudo ufw status

# Restore Database on docker mssql

    curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | sudo gpg --dearmor -o /usr/share/keyrings/microsoft-prod.gpg
    curl https://packages.microsoft.com/config/ubuntu/22.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
    sudo apt update && sudo ACCEPT_EULA=Y apt install -y mssql-tools18 unixodbc-dev
    echo 'export PATH="$PATH:/opt/mssql-tools18/bin"' >> ~/.bashrc && source ~/.bashrc

 **Create backups dir in container**
`sudo docker exec mssql mkdir -p /var/opt/mssql/backups`

 **Copy your backup** 
`sudo docker cp /opt/mssql-backups/db_a90de0_rkgdb_11_29_2025.bak mssql:/var/opt/mssql/backups/`

**Restore your backup** 
    sudo docker exec -it mssql /opt/mssql-tools18/bin/sqlcmd -S localhost,1433 -U sa -P 'India@12345' -C -Q "
    USE master;
    GO
    RESTORE DATABASE rkgdb 
    FROM DISK = '/var/opt/mssql/backups/db_a90de0_rkgdb_11_29_2025.bak'
    WITH MOVE 'rkgdb_Data' TO '/var/opt/mssql/data/rkgdb.mdf',
         MOVE 'rkgdb_Log' TO '/var/opt/mssql/data/rkgdb.ldf',
         REPLACE;
    GO"
    
**Take your backup** 

    sudo docker exec -it mssql /opt/mssql-tools18/bin/sqlcmd -S localhost,1433 -U sa -P 'India@12345' -C -Q "
    BACKUP DATABASE rkgdb 
    TO DISK = '/var/opt/mssql/backups/rkgdb_host_backup.bak'
    WITH COMPRESSION, INIT;
    GO"

    sudo docker cp mssql:/var/opt/mssql/backups/rkgdb_host_backup.bak /opt/mssql-backups/

**Cleanup backup from docker**

    sudo docker exec mssql ls -la /var/opt/mssql/backups/
    sudo docker exec mssql rm /var/opt/mssql/backups/db_a90de0_rkgdb_11_29_2025.bak
   `sudo docker exec mssql rm -f /var/opt/mssql/backups/*.bak`

# setup app on pm2
 **Global install (recommended)** 

    sudo  npm  install -g pm2 
    Verify pm2 --version
    
    cd /var/www/webapp
    PORT=8080 pm2 start index.js --name webappname
    pm2 save pm2 startup
    pm2 status 



MSSQL Docker Automated Backup Setup

This document explains how to set up daily automated backups for SQL Server running inside Docker, including cleanup, verification, and retention.

1. Prerequisites

SQL Server running inside a Docker container

Container name: mssql

sqlcmd available inside container at:

/opt/mssql-tools18/bin/sqlcmd


Docker installed on VPS

Root or sudo access

Backup directory inside container:

/var/opt/mssql/backups


Backup directory on host:

/opt/mssql-backups

2. Backup Strategy

The backup process follows this order:

Delete all old backups inside the container

Take fresh backups of all user databases

Verify each backup using RESTORE VERIFYONLY

Copy backups to host (date-based folder)

Retain only last 7 days of backups on host

3. Backup Script
Script Location
/usr/local/bin/mssql_backup.sh

Script Content
#!/bin/bash

set -e

CONTAINER="mssql"
SA_PASSWORD="India@12345"
CONTAINER_BACKUP_DIR="/var/opt/mssql/backups"
HOST_BACKUP_DIR="/opt/mssql-backups"

DATE=$(date +%F)
TIME=$(date +%H%M%S)

echo "Starting MSSQL backup at $DATE $TIME"

# Create host directory for today
mkdir -p "$HOST_BACKUP_DIR/$DATE"

# 1. Remove old backups inside container
echo "Cleaning old backups inside container..."
sudo docker exec $CONTAINER bash -c "rm -f $CONTAINER_BACKUP_DIR/*.bak"

# 2. Fetch list of user databases
echo "Fetching database list..."
DATABASES=$(sudo docker exec $CONTAINER \
  /opt/mssql-tools18/bin/sqlcmd \
  -S localhost,1433 \
  -U sa \
  -P "$SA_PASSWORD" \
  -C \
  -h -1 -W \
  -Q "SET NOCOUNT ON;
      SELECT name FROM sys.databases
      WHERE name NOT IN ('master','model','msdb','tempdb')")

# 3. Backup and verify each database
for DB in $DATABASES
do
  BACKUP_FILE="${DB}_${DATE}_${TIME}.bak"
  BACKUP_PATH="$CONTAINER_BACKUP_DIR/$BACKUP_FILE"

  echo "Backing up database: $DB"

  sudo docker exec $CONTAINER \
    /opt/mssql-tools18/bin/sqlcmd \
    -S localhost,1433 \
    -U sa \
    -P "$SA_PASSWORD" \
    -C \
    -Q "BACKUP DATABASE [$DB]
        TO DISK = '$BACKUP_PATH'
        WITH COMPRESSION, INIT;"

  echo "Verifying backup for database: $DB"

  sudo docker exec $CONTAINER \
    /opt/mssql-tools18/bin/sqlcmd \
    -S localhost,1433 \
    -U sa \
    -P "$SA_PASSWORD" \
    -C \
    -Q "RESTORE VERIFYONLY FROM DISK = '$BACKUP_PATH';"
done

# 4. Copy backups from container to host
echo "Copying backups to host..."
sudo docker cp $CONTAINER:$CONTAINER_BACKUP_DIR/. "$HOST_BACKUP_DIR/$DATE/"

# 5. Keep only last 7 days of backups on host
echo "Cleaning host backups older than 7 days..."
find "$HOST_BACKUP_DIR" -mindepth 1 -maxdepth 1 -type d -mtime +7 -exec rm -rf {} \;

echo "Backup completed successfully at $(date)"

4. Make Script Executable
sudo chmod +x /usr/local/bin/mssql_backup.sh

5. Manual Test (Mandatory)

Run once manually to ensure everything works:

sudo /usr/local/bin/mssql_backup.sh


Verify:

# Inside container
sudo docker exec mssql ls /var/opt/mssql/backups

# On host
ls /opt/mssql-backups


Expected structure:

/opt/mssql-backups/
└── 2026-02-08/
    ├── db1_2026-02-08_020000.bak
    ├── db2_2026-02-08_020000.bak

6. Cron Job Setup (Daily Automation)
Edit root crontab
sudo crontab -e

Add this line
0 2 * * * /usr/local/bin/mssql_backup.sh >> /var/log/mssql_backup.log 2>&1

Meaning
Field	Value
Minute	0
Hour	2
Day	Every
Month	Every
Weekday	Every

➡ Runs daily at 2:00 AM (server time)

7. Verify Cron Job
sudo crontab -l


Expected output:

0 2 * * * /usr/local/bin/mssql_backup.sh >> /var/log/mssql_backup.log 2>&1

8. Logs

Cron output is written to:

/var/log/mssql_backup.log


View logs:

tail -50 /var/log/mssql_backup.log

