# Configure TFTP Server on Debian/Ubuntu

> TFTP – Trivial File Transfer Protocol  

About the System
```shell
$ uname -rs
Linux 6.8.0-101-generic x86_64 GNU/Linux
$ lsb_release -a
Release: Ubuntu 24.04.4 LTS
Codename: noble
```

**Step1: installation of TFTP Server**

>  Package атауы: tftpd-hpa  
> Daemon/Service атауы: tftpd-hpa  

```shell
$ sudo apt update
$ sudo apt install -y tftpd-hpa tftp-hpa
```
> **tftpd-hpa** – HPA's TFTP Server  
> **tftp-hpa** – HPA's TFTP Client  

```shell
Status the tftpd-hpa Service/Daemon
$ sudo systemctl status tftpd-hpa
$ sudo systemctl is-enabled tftpd-hpa
```

**Step2: Configure the TFTP Server**

Edit tftpd-hpa Configuration File
```shell
$ sudo nano /etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/srv/tftp"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
to
TFTP_OPTIONS="--secure --create --listen --verbose" 
```

Modify Permission/Ownership on TFTP Root Directory
```shell
$ sudo mkdir -p /srv/tftp
```
> TFTP Root Directory: **/srv/tftp**  немесе **/var/lib/tftpboot**  

> Best practice (үздік тәжірибе) бойынша және Production ортасында ең дұрыс таңдау:  
>   - Linux дистрибутив RHEL/Rocky  болса, онда "/var/lib/tftpboot" дұрыс  
>   - Linux дистрибутив Debian/Ubuntu болса, онда "/srv/tftp" дұрыс  

```shell
Modify Ownership
$ sudo chown -R tftp /srv/tftp

Modify Permission
$ sudo chmod -R 755 /srv/tftp

$ ls -ld /srv/tftp
```

```shell
$ getent passwd | grep tftp
$ getent group | grep tftp

$ getent passwd | grep nobody
$ getent group | grep nogroup
```

Restart the tftpd-hpa Service/Daemon
```shell
$ sudo systemctl restart tftpd-hpa
```

```shell
$ sudo journalctl -u tftpd-hpa -f
```

**Step3: Configure the Firewall**

```shell
Configure UFW
$ sudo ufw enable

$ sudo ufw allow 69/udp
немесе
$ sudo ufw allow from 172.16.128.0/24 to any port 69 proto udp
$ sudo ufw deny 69/udp

$ sudo ufw reload
$ sudo ufw status
```

```shell
Configure iptables
-A INPUT -s 192.168.1.0/24 -m tcp -p tcp --dport 69 -j ACCEPT
-A INPUT -s 192.168.1.0/24 -m tcp -p udp  --dport 69 -j ACCEPT
```

```shell
$ ss -tulpn
```
```shell
$ sudo apt install -y net-tools
$ netstat -tulpn
```

**Step4: Testing the TFTP Server**

Download and Upload files
> get - Download file from TFTP server  
> put - Upload file from TFTP server  

```shell
student@tftp-server:~$ touch /srv/tftp/f1.conf
student@tftp-server:~$ tftp 127.0.0.1 -c get f1.conf
student@tftp-server:~$ ls -l
student@tftp-server:~$ pwd

student@tftp-server:~$ sudo journalctl -u tftpd-hpa -f
```

```shell
student@tftp-client:~$  sudo apt update
student@tftp-client:~$  sudo apt install -y tftp-hpa

student@tftp-client:~$ touch f2.conf

student@tftp-client:~$ tftp 172.16.128.69
tftp> ?
tftp> verbose
tftp> get f1.conf
tftp> put f2.conf
tftp> quit
```

Example: Huawei VRP
```shell
<Huawei> tftp <tftp-server-ip> get <remote-file> — Download file from TFTP server 

<Huawei> tftp 172.16.128.69 get f1.conf
<Huawei> dir
немесе
<Huawei> tftp 172.16.128.69 get f1.conf f11.cfg
<Huawei> dir
```

```shell
<Huawei> tftp <tftp-server-ip> put <local-file> — Upload file from TFTP server  

<Huawei> tftp 172.16.128.69 put vrpcfg.zip
student@tftp-server:~$ ls -l /srv/tftp/
```

Example: Cisco IOS
```shell
R1(config)# ip tftp source-interface g0/0/1

R1# dir ?
R1# dir system:
R1# copy running-config tftp:
Address or name of remote host []? 172.16.128.69
Destination filename [running-config]? R1-run-config

R1# dir nvram:
R1# copy startup-config tftp:
Address or name of remote host []? 172.16.128.69
Destination filename [startup-config]? R1-start-config

R1# dir flash:
R1# copy flash tftp:
Source filename []? firmware-file-name.bin
Address or name of remote host []? 172.16.128.69
Destination filename [firmware-file-name.bin]? Enter

student@tftp-server:~$ ls -lh /srv/tftp/
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
