# Remote Access Configuration using SSH/STelnet

#### Huawei AR6140E-9G-2AC Router

```shell
rsa local-key-pair create
Confirm to replace them? (y/n)[n]: y
Input the bits in the modulus[default = 2048]: 2048
```

```shell
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound ssh

display privilege state
```
> user-interface vty 0 4  
> user privilege level 15  

```shell
aaa
 local-user student password irreversible-cipher Huawei@123
 local-user student service-type terminal ssh
 local-user student privilege level 15
```

```shell
ssh server-source -i Vlanif 1
немесе
ssh server-source all-interface
```
> ssh server permit interface GigabitEthernet 0/0/0  
> ssh server permit interface all  

```shell
[Huawei] ssh user student
[Huawei] ssh user student service-type stelnet
[Huawei] ssh user student authentication-type password
```
> ssh user student service-type all  

```shell
stelnet server enable

display ssh server status
display current-configuration | include ssh
```
