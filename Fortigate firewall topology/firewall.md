# cyber-security-attacks-test
Analyzing cyber security attacks with PnetLab for a bachelor's project
___

## Fortigate firewall topology

![alt text](../blob/firewall/0.jpg)

##### Setup firewall:
    ➢ username : admin
    ➢ password : hit Enter if not been set, then set a new password
    ➢ Show system interfaces
    ➢ config system interfaces
    ➢ edit port1 (if it’s necessary)

![alt text](../blob/firewall/1.1.jpg)
![alt text](../blob/firewall/1.2.jpg)

- Login through wed browser:
![alt text](../blob/firewall/7.jpg)
![alt text](../blob/firewall/1.jpg)

- Setup DNS to be able to transfer Internet throughout the entire network:
![alt text](../blob/firewall/2.jpg)
![alt text](../blob/firewall/3.jpg)

- Setup Network interfaces:
![alt text](../blob/firewall/5.jpg)
![alt text](../blob/firewall/6.jpg)
![alt text](../blob/firewall/7.jpg)

- Setup Static Routes to create connection between gateway and port1:
![alt text](../blob/firewall/8.jpg)
- Setup Firewall Policies:
![alt text](../blob/firewall/9.jpg)
![alt text](../blob/firewall/10.jpg)
![alt text](../blob/firewall/11.jpg)
![alt text](../blob/firewall/12.jpg)

- Setup dhcp server for inter-communication:
![alt text](../blob/firewall/13.jpg)

- From kali tinycore, and from control panel > network tab, set dhcp broadcast to yes:
![alt text](../blob/firewall/14.jpg)

- After done these requirements, we should be able to use the Internet:
![alt text](../blob/firewall/15.jpg)

- From our firewall logs and report tab > froward traffic:
![alt text](../blob/firewall/16.jpg)

- From another side of the network, first we need to check the IP address of PC1, and then try to connect linux tinycore from other side of the network:
![alt text](../blob/firewall/17.jpg)
![alt text](../blob/firewall/18.jpg)

- Again view our traffic through “forward traffic” tab:
![alt text](../blob/firewall/19.jpg)

- Test inter-connections:
![alt text](../blob/firewall/20.jpg)
![alt text](../blob/firewall/21.jpg)
![alt text](../blob/firewall/22.jpg)


___