# Remote Access Configuration using SSH

## Huawei AR6140E-9G-2AC Router

```shell
stelnet server enable
display ssh server status
```

```shell
rsa local-key-pair create
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
ssh user student
ssh user student authentication-type password
ssh user student service-type stelnet
```
