#### VPN. GRE. DmVPN
###  




##### Цель:

* Настроить GRE между офисами Москва и С.-Петербург
* Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги


#### Схема сети:

  ![alt-текст](/lab-9/img/bgp.png)


###### Настраиваем GRE между офисами Москва и С.-Петербург
<details>
<summary>config-R14</summary>

  ```
interface Tunnel100
 description GRE-R18
 ip address 10.1.2.14 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 14.14.14.14
 tunnel destination 18.18.18.18
!
  ```
</details>

<details>
<summary>config-R15</summary>

  ```
  interface Tunnel101
   description GRE-R18
   ip address 10.1.3.15 255.255.255.0
   ip mtu 1400
   ip tcp adjust-mss 1360
   tunnel source 15.15.15.15
   tunnel destination 18.18.18.18
  !
  ```
</details>

<details>
<summary>config-R18</summary>

  ```
  interface Tunnel100
   description GRE-R14
   ip address 10.1.2.18 255.255.255.0
   ip mtu 1400
   ip tcp adjust-mss 1360
   tunnel source 18.18.18.18
   tunnel destination 14.14.14.14
  !
  interface Tunnel101
   ip address 10.1.3.18 255.255.255.0
   ip mtu 1400
   ip tcp adjust-mss 1360
   tunnel source 18.18.18.18
   tunnel destination 15.15.15.15
  !
  ```
</details>

<details>
<summary>Проверим GRE тоннели на R18</summary>

### Вывод команды сокращён


  ```
  R18#show interface Tunnel100
  Tunnel100 is up, line protocol is up
    Hardware is Tunnel
    Description: GRE-R14
    Internet address is 10.1.2.18/24
    MTU 17916 bytes, BW 100 Kbit/sec, DLY 50000 usec,
       reliability 255/255, txload 1/255, rxload 1/255
    Tunnel linestate evaluation up
    Tunnel source 18.18.18.18, destination 14.14.14.14
    Tunnel protocol/transport GRE/IP
    Tunnel transport MTU 1476 bytes

R18#show interface Tunnel101
    Tunnel101 is up, line protocol is up
      Hardware is Tunnel
      Internet address is 10.1.3.18/24
      MTU 17916 bytes, BW 100 Kbit/sec, DLY 50000 usec,
         reliability 255/255, txload 1/255, rxload 1/255
      Tunnel linestate evaluation up
      Tunnel source 18.18.18.18, destination 15.15.15.15
      Tunnel protocol/transport GRE/IP
      Tunnel transport MTU 1476 bytes


  ```
</details>

<details>
<summary>Сделаем PING через тоннели</summary>

```
R18#ping 10.1.2.14
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.2.14, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R18#ping 10.1.3.15
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.3.15, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R18#

```
</details>


###### Настраиваем DMVPN между офисами Москва и Чокурдах, Лабытнанги

<details>
<summary>config-R14</summary>

  ```
  interface Tunnel200
 ip address 10.1.200.14 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp authentication OTUSlab
 ip nhrp map multicast dynamic
 ip nhrp network-id 200
 ip tcp adjust-mss 1360
 tunnel source Loopback1
 tunnel mode gre multipoint
!
  ```
</details>

<details>
<summary>config-R27</summary>

  ```
  interface Tunnel200
 ip address 10.1.200.27 255.255.255.0
 ip mtu 1400
 ip nhrp authentication OTUSlab
 ip nhrp map multicast 14.14.14.14
 ip nhrp map 10.1.200.14 14.14.14.14
 ip nhrp network-id 200
 ip nhrp nhs 10.1.200.14
 ip tcp adjust-mss 1360
 tunnel source Loopback1
 tunnel destination 14.14.14.14
!

  ```
</details>
<details>
<summary>config-R28</summary>

  ```
  interface Tunnel200
   ip address 10.1.200.28 255.255.255.0
   ip mtu 1400
   ip nhrp authentication OTUSlab
   ip nhrp map multicast 14.14.14.14
   ip nhrp map 10.1.200.14 14.14.14.14
   ip nhrp network-id 200
   ip nhrp nhs 10.1.200.14
   ip tcp adjust-mss 1360
   tunnel source Ethernet0/0
   tunnel destination 14.14.14.14
  !
  ```
</details>

<details>
<summary>Проверим DMVPN на R14</summary>

### Вывод команды сокращён


  ```
  R14#show dmvpn
Legend: Attrb --> S - Static, D - Dynamic, I - Incomplete
N - NATed, L - Local, X - No Socket
# Ent --> Number of NHRP entries with same NBMA peer
NHS Status: E --> Expecting Replies, R --> Responding, W --> Waiting
UpDn Time --> Up or Down Time for a Tunnel
==========================================================================

Interface: Tunnel200, IPv4 NHRP Details
Type:Hub, NHRP Peers:2,

 # Ent  Peer NBMA Addr Peer Tunnel Add State  UpDn Tm Attrb
 ----- --------------- --------------- ----- -------- -----
     1 27.27.27.27         10.1.200.27    UP 01:00:34     D
     1 10.4.0.26           10.1.200.28    UP 00:56:14     D

R14#show ip nhrp
   10.1.200.27/32 via 10.1.200.27
      Tunnel200 created 01:00:49, expire 01:39:10
      Type: dynamic, Flags: unique registered used nhop
      NBMA address: 27.27.27.27
   10.1.200.28/32 via 10.1.200.28
      Tunnel200 created 00:56:29, expire 01:25:21
      Type: dynamic, Flags: unique registered used nhop
      NBMA address: 10.4.0.26
 R14#

  ```
</details>
