# Remote AAA configuration using RADIUS
> AAA (Authentication, Authorization, Accounting)  

### 🖧 Network Topology
![Topology](images/Remote_AAA.png)  

## Scenario:
1) Basic Device configuration
     - IP Address configuration
2) Create RADIUS Server template
3) AAA Scheme configuration
4) AAA Domain configuration
5) SSH Enable
6) VTY configuration
7) Backup Local Admin (Failover)

student@debian:~$ sudo radtest user1 Huawei@123 127.0.0.1 0 testing123
