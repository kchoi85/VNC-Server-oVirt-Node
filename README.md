# Connect to your oVirt Host Server with VNC
**From Windows 10**
![image](https://user-images.githubusercontent.com/52897657/82737389-d6f18480-9cfe-11ea-97c6-16a0f30fab80.png)    
.
## Requirements
- Complete setup of CentOS/oVirt-node server (Visit github.com/kchoi85/thisrepositorytosetupcentosserver)  
- VNC Windows client (https://www.realvnc.com/en/connect/download/viewer/windows/)  

## 1) Install tigervnc-server
- `yum install -y tigervnc-server`

## 2) Copy the vncserver config files
- `cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service`
> Note 
> CentOS6.x version's config files are located at `/etc/sysconfig/vncservers`  
> CentOS7.x config files are located at `/lib/systemd/system/vncserver@.service`  

## 3) Edit the copied config file
- `vi /etc/systemd/system/vncserver@\:1.service`
### Delete all the contents with `dd` command and paste the following:
User=root
[Unit]  
Description=Remote desktop service (VNC)  
After=syslog.target network.target  
[Service]  
Type=forking  
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'  
ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver %i"  
PIDFile=/root/.vnc/%H%i.pid  
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'  
[Install]  
WantedBy=multi-user.target

## 4) Restart the daemon and enable vncserver
- `systemctl daemon-reload`  
- `systemctl enable vncserver@:1.service`

## 5) Start the VNC Server (Input new password for login)
- `vncserver`

## 6) Disable firewall.service
- `Systemctl mask firewall.service`
- `Systemctl stop firewall.service`

## 7) Install and start iptables.service
- `yum –y install iptables-services`
- `systemctl enable iptables.service`
- `systemctl start iptables.service`

## 8) Add the VNC port (5901)
- `iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 5901 -j ACCEPT`
- `service iptables save`
- `systemctl restart iptables.service `

## 9) Log into your server 
- Connect at `host_ip_addr:5901`

## Architecture of VNC Server
![image](https://user-images.githubusercontent.com/52897657/82737864-d27a9b00-9d01-11ea-8582-e690c13fcc57.png) 
