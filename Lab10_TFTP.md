# Configure TFTP Server on Debian

> TFTP - Trivial File Transfer Protocol Server  

About the System
```shell
$ uname -rs
$ lsb_release -a
$ cat /etc/debian_version
```

**Step1: installation of TFTP Server**

> **tftpd-hpa** – HPA's TFTP Server  
> **tftp-hpa** – HPA's TFTP Client  

```shell
$ sudo apt update
$ sudo apt install -y tftpd-hpa tftp-hpa
```

```shell
$ sudo systemctl status tftpd-hpa
$ sudo systemctl is-enabled tftpd-hpa
```

**Step2: Configure the TFTP Server**
```shell
Edit tftpd-hpa Configuration File
$ sudo nano /etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/srv/tftp"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
to
TFTP_OPTIONS="--secure --create"
```
> **TFTP root directory:** /srv/tftp  

```shell
Modify Permissions on TFTP Root Directory
$ sudo chown -R tftp /var/lib/tftpboot
```

```shell
Restart the tftpd-hpa Service
$ sudo systemctl restart tftpd-hpa
```

**Firewall Configuration**
```shell
-A INPUT -s 192.168.1.0/24 -m tcp -p tcp --dport 69 -j ACCEPT
-A INPUT -s 192.168.1.0/24 -m tcp -p udp  --dport 69 -j ACCEPT
```

```shell
$ ss -tulpn

$ sudo apt install -y net-tools
$ netstat -tulpn
```

```shell
$ sudo chown -R tftp:tftp /srv/tftp
немесе
$ sudo chown -R nobody:nogroup /srv/tftp

$ sudo chmod 777 -R tftp /srv/tftp
$ ls -ld /srv/tftp
$ ls -lR /srv/tftp
```

Testing the TFTP Server
```shell
student@tftp-server:~$ touch /srv/tftp/file1
student@tftp-client:~$ touch file2
```
Download and Upload files
```shell
tftp 172.16.128.69
tftp> ?
tftp> verbose
tftp> get file1
tftp> put file2
tftp> quit
```
> get - Download file from TFTP server  
> put - Upload file from TFTP server  

Example: Cisco IOS
```shell
R1# copy running-config tftp:

student@tftp-server:~$ ls -l /srv/tftp/
```

## Additional Information

**DHCP option 150 and DHCP option 66**

*DHCP Option 150 is Cisco proprietary. Option 66 is an IEEE standard. Like option 150, option 66 is used to specify the Name of the TFTP server.*  

*Option 66 is an open standard juniper supports it. RFC 2132 defines option 66.*  

*For Cisco phones IP addresses can be assigned manually or by using DHCP. Devices also require access to a TFTP server that contains device configuration name files (.cnf file format), which enables the device to communicate with Cisco Call Manager.*  

*Cisco IP Phones download their configuration from a TFTP server. When a Cisco IP Phone starts, if it does not have both the IP address and TFTP server IP address pre configured, it sends a request with option 150 to the DHCP server to obtain this information.*  

Difference between option 150 and option 66:  
  - DHCP option 150 supports a list of TFTP servers (Multiple Server IPs);  
  - DHCP option 66 only supports the IP address or the hostname of a single TFTP server.  
