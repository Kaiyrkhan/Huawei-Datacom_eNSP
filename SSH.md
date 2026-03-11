# Remote Access Configuration using SSH

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
```
