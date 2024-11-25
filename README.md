# cyber-security-attacks-test
Analyzing cyber security attacks with PnetLab for a bachelor's project
___

## Preview
- [DOS attack: ICMP Flood Attack](#dos-attack-icmp-flood-attack)
- [DDOS attack: SYN Flood Attack](#ddos-attack-syn-flood-attack)
- [MITM attack](#mitm-attack)
- [VLAN Hopping: double tagging method](#vlan-hopping-double-tagging-method)
- [Fortigate firewall topology](#fortigate-firewall-topology)

___


## DOS ATTACK: ICMP Flood Attack

![schema](blob/dos/photo_2024-11-23_17-09-01.jpg)

### Kali Configuration:

1. On Kali Linux, Open Network connection window and click on Ethernet, then wired connection.
2. Under IPv4 settings tab, add a new static address:
    e.g. 
    | Address | Netmask | Gateway |
    | ------- | ------- | ------- |
    |192.168.1.1|24     |         |
3. Then Save the settings.
4. To check if IP was assigned: go to terminal and type below command:
    ```
    $ sudo su
    ```
    then : 
    ```
    $ ifconfig
    ```

### Router Configuration:

- From router terminal enter below commands:

```
Router> en
Router#
Router# config t "or terminal"
Router(config)#
Router(config)# hostname R1
R1(config)#
R1(config)# interface f0/0
R1(config-if)#
R1(config-if)# ip address 192.168.1.10 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)#
R1(config)# exit
R1#
R1# do wr
```

- To check the connection, we could ping from either router or kali:
```
R1# ping 192.168.1.1

or 

$ ping 192.168.1.10
```
- Before the attack, check the router cpu performance:
```
R1# show processes cpu
```
![schema](blob/dos/photo_2024-11-23_17-08-52.jpg)
- From kali linux:
```
$ hping3 -1 --flood -1 192.168.1.10
```
![schema](blob/dos/photo_2024-11-23_17-08-35.jpg)

$note:$ if we run this commad for more than 2 minutes, the target device will go down.

- The check the router cpu performance again:

![schema](blob/dos/photo_2024-11-23_17-08-57.jpg)

- From kali wireshark or the embedded one on pnetlab we could observe huge amount of ECHO was sent to the target device.

- From router:
```
R1# debug ip icmp

R1# u all
```

___

## DDOS attack: SYN Flood Attack
![schema1](blob/ddos/photo_2024-11-23_17-10-33.jpg)
- First we config the router:

```
Router> en
Router#
Router# config t "or terminal"
Router(config)#
Router(config)# hostname SRV
SRV(config)#
SRV(config)# interface f0/0
SRV(config-if)#
SRV(config-if)# ip address dhcp
SRV(config-if)# no shutdown
SRV(config-if)# exit
SRV(config)# ip http ser
SRV(config)# ip http authentication local
SRV(config)# username admin privilege 15 password 123
SRV(config)# do wr
SRV(config)# ^Z (ctrl + Z)
SRV# show ip int br

```
- Let's find out our kali linux ip:
    ```
    $ sudo su
    ```
    then : 
    ```
    $ ifconfig
    ```
- now we can access the SRV throuhg browser:

![schema2](blob/ddos/photo_2024-11-23_17-10-25.jpg)

- If we check wireshark we could capture syn , [syn, ack], and ack as the three way handshaking of TCP:
![schema3](blob/ddos/SYN-ACK.png)

- Now we are ready to attack:
```
hping3 -c 15000 -d 120 -w 64 -p 80 --flood --rand-source 192.168.128.140 
```

- The results are shown below:

![schema4](blob/ddos/photo_2024-11-23_17-10-40.jpg)
![schema5](blob/ddos/photo_2024-11-23_17-10-43.jpg)
![schema6](blob/ddos/photo_2024-11-23_17-10-47.jpg)
- Now if we stop the attack after a few seconds, SRV is back to normal again. (pay attention to the time!):
![schema7](blob/ddos/photo_2024-11-23_17-10-51.jpg)

___

## MITM attack

- MITM (Man in The Middle) means man in the middle of your conversation.
- In a Man-in-The-Middle attack, attackers place themselves between two devices.
- MITM attack to intercept or modify communications between the two devices.
- MITM cyberattacks allow attackers to secretly intercept communications.
- MITM attack happens when hacker inserts themselves between a user & apps.
- Attackers have many different reasons and methods for using a MITM attack.
- MITM is used to steal something, like credit card numbers or user login credentials.
- MITM attacks involve interception of communication between two digital systems.
- A MITM attack is when an attacker intercepts communications between two parties.
- The man-in-the middle attack intercepts a communication between two systems.

![schema](blob/mitm/Screenshot-2024-11-23-203157.png)

### config R1 and R2 :

```
Router> en
Router#
Router# config t "or terminal"
Router(config)#
Router(config)# hostname R1
R1(config)#
R1(config)# interface f0/0
R1(config-if)#
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# mac-address aaaa.aaaa.1111
R1(config-if)# no shutdown
R1(config-if)# do wr
R1(config-if)# exit
R1(config)# line vty 0 4
R1(config-line)#
R1(config-line)# transport input all
R1(config-line)# password 123
R1(config-line)# login
R1(config-line)# exit
R1(config)# 
R1(config)# enable password 123
R1(config)# do wr
```

- Same thing for R2 is done.
```
Router> en
Router#
Router# config t "or terminal"
Router(config)#
Router(config)# hostname R2
R2(config)#
R2(config)# interface f0/0
R2(config-if)#
R2(config-if)# ip address 192.168.1.2 255.255.255.0
R2(config-if)# mac-address aaaa.aaaa.2222
R2(config-if)# no shutdown
R2(config-if)# do wr
R2(config-if)# exit
R2(config)# line vty 0 4
R2(config-line)#
R2(config-line)# transport input all
R2(config-line)# password 123
R2(config-line)# login
R2(config-line)# exit
R2(config)# 
R2(config)# enable password 123
R2(config)# do wr
```
- On Kali Linux, Open Network connection window and click on Ethernet, then wired connection.
- Under IPv4 settings tab, add a new static address:
    e.g. 
    | Address | Netmask | Gateway |
    | ------- | ------- | ------- |
    |192.168.1.3|24     |         |
- Then Save the settings.
- Now on the terminal we setup mac address:
```
$ macchanger -m aa:aa:aa:aa:33:33 eth0
```
![schema](blob/mitm/photo_2024-11-23_17-09-06.jpg)

- Now we can attack as shown below:
![schema](blob/mitm/photo_2024-11-23_17-09-34.jpg)
![schema](blob/mitm/Screenshot-2024-11-23-205022.png)
![schema](blob/mitm/Screenshot-2024-11-23-205308.png)

- Now the results of the attack:

![schema](blob/mitm/photo_2024-11-23_17-09-13.jpg)
![schema](blob/mitm/photo_2024-11-23_17-09-17.jpg)
- Compare these two with the first one earlier:
![schema](blob/mitm/photo_2024-11-23_17-09-24.jpg)
![schema](blob/mitm/photo_2024-11-23_17-09-29.jpg)
___

## VLAN Hopping: double tagging method
- Yersinia is used to launch the attack, focusing on Dynamic Trunking Protocol (DTP) vulnerabilities.
- The DTP protocol attempts to establish trunking between switches, which can be exploited if switches are not properly configured.
- The switch is configured to allow trunking, enabling access to multiple VLANs through VLAN 1.
- Using a double encapsulation attack, access to the network can be gained easily.
- To prevent the attack, interfaces should be converted to access ports across specified ranges.
- The command "show IP interface brief" can be used to monitor and manage the interfaces.
- Setting the port to access mode and using 'switchport nonegotiate' halts DTP packets from being sent.
- Creating a new VLAN instead of using the default VLAN 1 helps in managing network traffic more effectively.
- Verifying the configuration with 'show interface trunk' confirms that no trunks are present anymore.
- This method can be used to perform VLAN hopping attacks and mitigate them effectively using tools like Kali Linux.

![alt text](blob/vlan/photo_of_vlan_topology.png)

### Install Yersinia On Kali Linux

- [How to install yersinia on kali ?](https://www.youtube.com/watch?v=v6CGKLXeKlA)

#### Before the Attack
- We shouldn't trunking with anybody:
![alt text](blob/vlan/photo_2024-11-23_17-09-58.jpg)

#### Launching yersinia 

![alt text](blob/vlan/photo_2024-11-23_17-09-54.jpg)

- result:
```
SW# show interfaces trunk
```
![alt text](blob/vlan/photo_2024-11-23_17-09-47.jpg)

#### How to prevent that from happening ?

```
SW(config-if-range)#switchport nonegotiate
SW(config-if-range)#exit
SW(config)#vlan 5
SW(config-vlan)#int range e0/0-3
(config-if-range)#switchport access vlan 5
SW(config-if-range)#do show int trunk
SW(config-if-range)#
```
![alt text](blob/vlan/photo_2024-11-23_17-10-03.jpg)

___

## Fortigate firewall topology

![alt text](blob/firewall/0.jpg)

##### Setup firewall:
    ➢ username : admin
    ➢ password : hit Enter if not been set, then set a new password
    ➢ Show system interfaces
    ➢ config system interfaces
    ➢ edit port1 (if it’s necessary)

![alt text](blob/firewall/1.1.jpg)
![alt text](blob/firewall/1.2.jpg)

- Login through wed browser:
![alt text](blob/firewall/7.jpg)
![alt text](blob/firewall/1.jpg)

- Setup DNS to be able to transfer Internet throughout the entire network:
![alt text](blob/firewall/2.jpg)
![alt text](blob/firewall/3.jpg)

- Setup Network interfaces:
![alt text](blob/firewall/5.jpg)
![alt text](blob/firewall/6.jpg)
![alt text](blob/firewall/7.jpg)

- Setup Static Routes to create connection between gateway and port1:
![alt text](blob/firewall/8.jpg)
- Setup Firewall Policies:
![alt text](blob/firewall/9.jpg)
![alt text](blob/firewall/10.jpg)
![alt text](blob/firewall/11.jpg)
![alt text](blob/firewall/12.jpg)

- Setup dhcp server for inter-communication:
![alt text](blob/firewall/13.jpg)

- From kali tinycore, and from control panel > network tab, set dhcp broadcast to yes:
![alt text](blob/firewall/14.jpg)

- After done these requirements, we should be able to use the Internet:
![alt text](blob/firewall/15.jpg)

- From our firewall logs and report tab > froward traffic:
![alt text](blob/firewall/16.jpg)

- From another side of the network, first we need to check the IP address of PC1, and then try to connect linux tinycore from other side of the network:
![alt text](blob/firewall/17.jpg)
![alt text](blob/firewall/18.jpg)

- Again view our traffic through “forward traffic” tab:
![alt text](blob/firewall/19.jpg)

- Test inter-connections:
![alt text](blob/firewall/20.jpg)
![alt text](blob/firewall/21.jpg)
![alt text](blob/firewall/22.jpg)


___
