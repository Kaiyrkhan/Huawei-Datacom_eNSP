# Network Automation. Python

## Configure Remote Access (Telnet, SSH)

Configure Local User Authentication and Authorization
```shell
aaa
 local-user user1 password irreversible-cipher Huawei@123
 local-user user1 service-type terminal ssh
 local-user user1 privilege level 15
```

```shell
ssh user student authentication-type password
ssh user student service-type stelnet
```

Configure VTY Lines
```shell
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound all
```

Generate RSA Key
```shell
rsa local-key-pair create

Warning: Confirm to replace them! Continue? [Y/N] Y
Input the bits in the modulus[default = 3072]: 2048

display rsa local-key-pair public
```

Enable SSH
```shell
stelnet server enable

display ssh server status
display telnet server status
```

```shell
student@ubuntu:~$ sudo nano ~/.ssh/config
Host 172.16.128.11
    KexAlgorithms +diffie-hellman-group1-sha1
    HostKeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
    Ciphers +aes128-cbc
CTRL+O, ENTER, CTRL+X
```

```shell
student@ubuntu:~$ ssh user1@172.16.128.11
student@ubuntu:~$ telnet 172.16.128.11
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```
