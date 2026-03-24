# Eth-Trunk and MSTP configuration in eNSP Simulator

**HQ-D1 and HQ-D2**

```shell
vlan batch 10 20

display vlan
```

Step1: Eth-Trunk конфигурациялау
```shell
interface Eth-Trunk1
 mode lacp-static
 port link-type trunk
 port trunk allow-pass vlan 10 20
```

Step2: Физикалық порттарды Eth-Trunk-қа қосу
```shell
interface g0/0/1
 eth-trunk 1

interface g0/0/2
 eth-trunk 1
```

```shell
interface Eth-Trunk1
load-balance src-dst-mac
```

Step3: MSTP instance mapping
```shell
stp mode mstp
stp region-configuration
 region-name HQ
 revision-level 1
 instance 1 vlan 10
 instance 2 vlan 20
 active region-configuration
```

Root placement (load-balancing)

**HQ-D1**
```shell
stp instance 1 root primary
stp instance 2 root secondary
```

**HQ-D2**
```shell
stp instance 2 root primary
stp instance 1 root secondary
```

Step4: Тексеру (Verification)
```shell
display eth-trunk 1

display stp brief

display stp instance 1 brief
display stp instance 2 brief

display stp vlan 10
display stp vlan 20
```

```shell
display interface eth-trunk 1
display lacp statistics eth-trunk 1
```

# Eth-Trunk and MSTP configuration in a Production Environment

**HQ-D1 and HQ-D2**

Step1: Eth-Trunk конфигурациялау
```shell
interface Eth-Trunk1
 mode lacp-static
 port link-type trunk
 port trunk allow-pass vlan 10 20
 stp disable
```

Step2: Физикалық порттарды Eth-Trunk-қа қосу
```shell
interface g0/0/1
 eth-trunk 1
 stp disable

interface g0/0/2
 eth-trunk 1
 stp disable
```
