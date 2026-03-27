# Configure TFTP Server on Ubuntu and TFTP Client on Huawei VRP

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

**DHCP option 150 and DHCP option 66**

*DHCP Option 150 is Cisco proprietary. Option 66 is an IEEE standard. Like option 150, option 66 is used to specify the Name of the TFTP server.*  

*Option 66 is an open standard juniper supports it. RFC 2132 defines option 66.*  

*For Cisco phones IP addresses can be assigned manually or by using DHCP. Devices also require access to a TFTP server that contains device configuration name files (.cnf file format), which enables the device to communicate with Cisco Call Manager.*  

*Cisco IP Phones download their configuration from a TFTP server. When a Cisco IP Phone starts, if it does not have both the IP address and TFTP server IP address pre configured, it sends a request with option 150 to the DHCP server to obtain this information.*  

Difference between option 150 and option 66:  
  - DHCP option 150 supports a list of TFTP servers (Multiple Server IPs);  
  - DHCP option 66 only supports the IP address or the hostname of a single TFTP server.  
