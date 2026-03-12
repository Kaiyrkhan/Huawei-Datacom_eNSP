# Remote AAA configuration using HWTACACS
> AAA (Authentication, Authorization, Accounting)  

### 🖧 Network Topology
![Topology](images/Lab8_NetworkTopology_Remote_AAA.png)  

### Scenario:
1) Basic Device configuration
     - IP Address configuration
2) Create HWTACACS Server template
3) AAA Scheme configuration
4) AAA Domain configuration
5) SSH Enable
6) VTY configuration
7) Backup Local Admin (Failover)

```shell
student@ubuntu:~$ sudo nano /etc/netplan/50-cloud-init.yaml
network:
  version: 2
  ethernets:
    ens32:
      dhcp4: true
    ens34:
      dhcp4: false
      addresses: [172.16.128.10/24]
CTRL+O, ENTER, CTRL+X

student@ubuntu:~$ sudo netplan apply

student@ubuntu:~$ ip address
ens32: DHCP Assigned
ens34: 172.16.128.10/24
```

```shell
student@ubuntu:~$ sudo apt update
student@ubuntu:~$ sudo apt install -y build-essential flex bison libwrap0-dev libpcre2-dev libssl-dev zlib1g-dev
```

```shell
student@ubuntu:~$ git clone https://github.com/MarcJHuber/event-driven-servers.git
```

```shell
student@ubuntu:~$ cd event-driven-servers
student@ubuntu:~/event-driven-servers$ ./configure tac_plus
student@ubuntu:~/event-driven-servers$ make
student@ubuntu:~/event-driven-servers$ sudo make install

student@ubuntu:~$ which tac_plus
/usr/local/sbin/tac_plus
```

```shell
student@ubuntu:~$ sudo nano /etc/tac_plus.conf

id = spawnd {
    listen = { port = 49 }
}

id = tacacs {
    key = Huawei123

    user = user1 {
        password = cleartext Huawei@123
        member = admin
    }
	
    user = user2 {
        password = cleartext Huawei@123
        member = operator
    }	

    user = user3 {
        password = cleartext Huawei@123
        member = readonly
    }

    group = admin {
        service = exec {
            priv-lvl = 15
        }
    }

    group = operator {
        service = exec {
            priv-lvl = 5
        }
    }

    group = readonly {
        service = exec {
            priv-lvl = 1
        }
    }
}

CTRL+O, ENTER, CTRL+X
```

```shell
student@ubuntu:~$ sudo tac_plus -b /etc/tac_plus.conf
student@ubuntu:~$ ss -lntp | grep 49
немесе
student@ubuntu:~$ sudo apt install net-tools
student@ubuntu:~$ netstat -an | grep 49
```

**Create Daemon Service**
```shell
student@ubuntu:~$ sudo nano /etc/systemd/system/tac_plus.service
[Unit]
Description=TACACS+ Server
After=network.target

[Service]
ExecStart=/usr/local/sbin/tac_plus /etc/tac_plus.conf
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```
```shell
student@ubuntu:~$ sudo systemctl daemon-reload
student@ubuntu:~$ sudo systemctl start tac_plus
student@ubuntu:~$ sudo systemctl enable tac_plus
student@ubuntu:~$ sudo systemctl status tac_plus
```

```shell
student@ubuntu:~$ sudo radtest user1 Huawei@123 127.0.0.1 0 testing123
Access-Accept
```

---

**Create HWTACACS Server template**
```shell
[R1] hwtacacs enable

hwtacacs-server template HT
 hwtacacs-server authentication 172.16.128.10 49
 hwtacacs-server authorization 172.16.128.10 49
 hwtacacs-server accounting 172.16.128.10 49
 hwtacacs-server shared-key cipher Huawei123
```

**AAA Scheme configuration**
```shell
aaa
authentication-scheme HWTACACS
 authentication-mode hwtacacs local

authorization-scheme HWTACACS
 authorization-mode hwtacacs local

accounting-scheme HWTACACS
 accounting-mode hwtacacs
 accounting start-fail online
 accounting realtime 3
```

**AAA Domain configuration**
```shell
aaa
domain LAB.LOCAL
authentication-scheme HWTACACS
authorization-scheme HWTACACS
accounting-scheme HWTACACS
hwtacacs-server HT
```

*Configure the global default domain for administrations*
```shell
[R1] domain LAB.LOCAL admin
```

**Verification**
```shell
display hwtacacs-server template HT
display domain name LAB.LOCAL
```

**SSH enable**
```shell
stelnet server enable
display ssh server status

rsa local-key-pair create
```

**VTY configuration**
```shell
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound ssh
```

**Backup Local Admin (Failover)**
```shell
aaa
 local-user student password irreversible-cipher Huawei@123
 local-user student service-type terminal ssh
 local-user student privilege level 15
```

**Verification**
```shell
[R1] test-aaa user1 Huawei@123 radius-template HT

[R3] ssh user1@172.16.128.11
```

### References
1) [Example for Configuring HWTACACS Authentication, Accounting, and Authorization](https://support.huawei.com/enterprise/en/doc/EDOC1000178178/74e8248d/example-for-configuring-hwtacacs-authentication-accounting-and-authorization)
