# Remote Access Configuration using SSH

#### Huawei AR6140E-9G-2AC Router

```shell
ssh server-source -i Vlanif 1
немесе
ssh server-source all-interface
```

```shell
rsa local-key-pair create
Confirm to replace them? (y/n)[n]: y
Input the bits in the modulus[default = 2048]: 2048
```

```shell
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound ssh
 user privilege level 15
```

```shell
aaa
 local-user student password irreversible-cipher Huawei@123
 local-user student service-type terminal ssh
 local-user student privilege level 15
```
```shell
[Huawei] ssh user student service-type stelnet
[Huawei] ssh user student authentication-type password
```

```shell
stelnet server enable
display ssh server status
```
