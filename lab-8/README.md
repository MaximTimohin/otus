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
!
interface Ethernet0/1
 description R17
 ip address 10.5.0.2 255.255.255.252
 !
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
!
interface Ethernet0/1
 description R18
 ip address 10.5.0.1 255.255.255.252
!
interface Ethernet0/2
 description Sw10
 no ip address
!
interface Ethernet0/2.14
 encapsulation dot1Q 14
 ip address 10.5.0.21 255.255.255.252
!
router eigrp SP
 !
 address-family ipv4 unicast autonomous-system 2042
  !
  topology base
   auto-summary
  exit-af-topology
  network 10.5.0.0 0.0.0.3
  network 10.5.0.16 0.0.0.3
  network 10.5.0.20 0.0.0.3
  network 10.5.129.0 0.0.0.255
  network 17.17.17.17 0.0.0.0
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
!
interface Ethernet0/1
 description R18
 ip address 10.5.0.5 255.255.255.252
!
interface Ethernet0/2
 description SW9
 no ip address
!
interface Ethernet0/2.15
 encapsulation dot1Q 15
 ip address 10.5.0.25 255.255.255.252
!
interface Ethernet0/3
 description R32
 ip address 10.5.0.33 255.255.255.252
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
  topology base
   auto-summary
   redistribute static
  exit-af-topology
  network 10.5.0.0 0.0.255.255
  network 16.16.16.16 0.0.0.0
  eigrp router-id 16.16.16.16
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
!
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
```
</details>


##### Настраиваем SW-9
<details>
<summary>config-SW-9</summary>

```
interface Loopback1
 ip address 9.9.9.9 255.255.255.255
!
interface Ethernet0/0
!
interface Ethernet0/2
 switchport access vlan 997
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
 interface Vlan14
  ip address 10.5.0.18 255.255.255.252
 !
 interface Vlan15
  ip address 10.5.0.26 255.255.255.252
 !         
 interface Vlan997
  ip address 10.5.129.1 255.255.255.0
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
!
interface Vlan15
 ip address 10.5.0.30 255.255.255.252
!
interface Vlan997
 ip address 10.5.128.1 255.255.255.0
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
```
</details>


####   

##### Проверяем результат настройки:

###### eigrp neighbors
<details>
<summary>R18 ip eigrp neighbors</summary>

```
R18#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   10.5.0.5                Et0/0                    14 01:58:12    1   100  0  92
0   10.5.0.1                Et0/1                    13 02:04:22    1   100  0  60
R18#
```
</details>


<details>
<summary>R17 ip eigrp neighbors</summary>

```
R17#show ip eigrp ne
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
2   10.5.0.22               Et0/2.14                 12 00:41:04   11   100  0  23
1   10.5.0.18               Et0/0.14                 10 00:41:07    6   100  0  20
0   10.5.0.2                Et0/1                    12 02:07:16    1   100  0  121
R17#
```
</details>


<details>
<summary>R16 ip eigrp neighbors</summary>

```
R16#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
3   10.5.0.34               Et0/3                    12 00:37:57    8   100  0  7
2   10.5.0.26               Et0/2.15                 11 00:55:20    1   100  0  21
1   10.5.0.30               Et0/0.15                 12 01:06:57    3   100  0  24
0   10.5.0.6                Et0/1                    14 02:01:23    1   100  0  120
R16#
```
</details>


<details>
<summary>R32 ip eigrp neighbors</summary>

```
R32#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
0   10.5.0.33               Et0/0                    11 00:38:16    5   100  0  89
R32#
```
</details>


<details>
<summary>SW-9 ip eigrp neighbors</summary>

```
SW9#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   10.5.0.17               Vl14                     12 00:41:58    3   100  0  61
0   10.5.0.25               Vl15                     13 00:55:54    1   100  0  91
SW9#
```
</details>


<details>
<summary>SW-10 ip eigrp neighbors</summary>

```
SW10#show ip eigrp neighbors
EIGRP-IPv4 VR(SP) Address-Family Neighbors for AS(2042)
H   Address                 Interface              Hold Uptime   SRTT   RTO  Q  Seq
                                                   (sec)         (ms)       Cnt Num
1   10.5.0.21               Vl14                     12 00:42:13    5   100  0  62
0   10.5.0.29               Vl15                     10 01:07:49    2   100  0  93
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
D        9.9.9.9 [90/1536640] via 10.5.0.5, 00:46:28, Ethernet0/0
                 [90/1536640] via 10.5.0.1, 00:46:28, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 16 subnets, 3 masks
D        10.5.0.16/30 [90/1536000] via 10.5.0.1, 00:46:28, Ethernet0/1
D        10.5.0.20/30 [90/1536000] via 10.5.0.1, 00:46:25, Ethernet0/1
D        10.5.0.24/30 [90/1536000] via 10.5.0.5, 00:46:28, Ethernet0/0
D        10.5.0.28/30 [90/1536000] via 10.5.0.5, 00:46:25, Ethernet0/0
D        10.5.0.32/30 [90/1536000] via 10.5.0.5, 00:46:28, Ethernet0/0
D        10.5.128.0/24 [90/1541120] via 10.5.0.5, 00:46:25, Ethernet0/0
                       [90/1541120] via 10.5.0.1, 00:46:25, Ethernet0/1
D        10.5.129.0/24 [90/1541120] via 10.5.0.5, 00:46:28, Ethernet0/0
                       [90/1541120] via 10.5.0.1, 00:46:28, Ethernet0/1
D        10.10.10.10/32 [90/1536640] via 10.5.0.5, 00:46:25, Ethernet0/0
                        [90/1536640] via 10.5.0.1, 00:46:25, Ethernet0/1
D     16.0.0.0/8 [90/1024640] via 10.5.0.5, 00:46:28, Ethernet0/0
D     17.0.0.0/8 [90/1024640] via 10.5.0.1, 00:46:28, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1536640] via 10.5.0.5, 00:42:33, Ethernet0/0
R18#
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
```
</details>


<details>
<summary>R17 ip route</summary>

```
R17#show ip route eigrp | begin Gateway
Gateway of last resort is 10.5.0.2 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1536000] via 10.5.0.2, 00:47:10, Ethernet0/1
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1024640] via 10.5.0.18, 00:47:10, Ethernet0/0.14
      10.0.0.0/8 is variably subnetted, 16 subnets, 4 masks
D        10.0.0.0/8 is a summary, 01:29:44, Null0
D        10.5.0.4/30 [90/1536000] via 10.5.0.2, 00:47:10, Ethernet0/1
D        10.5.0.8/30 [90/1536000] via 10.5.0.2, 00:47:10, Ethernet0/1
D        10.5.0.12/30 [90/1536000] via 10.5.0.2, 00:47:10, Ethernet0/1
D        10.5.0.24/30 [90/1029120] via 10.5.0.18, 00:47:10, Ethernet0/0.14
D        10.5.0.28/30 [90/1029120] via 10.5.0.22, 00:47:10, Ethernet0/2.14
D        10.5.0.32/30 [90/1541120] via 10.5.0.22, 00:47:10, Ethernet0/2.14
                      [90/1541120] via 10.5.0.18, 00:47:10, Ethernet0/0.14
D        10.5.128.0/24 [90/1029120] via 10.5.0.22, 00:47:10, Ethernet0/2.14
D        10.5.129.0/24 [90/1029120] via 10.5.0.18, 00:47:10, Ethernet0/0.14
D        10.10.10.10/32 [90/1024640] via 10.5.0.22, 00:47:10, Ethernet0/2.14
      16.0.0.0/8 is subnetted, 1 subnets
D        16.0.0.0 [90/1029760] via 10.5.0.22, 00:47:10, Ethernet0/2.14
                  [90/1029760] via 10.5.0.18, 00:47:10, Ethernet0/0.14
      17.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        17.0.0.0/8 is a summary, 01:29:44, Null0
      18.0.0.0/32 is subnetted, 1 subnets
D        18.18.18.18 [90/1024640] via 10.5.0.2, 00:15:21, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1541760] via 10.5.0.22, 00:43:18, Ethernet0/2.14
                     [90/1541760] via 10.5.0.18, 00:43:18, Ethernet0/0.14
R17#

```
</details>


<details>
<summary>R16 ip route</summary>

```
R16#show ip route eigrp | beg Gateway
Gateway of last resort is 0.0.0.0 to network 0.0.0.0

D*    0.0.0.0/0 is a summary, 00:42:00, Null0
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1024640] via 10.5.0.26, 00:59:58, Ethernet0/2.15
      10.0.0.0/8 is variably subnetted, 17 subnets, 4 masks
D        10.0.0.0/8 is a summary, 01:39:46, Null0
D        10.5.0.0/30 [90/1536000] via 10.5.0.6, 00:47:35, Ethernet0/1
D        10.5.0.8/30 [90/1536000] via 10.5.0.6, 01:34:12, Ethernet0/1
D        10.5.0.12/30 [90/1536000] via 10.5.0.6, 01:33:50, Ethernet0/1
D        10.5.0.16/30 [90/1029120] via 10.5.0.26, 00:47:35, Ethernet0/2.15
D        10.5.0.20/30 [90/1029120] via 10.5.0.30, 00:47:35, Ethernet0/0.15
D        10.5.128.0/24 [90/1029120] via 10.5.0.30, 01:10:32, Ethernet0/0.15
D        10.5.129.0/24 [90/1029120] via 10.5.0.26, 01:01:25, Ethernet0/2.15
D        10.10.10.10/32 [90/1024640] via 10.5.0.30, 00:58:08, Ethernet0/0.15
      16.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
D        16.0.0.0/8 is a summary, 01:39:46, Null0
      17.0.0.0/8 is subnetted, 1 subnets
D        17.0.0.0 [90/1029760] via 10.5.0.30, 00:47:35, Ethernet0/0.15
                  [90/1029760] via 10.5.0.26, 00:47:35, Ethernet0/2.15
      18.0.0.0/32 is subnetted, 1 subnets
D        18.18.18.18 [90/1024640] via 10.5.0.6, 00:15:45, Ethernet0/1
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1024640] via 10.5.0.34, 00:43:43, Ethernet0/3
R16#
```
</details>


<details>
<summary>R32 ip route</summary>

```
R32#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.33 to network 0.0.0.0

D*    0.0.0.0/0 [90/1024640] via 10.5.0.33, 00:42:25, Ethernet0/0
R32#

```
</details>


<details>
<summary>SW-9 ip route</summary>

```
SW9#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.17 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1541120] via 10.5.0.17, 00:42:43, Vlan14
      10.0.0.0/8 is variably subnetted, 15 subnets, 3 masks
D        10.5.0.0/30 [90/1029120] via 10.5.0.17, 00:48:21, Vlan14
D        10.5.0.4/30 [90/1029120] via 10.5.0.25, 00:48:21, Vlan15
D        10.5.0.8/30 [90/1541120] via 10.5.0.25, 00:48:21, Vlan15
                     [90/1541120] via 10.5.0.17, 00:48:21, Vlan14
D        10.5.0.12/30 [90/1541120] via 10.5.0.25, 00:48:21, Vlan15
                      [90/1541120] via 10.5.0.17, 00:48:21, Vlan14
D        10.5.0.20/30 [90/1029120] via 10.5.0.17, 00:48:18, Vlan14
D        10.5.0.28/30 [90/1029120] via 10.5.0.25, 00:48:18, Vlan15
D        10.5.0.32/30 [90/1029120] via 10.5.0.25, 01:02:15, Vlan15
D        10.5.128.0/24 [90/1034240] via 10.5.0.25, 00:48:18, Vlan15
                       [90/1034240] via 10.5.0.17, 00:48:18, Vlan14
D        10.10.10.10/32 [90/1029760] via 10.5.0.25, 00:48:18, Vlan15
                        [90/1029760] via 10.5.0.17, 00:48:18, Vlan14
D     16.0.0.0/8 [90/10880] via 10.5.0.25, 01:02:15, Vlan15
D     17.0.0.0/8 [90/10880] via 10.5.0.17, 00:48:21, Vlan14
      18.0.0.0/32 is subnetted, 1 subnets
D        18.18.18.18 [90/1029760] via 10.5.0.25, 00:16:29, Vlan15
                     [90/1029760] via 10.5.0.17, 00:16:29, Vlan14
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1029760] via 10.5.0.25, 00:44:26, Vlan15
SW9#
```
</details>


<details>
<summary>SW-10 ip route</summary>

```
SW10#show ip route eigrp | beg Gateway
Gateway of last resort is 10.5.0.21 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1541120] via 10.5.0.21, 00:43:03, Vlan14
      9.0.0.0/32 is subnetted, 1 subnets
D        9.9.9.9 [90/1029760] via 10.5.0.29, 00:48:38, Vlan15
                 [90/1029760] via 10.5.0.21, 00:48:38, Vlan14
      10.0.0.0/8 is variably subnetted, 15 subnets, 3 masks
D        10.5.0.0/30 [90/1029120] via 10.5.0.21, 00:48:38, Vlan14
D        10.5.0.4/30 [90/1029120] via 10.5.0.29, 00:48:38, Vlan15
D        10.5.0.8/30 [90/1541120] via 10.5.0.29, 00:48:38, Vlan15
                     [90/1541120] via 10.5.0.21, 00:48:38, Vlan14
D        10.5.0.12/30 [90/1541120] via 10.5.0.29, 00:48:38, Vlan15
                      [90/1541120] via 10.5.0.21, 00:48:38, Vlan14
D        10.5.0.16/30 [90/1029120] via 10.5.0.21, 00:48:38, Vlan14
D        10.5.0.24/30 [90/1029120] via 10.5.0.29, 00:48:38, Vlan15
D        10.5.0.32/30 [90/1029120] via 10.5.0.29, 01:14:14, Vlan15
D        10.5.129.0/24 [90/1034240] via 10.5.0.29, 00:48:38, Vlan15
                       [90/1034240] via 10.5.0.21, 00:48:38, Vlan14
D     16.0.0.0/8 [90/10880] via 10.5.0.29, 01:14:14, Vlan15
D     17.0.0.0/8 [90/10880] via 10.5.0.21, 00:48:38, Vlan14
      18.0.0.0/32 is subnetted, 1 subnets
D        18.18.18.18 [90/1029760] via 10.5.0.29, 00:16:48, Vlan15
                     [90/1029760] via 10.5.0.21, 00:16:48, Vlan14
      32.0.0.0/32 is subnetted, 1 subnets
D        32.32.32.32 [90/1029760] via 10.5.0.29, 00:44:46, Vlan15
SW10#
```
</details>
