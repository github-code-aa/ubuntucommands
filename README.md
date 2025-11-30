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

