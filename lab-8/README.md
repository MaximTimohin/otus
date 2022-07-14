#### EIGRP named
Настраиваем EIGRP в офисе Санкт-Петербург.


* R32 получает только маршрут по умолчанию.
* R16-17 анонсируют только суммарные префиксы.
* Проверяем результаты настройки

#### Схема сети:

  ![alt-текст](/lab-8/img/2042.png)

##### Настраиваем R18
<details>
<summary>config-R18</summary>

```
interface Loopback1
 ip address 18.18.18.18 255.255.255.255
!
interface Ethernet0/0
 description R16
 ip address 10.5.0.6 255.255.255.252
 ip nat inside
 ip virtual-reassembly in
 ipv6 address FE80::18 link-local
 ipv6 address 20FF:AAAA:BBBB:500::18/64
 ipv6 enable
!
interface Ethernet0/1
 description R17
 ip address 10.5.0.2 255.255.255.252
 ip nat inside
 ip virtual-reassembly in
 ipv6 address FE80::18 link-local
 ipv6 address 20FF:AAAA:BBBB:510::18/64
 ipv6 enable
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  topology base
   redistribute static
  exit-af-topology
  network 0.0.0.0
  network 10.5.0.0 0.0.0.3
  network 10.5.0.4 0.0.0.3
  eigrp router-id 18.18.18.18
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  topology base
  exit-af-topology
  eigrp router-id 18.18.18.18
 exit-address-family
!

```
</details>



##### Настраиваем R17
<details>
<summary>config-R17</summary>

```
interface Loopback1
 ip address 17.17.17.17 255.255.255.255
!
interface Ethernet0/0
 description SW9
 no ip address
!
interface Ethernet0/0.14
 encapsulation dot1Q 14
 ip address 10.5.0.17 255.255.255.252
 ipv6 address FE80::17 link-local
 ipv6 address 20FF:AAAA:BBBB:503::17/64
 ipv6 enable
!
interface Ethernet0/1
 description R18
 ip address 10.5.0.1 255.255.255.252
 ipv6 address FE80::17 link-local
 ipv6 address 20FF:AAAA:BBBB:510::17/64
 ipv6 enable
!
interface Ethernet0/2
 description Sw10
 no ip address
!
interface Ethernet0/2.14
 encapsulation dot1Q 14
 ip address 10.5.0.21 255.255.255.252
 ipv6 address FE80::17 link-local
 ipv6 address 20FF:AAAA:BBBB:504::17/64
 ipv6 enable
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  af-interface Ethernet0/1
   summary-address 10.5.0.16 255.255.255.248
   summary-address 10.5.128.0 255.255.254.0
  exit-af-interface
  !
  topology base
   auto-summary
   redistribute static
   redistribute connected
  exit-af-topology
  network 10.5.0.0 0.0.0.3
  network 10.5.0.16 0.0.0.7
  network 10.5.129.0 0.0.0.255
  network 17.17.17.17 0.0.0.0
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  af-interface Ethernet0/1
   summary-address 20FF:AAAA:BBBB:508::/63
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 17.17.17.17
 exit-address-family
!

```
</details>



##### Настраиваем R16
<details>
<summary>config-R16</summary>

```
interface Loopback1
 ip address 16.16.16.16 255.255.255.255
!
interface Ethernet0/0
 description SW10
 no ip address
!
interface Ethernet0/0.15
 encapsulation dot1Q 15
 ip address 10.5.0.29 255.255.255.252
 ipv6 address FE80::16 link-local
 ipv6 address 20FF:AAAA:BBBB:506::16/64
 ipv6 enable
!
interface Ethernet0/1
 description R18
 ip address 10.5.0.5 255.255.255.252
 ipv6 address FE80::16 link-local
 ipv6 address 20FF:AAAA:BBBB:500::16/64
!
interface Ethernet0/2
 description SW9
 no ip address
!
interface Ethernet0/2.15
 encapsulation dot1Q 15
 ip address 10.5.0.25 255.255.255.252
 ipv6 address FE80::16 link-local
 ipv6 address 20FF:AAAA:BBBB:505::16/64
 ipv6 enable
!
interface Ethernet0/3
 description R32
 ip address 10.5.0.33 255.255.255.252
 ipv6 address FE80::16 link-local
 ipv6 address 20FF:AAAA:BBBB:507::16/64
 ipv6 enable
!
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  af-interface Ethernet0/3
   summary-address 0.0.0.0 0.0.0.0
  exit-af-interface
  !
  af-interface Ethernet0/1
   summary-address 10.5.0.16 255.255.255.248
   summary-address 10.5.128.0 255.255.254.0
  exit-af-interface
  !
  topology base
   auto-summary
   redistribute static
   redistribute connected
  exit-af-topology
  network 10.5.0.4 0.0.0.3
  network 10.5.0.24 0.0.0.3
  network 10.5.0.28 0.0.0.3
  network 10.5.0.32 0.0.0.3
  network 10.5.128.0 0.0.0.255
  network 16.16.16.16 0.0.0.0
  eigrp router-id 16.16.16.16
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  af-interface Ethernet0/1
   summary-address 20FF:AAAA:BBBB:508::/63
  exit-af-interface
  !
  topology base
  exit-af-topology
 exit-address-family
!

```
</details>



##### Настраиваем R32
<details>
<summary>config-R32</summary>

```
interface Loopback1
 ip address 32.32.32.32 255.255.255.255
!
interface Ethernet0/0
 description R16
 ip address 10.5.0.34 255.255.255.252
 ipv6 address FE80::32 link-local
 ipv6 address 20FF:AAAA:BBBB:507::7/64
 ipv6 enable
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  topology base
  exit-af-topology
  network 10.5.0.32 0.0.0.3
  network 32.32.32.32 0.0.0.0
  eigrp router-id 32.32.32.32
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  topology base
  exit-af-topology
 exit-address-family
!

```
</details>


##### Настраиваем SW-9
<details>
<summary>config-SW-9</summary>

```
interface Loopback1
 ip address 9.9.9.9 255.255.255.255
!
interface Ethernet0/3
 description R17
 switchport trunk allowed vlan 14
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 description R16
 switchport trunk allowed vlan 15
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Vlan14
 ip address 10.5.0.18 255.255.255.252
 ipv6 address FE80::9 link-local
 ipv6 address 20FF:AAAA:BBBB:503::9/64
 ipv6 enable
!
interface Vlan15
 ip address 10.5.0.26 255.255.255.252
 ipv6 address FE80::9 link-local
 ipv6 address 20FF:AAAA:BBBB:505::9/64
 ipv6 enable
!
interface Vlan997
 ip address 10.5.129.1 255.255.255.0
 ipv6 address FE80::9 link-local
 ipv6 address 20FF:AAAA:BBBB:508::1/64
 ipv6 enable
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  topology base
   redistribute connected
  exit-af-topology
  network 9.9.9.9 0.0.0.0
  network 10.5.0.16 0.0.0.3
  network 10.5.0.24 0.0.0.3
  network 10.5.129.0 0.0.0.255
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  topology base
  exit-af-topology
 exit-address-family
!


```
</details>


##### Настраиваем SW-10
<details>
<summary>config-SW-10</summary>

```
interface Loopback1
 ip address 10.10.10.10 255.255.255.255
!
interface Ethernet0/0
!
interface Ethernet0/1
!
interface Ethernet0/2
 switchport access vlan 997
!
interface Ethernet0/3
 switchport trunk allowed vlan 15
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 switchport trunk allowed vlan 14
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Vlan14
 ip address 10.5.0.22 255.255.255.252
 ipv6 address FE80::10 link-local
 ipv6 address 20FF:AAAA:BBBB:504::10/64
 ipv6 enable
!
interface Vlan15
 ip address 10.5.0.30 255.255.255.252
 ipv6 address FE80::10 link-local
 ipv6 address 20FF:AAAA:BBBB:506::10/64
 ipv6 enable
!
interface Vlan997
 ip address 10.5.128.1 255.255.255.0
 ipv6 address FE80::10 link-local
 ipv6 address 20FF:AAAA:BBBB:509::1/64
 ipv6 enable
!
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  topology base
   redistribute connected
  exit-af-topology
  network 10.5.0.20 0.0.0.3
  network 10.5.0.28 0.0.0.3
  network 10.5.128.0 0.0.0.255
  network 10.10.10.10 0.0.0.0
  eigrp router-id 10.10.10.10
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 2042
  !
  topology base
  exit-af-topology
 exit-address-family
!

```
</details>


####   

##### Проверяем результат настройки:

###### eigrp neighbors
<details>
<summary>R18 eigrp neighbors</summary>

```
R18#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   10.5.0.5                Et0/0                    13 04:39:04    3   100  0  154
0   10.5.0.1                Et0/1                    14 04:39:04    1   100  0  110
R18#show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   Link-local address:     Et0/0                    13 00:30:51    8   100  0  30
    FE80::16
0   Link-local address:     Et0/1                    12 00:38:31    5   100  0  27
    FE80::17
R18#

```
</details>


<details>
<summary>R17 eigrp neighbors</summary>

```
R17#              show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   10.5.0.18               Et0/0.14                 10 02:56:52    4   100  0  87
1   10.5.0.22               Et0/2.14                 12 02:56:52    5   100  0  87
0   10.5.0.2                Et0/1                    12 04:39:38    9   100  0  68
R17#              show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   Link-local address:     Et0/2.14                 14 00:28:14    5   100  0  14
    FE80::10
1   Link-local address:     Et0/0.14                 11 00:35:12    2   100  0  20
    FE80::9
0   Link-local address:     Et0/1                    14 00:39:07    2   100  0  24
    FE80::18
R17#

```
</details>


<details>
<summary>R16 eigrp neighbors</summary>

```
R16#           show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   10.5.0.6                Et0/1                    10 04:40:03    1   100  0  71
3   10.5.0.30               Et0/0.15                 13 2d03h       2   100  0  85
2   10.5.0.26               Et0/2.15                 10 2d03h       2   100  0  85
0   10.5.0.34               Et0/3                    11 2d03h       3   100  0  11
R16#           show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
3   Link-local address:     Et0/3                    13 00:07:26    7   100  0  4
    FE80::32
2   Link-local address:     Et0/0.15                 13 00:28:37    6   100  0  13
    FE80::10
1   Link-local address:     Et0/2.15                 11 00:31:49    5   100  0  21
    FE80::9
0   Link-local address:     Et0/1                    12 00:31:49    6   100  0  23
    FE80::18
R16#

```
</details>


<details>
<summary>R32 eigrp neighbors</summary>

```
R32#          show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   10.5.0.33               Et0/0                    14 2d03h       4   100  0  152
R32#          show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   Link-local address:     Et0/0                    11 00:07:55    5   100  0  31
    FE80::16

```
</details>


<details>
<summary>SW-9 eigrp neighbors</summary>

```
SW9#           show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   10.5.0.17               Vl14                     13 02:58:17    3   100  0  109
1   10.5.0.25               Vl15                     14 2d03h       1   100  0  156
SW9#           show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   Link-local address:     Vl15                     13 00:32:50    5   100  0  29
    FE80::16
0   Link-local address:     Vl14                     12 00:36:36    5   100  0  28
    FE80::17
SW9#

```
</details>


<details>
<summary>SW-10 eigrp neighbors</summary>

```
SW10#              show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   10.5.0.21               Vl14                     11 02:58:39    5   100  0  108
1   10.5.0.29               Vl15                     10 2d03h       1   100  0  155
SW10#              show ipv6 eigrp neighbors
EIGRP-IPv6 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   Link-local address:     Vl14                     13 00:29:59    8   100  0  29
    FE80::17
0   Link-local address:     Vl15                     10 00:29:59    6   100  0  28
    FE80::16
SW10#

```
</details>


#####  

######  ip route


<details>
<summary>R18 ip route</summary>

```
R18#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.10 to network 0.0.0.0

      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1536640] via 10.5.0.5, 03:00:27, Ethernet0/0
                 [90/1536640] via 10.5.0.1, 03:00:27, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 26 subnets, 6 masks
D        10.5.0.16/29 [90/1536000] via 10.5.0.1, 00:28:13, Ethernet0/1
D        10.5.0.24/30 [90/1536000] via 10.5.0.5, 00:22:20, Ethernet0/0
D        10.5.0.28/30 [90/1536000] via 10.5.0.5, 00:22:20, Ethernet0/0
D        10.5.0.32/30 [90/1536000] via 10.5.0.5, 03:00:27, Ethernet0/0
D        10.5.128.0/23 [90/1536000] via 10.5.0.5, 00:24:47, Ethernet0/0
                       [90/1536000] via 10.5.0.1, 00:24:47, Ethernet0/1
D        10.10.10.10/32 [90/1536640] via 10.5.0.5, 03:00:27, Ethernet0/0
                        [90/1536640] via 10.5.0.1, 03:00:27, Ethernet0/1
D     16.0.0.0/8 [90/1024640] via 10.5.0.5, 03:00:27, Ethernet0/0
D     17.0.0.0/8 [90/1024640] via 10.5.0.1, 02:51:34, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1536640] via 10.5.0.5, 03:00:27, Ethernet0/0


R18#show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:503::/64 [90/1536000]
     via FE80::17, Ethernet0/1
D   20FF:AAAA:BBBB:504::/64 [90/1536000]
     via FE80::17, Ethernet0/1
D   20FF:AAAA:BBBB:505::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:506::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:507::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:508::/63 [90/1541120]
     via FE80::17, Ethernet0/1
     via FE80::16, Ethernet0/0

```
</details>


<details>
<summary>R17 ip route</summary>

```
R17#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.2 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1536000] via 10.5.0.2, 04:44:43, Ethernet0/1
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1024640] via 10.5.0.18, 03:01:59, Ethernet0/0.14
      10.0.0.0/8 is variably subnetted, 21 subnets, 7 masks
D        10.0.0.0/8 is a summary, 02:53:06, Null0
D        10.1.2.0/24 [90/77312000] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.1.3.0/24 [90/77312000] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.5.0.0/16 [90/1024512] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.5.0.4/30 [90/1536000] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.5.0.8/30 [90/1536000] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.5.0.12/30 [90/1536000] via 10.5.0.2, 03:01:59, Ethernet0/1
D        10.5.0.16/29 is a summary, 00:31:27, Null0
D        10.5.0.24/30 [90/1029120] via 10.5.0.18, 00:23:52, Ethernet0/0.14
D        10.5.0.28/30 [90/1029120] via 10.5.0.22, 00:23:52, Ethernet0/2.14
D        10.5.0.32/30 [90/1541120] via 10.5.0.22, 03:01:59, Ethernet0/2.14
                      [90/1541120] via 10.5.0.18, 03:01:59, Ethernet0/0.14
D        10.5.128.0/23 is a summary, 00:27:11, Null0
D        10.5.128.0/24 [90/1029120] via 10.5.0.22, 00:27:10, Ethernet0/2.14
D        10.10.10.10/32 [90/1024640] via 10.5.0.22, 03:01:59, Ethernet0/2.14
D     16.0.0.0/8 [90/1029760] via 10.5.0.22, 03:01:59, Ethernet0/2.14
                 [90/1029760] via 10.5.0.18, 03:01:59, Ethernet0/0.14
      17.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        17.0.0.0/8 is a summary, 02:53:06, Null0
      18.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        18.18.18.0/24 [90/1024512] via 10.5.0.2, 03:01:59, Ethernet0/1
D        18.18.18.18/32 [90/1024640] via 10.5.0.2, 03:01:59, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1541760] via 10.5.0.22, 03:01:59, Ethernet0/2.14
                     [90/1541760] via 10.5.0.18, 03:01:59, Ethernet0/0.14
R17#show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:500::/64 [90/1536000]
     via FE80::18, Ethernet0/1
D   20FF:AAAA:BBBB:505::/64 [90/1029120]
     via FE80::9, Ethernet0/0.14
D   20FF:AAAA:BBBB:506::/64 [90/1029120]
     via FE80::10, Ethernet0/2.14
D   20FF:AAAA:BBBB:507::/64 [90/1541120]
     via FE80::10, Ethernet0/2.14
     via FE80::9, Ethernet0/0.14
D   20FF:AAAA:BBBB:508::/63 [5/1029120]
     via Null0, directly connected
D   20FF:AAAA:BBBB:508::/64 [90/1029120]
     via FE80::9, Ethernet0/0.14
D   20FF:AAAA:BBBB:509::/64 [90/1029120]
     via FE80::10, Ethernet0/2.14
R17#

```
</details>

<details>
<summary>R16 ip route</summary>

```
R16#show ip route eigrp | beg Gateway
Gateway of last resort is 0.0.0.0 to network 0.0.0.0

D*    0.0.0.0/0 is a summary, 05:18:22, Null0
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1024640] via 10.5.0.26, 2d05h, Ethernet0/2.15
      10.0.0.0/8 is variably subnetted, 22 subnets, 7 masks
D        10.0.0.0/8 is a summary, 2d05h, Null0
D        10.1.2.0/24 [90/77312000] via 10.5.0.6, 07:01:04, Ethernet0/1
D        10.1.3.0/24 [90/77312000] via 10.5.0.6, 07:01:04, Ethernet0/1
D        10.5.0.0/16 [90/1024512] via 10.5.0.6, 07:01:06, Ethernet0/1
D        10.5.0.0/30 [90/1536000] via 10.5.0.6, 05:18:22, Ethernet0/1
D        10.5.0.8/30 [90/1536000] via 10.5.0.6, 07:01:06, Ethernet0/1
D        10.5.0.12/30 [90/1536000] via 10.5.0.6, 07:01:06, Ethernet0/1
D        10.5.0.16/29 is a summary, 02:46:08, Null0
D        10.5.0.16/30 [90/1029120] via 10.5.0.26, 02:47:50, Ethernet0/2.15
D        10.5.0.20/30 [90/1029120] via 10.5.0.30, 02:47:50, Ethernet0/0.15
D        10.5.128.0/23 is a summary, 02:42:42, Null0
D        10.5.129.0/24 [90/1029120] via 10.5.0.26, 2d05h, Ethernet0/2.15
D        10.10.10.10/32 [90/1024640] via 10.5.0.30, 2d05h, Ethernet0/0.15
      16.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        16.0.0.0/8 is a summary, 2d05h, Null0
      17.0.0.0/8 is subnetted, 1 subnets
D        17.0.0.0 [90/1029760] via 10.5.0.30, 05:09:29, Ethernet0/0.15
                  [90/1029760] via 10.5.0.26, 05:09:29, Ethernet0/2.15
      18.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        18.18.18.0/24 [90/1024512] via 10.5.0.6, 07:01:06, Ethernet0/1
D        18.18.18.18/32 [90/1024640] via 10.5.0.6, 07:01:06, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1024640] via 10.5.0.34, 2d05h, Ethernet0/3
R16#show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:503::/64 [90/1029120]
     via FE80::9, Ethernet0/2.15
D   20FF:AAAA:BBBB:504::/64 [90/1029120]
     via FE80::10, Ethernet0/0.15
D   20FF:AAAA:BBBB:508::/63 [5/1029120]
     via Null0, directly connected
D   20FF:AAAA:BBBB:508::/64 [90/1029120]
     via FE80::9, Ethernet0/2.15
D   20FF:AAAA:BBBB:509::/64 [90/1029120]
     via FE80::10, Ethernet0/0.15
D   20FF:AAAA:BBBB:510::/64 [90/1536000]
     via FE80::18, Ethernet0/1
R16#

```
</details>
<details>
<summary>R32 ip route</summary>

```
R32#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.33 to network 0.0.0.0

D*    0.0.0.0/0 [90/1024640] via 10.5.0.33, 2d05h, Ethernet0/0
R32#show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:500::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:503::/64 [90/1541120]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:504::/64 [90/1541120]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:505::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:506::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:508::/64 [90/1541120]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:509::/64 [90/1541120]
     via FE80::16, Ethernet0/0
D   20FF:AAAA:BBBB:510::/64 [90/2048000]
     via FE80::16, Ethernet0/0
R32#


```
</details>
<details>
<summary>SW10 ip route</summary>

```
SW10#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.21 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1541120] via 10.5.0.21, 05:20:57, Vlan14
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1029760] via 10.5.0.29, 05:20:57, Vlan15
                 [90/1029760] via 10.5.0.21, 05:20:57, Vlan14
      10.0.0.0/8 is variably subnetted, 18 subnets, 4 masks
D        10.1.2.0/24 [90/77317120] via 10.5.0.29, 05:20:57, Vlan15
                     [90/77317120] via 10.5.0.21, 05:20:57, Vlan14
D        10.1.3.0/24 [90/77317120] via 10.5.0.29, 05:20:57, Vlan15
                     [90/77317120] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.0/16 [90/1029632] via 10.5.0.29, 05:20:57, Vlan15
                     [90/1029632] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.0/30 [90/1029120] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.4/30 [90/1029120] via 10.5.0.29, 05:20:57, Vlan15
D        10.5.0.8/30 [90/1541120] via 10.5.0.29, 05:20:57, Vlan15
                     [90/1541120] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.12/30 [90/1541120] via 10.5.0.29, 05:20:57, Vlan15
                      [90/1541120] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.16/30 [90/1029120] via 10.5.0.21, 05:20:57, Vlan14
D        10.5.0.24/30 [90/1029120] via 10.5.0.29, 05:20:57, Vlan15
D        10.5.0.32/30 [90/1029120] via 10.5.0.29, 2d05h, Vlan15
D        10.5.129.0/24 [90/1034240] via 10.5.0.29, 05:20:57, Vlan15
D     16.0.0.0/8 [90/10880] via 10.5.0.29, 2d05h, Vlan15
D     17.0.0.0/8 [90/10880] via 10.5.0.21, 05:12:04, Vlan14
      18.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        18.18.18.0/24 [90/1029632] via 10.5.0.29, 05:20:57, Vlan15
                       [90/1029632] via 10.5.0.21, 05:20:57, Vlan14
D        18.18.18.18/32 [90/1029760] via 10.5.0.29, 05:20:57, Vlan15
                        [90/1029760] via 10.5.0.21, 05:20:57, Vlan14
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1029760] via 10.5.0.29, 2d05h, Vlan15
SW10# show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:500::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:503::/64 [90/1029120]
     via FE80::17, Vlan14
D   20FF:AAAA:BBBB:505::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:507::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:508::/64 [90/1034240]
     via FE80::16, Vlan15
     via FE80::17, Vlan14
D   20FF:AAAA:BBBB:510::/64 [90/1029120]
     via FE80::17, Vlan14
SW10#

```
</details>
<details>
<summary>SW9 ip route</summary>

```
SW9#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.17 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1541120] via 10.5.0.17, 05:21:53, Vlan14
      10.0.0.0/8 is variably subnetted, 18 subnets, 4 masks
D        10.1.2.0/24 [90/77317120] via 10.5.0.25, 05:21:53, Vlan15
                     [90/77317120] via 10.5.0.17, 05:21:53, Vlan14
D        10.1.3.0/24 [90/77317120] via 10.5.0.25, 05:21:53, Vlan15
                     [90/77317120] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.0/16 [90/1029632] via 10.5.0.25, 05:21:53, Vlan15
                     [90/1029632] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.0/30 [90/1029120] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.4/30 [90/1029120] via 10.5.0.25, 05:21:53, Vlan15
D        10.5.0.8/30 [90/1541120] via 10.5.0.25, 05:21:53, Vlan15
                     [90/1541120] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.12/30 [90/1541120] via 10.5.0.25, 05:21:53, Vlan15
                      [90/1541120] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.20/30 [90/1029120] via 10.5.0.17, 05:21:53, Vlan14
D        10.5.0.28/30 [90/1029120] via 10.5.0.25, 05:21:53, Vlan15
D        10.5.0.32/30 [90/1029120] via 10.5.0.25, 2d05h, Vlan15
D        10.5.128.0/24 [90/1034240] via 10.5.0.17, 05:21:53, Vlan14
D        10.10.10.10/32 [90/1029760] via 10.5.0.25, 05:21:53, Vlan15
                        [90/1029760] via 10.5.0.17, 05:21:53, Vlan14
D     16.0.0.0/8 [90/10880] via 10.5.0.25, 2d05h, Vlan15
D     17.0.0.0/8 [90/10880] via 10.5.0.17, 05:13:00, Vlan14
      18.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        18.18.18.0/24 [90/1029632] via 10.5.0.25, 05:21:53, Vlan15
                       [90/1029632] via 10.5.0.17, 05:21:53, Vlan14
D        18.18.18.18/32 [90/1029760] via 10.5.0.25, 05:21:53, Vlan15
                        [90/1029760] via 10.5.0.17, 05:21:53, Vlan14
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1029760] via 10.5.0.25, 2d05h, Vlan15
SW9#  show ipv6 route eigrp | beg \[
D   20FF:AAAA:BBBB:500::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:504::/64 [90/1029120]
     via FE80::17, Vlan14
D   20FF:AAAA:BBBB:506::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:507::/64 [90/1029120]
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:509::/64 [90/1034240]
     via FE80::17, Vlan14
     via FE80::16, Vlan15
D   20FF:AAAA:BBBB:510::/64 [90/1029120]
     via FE80::17, Vlan14

```
</details>
