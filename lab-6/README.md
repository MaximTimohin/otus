#### OSPF Фильтрация
Настраиваем OSPF в офисе Москва

* R14-R15 в зоне 0.  
* R12-R13 в зоне 10. Дополнительно должны получать default.
* R19 в зоне 101 и получает только default.
* R20 в зоне 102 и получает всё, кроме маршрутов до сетей зоны 101.
* Проверка результатов настройки.


#### Схема сети:

  ![alt-текст](/lab-6/img1/moscow.png)


##### Настраиваем R14-R15
  <details>
    <summary>config-R15</summary>

##### Интерфейсы

```
interface Loopback1
ip address 15.15.15.15 255.255.255.255
ip ospf 1 area 0
!
interface Ethernet0/0
 description R13
 ip address 10.1.1.21 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/1
 description R12
 ip address 10.1.1.17 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/2
 description R21
 ip address 10.1.1.29 255.255.255.252
!
interface Ethernet0/3
 description R20
 ip address 10.1.1.25 255.255.255.252
 ip ospf 1 area 102
!
interface Ethernet1/0
description R14-e1/0
ip address 10.1.1.49 255.255.255.252
ip ospf 1 area 0
!
```
##### ospf
```
router ospf 1
router-id 15.15.15.15
area 10 stub
area 102 filter-list prefix AREA102 in
!
ip prefix-list AREA102 seq 5 deny 19.19.19.19/32
ip prefix-list AREA102 seq 10 permit 0.0.0.0/0 le 32
```
</details>


  <details>
    <summary>config-R14</summary>

##### Интерфейсы

  ```
  interface Loopback1
 ip address 14.14.14.14 255.255.255.255
 ip ospf 1 area 0
!
interface Ethernet0/0
 description R12
 ip address 10.1.1.5 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/1
 description R13
 ip address 10.1.1.9 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/2
 description R22
 ip address 10.1.1.13 255.255.255.252
!
interface Ethernet0/3
 description R19
 ip address 10.1.1.1 255.255.255.252
 ip ospf 1 area 101
!
interface Ethernet1/0
 description R15-e1/0
 ip address 10.1.1.50 255.255.255.252
 ip ospf 1 area 0
!
  ```
##### ospf
  ```
  router ospf 1
   router-id 14.14.14.14
   area 10 stub
    area 101 stub no-summary
  ```
</details>


##### Настраиваем R12-R13
<details>
  <summary>config-R13</summary>

##### Интерфейсы

```
interface Loopback1
 ip address 13.13.13.13 255.255.255.255
 ip ospf 1 area 10
!
interface Ethernet0/2
 description R15
 ip address 10.1.1.22 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/3
 description R14
 ip address 10.1.1.10 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet1/0
 description R12-e1/0
 ip address 10.1.1.54 255.255.255.252
 ip ospf 1 area 10
!
```
##### ospf
```
router ospf 1
 router-id 13.13.13.13
 area 10 stub
```
</details>

<details>
  <summary>config-R12</summary>

##### Интерфейсы

```
interface Loopback1
 ip address 12.12.12.12 255.255.255.255
 ip ospf 1 area 10
!
interface Ethernet0/2
 description R14
 ip address 10.1.1.6 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet0/3
 description R15
 ip address 10.1.1.18 255.255.255.252
 ip ospf 1 area 10
!
interface Ethernet1/0
 description R13-e1/0
 ip address 10.1.1.53 255.255.255.252
 ip ospf 1 area 10
!
```
##### ospf
```
router ospf 1
 router-id 12.12.12.12
 area 10 stub
```
</details>


##### Настраиваем R19
<details>
  <summary>config-R19</summary>

##### Интерфейсы

```
interface Loopback1
 ip address 19.19.19.19 255.255.255.255
 ip ospf 1 area 101
!
interface Ethernet0/0
 description R14
 ip address 10.1.1.2 255.255.255.252
 ip ospf 1 area 101
!
```
##### ospf
```
router ospf 1
 router-id 19.19.19.19
 area 101 stub
```
</details>

##### Настраиваем R20
<details>
  <summary>config-R20</summary>

##### Интерфейсы

```
interface Ethernet0/0
 description R15
 ip address 10.1.1.26 255.255.255.252
 ip ospf 1 area 102
!
```
##### ospf
```
router ospf 1
 router-id 20.20.20.20
```
</details>

### Проверка результатов настройки.
<details>
  <summary>R14 ip ospf neighbour</summary>

```
R14#show ip ospf neighbor  

Neighbor ID     Pri   State           Dead Time   Address         Interface
15.15.15.15       1   FULL/BDR        00:00:36    10.1.1.49       Ethernet1/0
12.12.12.12       1   FULL/BDR        00:00:33    10.1.1.6        Ethernet0/0
13.13.13.13       1   FULL/BDR        00:00:32    10.1.1.10       Ethernet0/1
19.19.19.19       1   FULL/DR         00:00:34    10.1.1.2        Ethernet0/3
```
  </details>
  <details>
    <summary>R14 ip ospf database</summary>

  ```
  R14#show ip ospf database

            OSPF Router with ID (14.14.14.14) (Process ID 1)

Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
14.14.14.14     14.14.14.14     552         0x800000E9 0x00C841 2
15.15.15.15     15.15.15.15     435         0x800000E7 0x008878 2

Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.50       14.14.14.14     1559        0x800000D8 0x007703

Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.0        14.14.14.14     802         0x80000003 0x009857
10.1.1.4        14.14.14.14     552         0x800000D7 0x00C650
10.1.1.4        15.15.15.15     435         0x80000004 0x00B428
10.1.1.8        14.14.14.14     312         0x80000004 0x0046A0
10.1.1.8        15.15.15.15     1431        0x800000D7 0x00E420
10.1.1.16       14.14.14.14     312         0x80000004 0x005A7A
10.1.1.16       15.15.15.15     1431        0x800000D7 0x0030D6
10.1.1.20       14.14.14.14     312         0x80000004 0x00329E
10.1.1.20       15.15.15.15     1431        0x800000D7 0x0008FA
10.1.1.24       15.15.15.15     1938        0x80000002 0x008B49
10.1.1.52       14.14.14.14     312         0x80000004 0x00F0BF
10.1.1.52       15.15.15.15     1431        0x800000D7 0x002BAD
12.12.12.12     14.14.14.14     312         0x80000004 0x0022A4
12.12.12.12     15.15.15.15     435         0x80000004 0x0004BE
13.13.13.13     14.14.14.14     312         0x80000004 0x00F3CE
13.13.13.13     15.15.15.15     676         0x80000004 0x00D5E8
19.19.19.19     14.14.14.14     1823        0x80000002 0x00E2C9

Router Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum Link count
12.12.12.12     12.12.12.12     315         0x800000EA 0x000587 4
13.13.13.13     13.13.13.13     245         0x800000E5 0x00D79C 4
14.14.14.14     14.14.14.14     312         0x800000DE 0x005225 2
15.15.15.15     15.15.15.15     436         0x800000DD 0x000B35 2

Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.5        14.14.14.14     312         0x800000DB 0x00BCF5
10.1.1.9        14.14.14.14     312         0x800000D9 0x00CAE1
10.1.1.17       15.15.15.15     436         0x800000DB 0x004856
10.1.1.21       15.15.15.15     436         0x800000D9 0x005642
10.1.1.54       13.13.13.13     245         0x800000DA 0x00CEBB

Summary Net Link States (Area 10)
Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     312         0x80000004 0x0006FC
0.0.0.0         15.15.15.15     436         0x80000004 0x00E717
10.1.1.0        14.14.14.14     802         0x80000003 0x00B63B
10.1.1.0        15.15.15.15     677         0x80000003 0x00FCE6
10.1.1.24       14.14.14.14     1823        0x80000002 0x002CA4
10.1.1.24       15.15.15.15     1939        0x80000002 0x00A92D
10.1.1.48       14.14.14.14     312         0x800000DA 0x0025C4
10.1.1.48       15.15.15.15     436         0x800000DA 0x0007DE
14.14.14.14     14.14.14.14     312         0x80000005 0x007D4C
14.14.14.14     15.15.15.15     436         0x80000005 0x00C3F7
15.15.15.15     14.14.14.14     312         0x80000005 0x00B308
15.15.15.15     15.15.15.15     436         0x80000005 0x003190
19.19.19.19     14.14.14.14     1823        0x80000002 0x0001AD
19.19.19.19     15.15.15.15     1939        0x80000002 0x004759

Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
14.14.14.14     14.14.14.14     802         0x80000008 0x007622 1
19.19.19.19     19.19.19.19     1919        0x80000008 0x00CB47 2

Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.2        19.19.19.19     698         0x80000007 0x00FC5D

Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     1063        0x80000005 0x0004FD
10.1.1.4        14.14.14.14     1063        0x80000005 0x008A61
10.1.1.8        14.14.14.14     1063        0x80000005 0x006285
10.1.1.16       14.14.14.14     1063        0x80000005 0x00765F
10.1.1.20       14.14.14.14     1063        0x80000005 0x004E83
10.1.1.24       14.14.14.14     1823        0x80000002 0x002CA4
10.1.1.48       14.14.14.14     1063        0x80000005 0x00D0EE
10.1.1.52       14.14.14.14     1063        0x80000005 0x000DA4
12.12.12.12     14.14.14.14     1063        0x80000005 0x003E89
13.13.13.13     14.14.14.14     1063        0x80000005 0x0010B3
14.14.14.14     14.14.14.14     1063        0x80000005 0x007D4C
15.15.15.15     14.14.14.14     1063        0x80000005 0x00B308
  ```
  </details>

<details>
<summary>R14 ip route</summary>

```
R14# show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 14 subnets, 2 masks
C        10.1.1.0/30 is directly connected, Ethernet0/3
L        10.1.1.1/32 is directly connected, Ethernet0/3
C        10.1.1.4/30 is directly connected, Ethernet0/0
L        10.1.1.5/32 is directly connected, Ethernet0/0
C        10.1.1.8/30 is directly connected, Ethernet0/1
L        10.1.1.9/32 is directly connected, Ethernet0/1
C        10.1.1.12/30 is directly connected, Ethernet0/2
L        10.1.1.13/32 is directly connected, Ethernet0/2
O        10.1.1.16/30 [110/20] via 10.1.1.6, 01:26:31, Ethernet0/0
O        10.1.1.20/30 [110/20] via 10.1.1.10, 01:26:31, Ethernet0/1
O IA     10.1.1.24/30 [110/20] via 10.1.1.49, 01:08:20, Ethernet1/0
C        10.1.1.48/30 is directly connected, Ethernet1/0
L        10.1.1.50/32 is directly connected, Ethernet1/0
O        10.1.1.52/30 [110/20] via 10.1.1.10, 01:26:31, Ethernet0/1
                      [110/20] via 10.1.1.6, 01:26:31, Ethernet0/0
      12.0.0.0/32 is subnetted, 1 subnets
O        12.12.12.12 [110/11] via 10.1.1.6, 01:26:31, Ethernet0/0
      13.0.0.0/32 is subnetted, 1 subnets
O        13.13.13.13 [110/11] via 10.1.1.10, 01:26:31, Ethernet0/1
      14.0.0.0/32 is subnetted, 1 subnets
C        14.14.14.14 is directly connected, Loopback1
      15.0.0.0/32 is subnetted, 1 subnets
O        15.15.15.15 [110/11] via 10.1.1.49, 01:26:31, Ethernet1/0
      19.0.0.0/32 is subnetted, 1 subnets
O        19.19.19.19 [110/11] via 10.1.1.2, 01:06:43, Ethernet0/3
R14#  
```
</details>

####  

<details>
<summary>R15 ip ospf neighbour</summary>

```
R15#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
14.14.14.14       1   FULL/DR         00:00:32    10.1.1.50       Ethernet1/0
13.13.13.13       1   FULL/BDR        00:00:35    10.1.1.22       Ethernet0/0
12.12.12.12       1   FULL/BDR        00:00:36    10.1.1.18       Ethernet0/1
20.20.20.20       1   FULL/DR         00:00:31    10.1.1.26       Ethernet0/3
R15#
```
</details>

<details>

<summary>R15 ip ospf database</summary>

```
R15#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
14.14.14.14       1   FULL/DR         00:00:32    10.1.1.50       Ethernet1/0
13.13.13.13       1   FULL/BDR        00:00:35    10.1.1.22       Ethernet0/0
12.12.12.12       1   FULL/BDR        00:00:36    10.1.1.18       Ethernet0/1
20.20.20.20       1   FULL/DR         00:00:31    10.1.1.26       Ethernet0/3
R15#show ip ospf database

            OSPF Router with ID (15.15.15.15) (Process ID 1)

Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
14.14.14.14     14.14.14.14     968         0x800000E9 0x00C841 2
15.15.15.15     15.15.15.15     849         0x800000E7 0x008878 2

Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.50       14.14.14.14     1974        0x800000D8 0x007703

Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.0        14.14.14.14     1218        0x80000003 0x009857
10.1.1.4        14.14.14.14     968         0x800000D7 0x00C650
10.1.1.4        15.15.15.15     849         0x80000004 0x00B428
10.1.1.8        14.14.14.14     727         0x80000004 0x0046A0
10.1.1.8        15.15.15.15     1845        0x800000D7 0x00E420
10.1.1.16       14.14.14.14     727         0x80000004 0x005A7A
10.1.1.16       15.15.15.15     1845        0x800000D7 0x0030D6
10.1.1.20       14.14.14.14     727         0x80000004 0x00329E
10.1.1.20       15.15.15.15     1845        0x800000D7 0x0008FA
10.1.1.24       15.15.15.15     325         0x80000003 0x00894A
10.1.1.52       14.14.14.14     727         0x80000004 0x00F0BF
10.1.1.52       15.15.15.15     1845        0x800000D7 0x002BAD
12.12.12.12     14.14.14.14     727         0x80000004 0x0022A4
12.12.12.12     15.15.15.15     849         0x80000004 0x0004BE
13.13.13.13     14.14.14.14     727         0x80000004 0x00F3CE
13.13.13.13     15.15.15.15     1089        0x80000004 0x00D5E8
19.19.19.19     14.14.14.14     235         0x80000003 0x00E0CA

Router Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum Link count
12.12.12.12     12.12.12.12     730         0x800000EA 0x000587 4
13.13.13.13     13.13.13.13     659         0x800000E5 0x00D79C 4
14.14.14.14     14.14.14.14     728         0x800000DE 0x005225 2
15.15.15.15     15.15.15.15     849         0x800000DD 0x000B35 2

Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.5        14.14.14.14     728         0x800000DB 0x00BCF5
10.1.1.9        14.14.14.14     728         0x800000D9 0x00CAE1
10.1.1.17       15.15.15.15     849         0x800000DB 0x004856
10.1.1.21       15.15.15.15     849         0x800000D9 0x005642
10.1.1.54       13.13.13.13     659         0x800000DA 0x00CEBB

Summary Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     728         0x80000004 0x0006FC
0.0.0.0         15.15.15.15     849         0x80000004 0x00E717
10.1.1.0        14.14.14.14     1219        0x80000003 0x00B63B
10.1.1.0        15.15.15.15     1089        0x80000003 0x00FCE6
10.1.1.24       14.14.14.14     236         0x80000003 0x002AA5
10.1.1.24       15.15.15.15     325         0x80000003 0x00A72E
10.1.1.48       14.14.14.14     728         0x800000DA 0x0025C4
10.1.1.48       15.15.15.15     849         0x800000DA 0x0007DE
14.14.14.14     14.14.14.14     728         0x80000005 0x007D4C
14.14.14.14     15.15.15.15     849         0x80000005 0x00C3F7
15.15.15.15     14.14.14.14     728         0x80000005 0x00B308
15.15.15.15     15.15.15.15     849         0x80000005 0x003190
19.19.19.19     14.14.14.14     235         0x80000003 0x00FEAE
19.19.19.19     15.15.15.15     325         0x80000003 0x00455A

Router Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum Link count
15.15.15.15     15.15.15.15     325         0x80000004 0x00560C 1
20.20.20.20     20.20.20.20     373         0x80000004 0x00E356 1

Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.26       20.20.20.20     373         0x80000003 0x002C0C

Summary Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.0        15.15.15.15     325         0x80000003 0x00DE03
10.1.1.4        15.15.15.15     325         0x80000003 0x00B627
10.1.1.8        15.15.15.15     325         0x80000003 0x008E4B
10.1.1.16       15.15.15.15     325         0x80000003 0x00D902
10.1.1.20       15.15.15.15     325         0x80000003 0x00B126
10.1.1.48       15.15.15.15     325         0x80000003 0x009823
10.1.1.52       15.15.15.15     325         0x80000003 0x00D4D8
12.12.12.12     15.15.15.15     325         0x80000003 0x0006BD
13.13.13.13     15.15.15.15     325         0x80000003 0x00D7E7
14.14.14.14     15.15.15.15     325         0x80000003 0x00A912
15.15.15.15     15.15.15.15     325         0x80000003 0x0017AA
R15#  
```
</details>

<details>
<summary>R15 ip ospf route</summary>

```
R15#show ip route        
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 14 subnets, 2 masks
O IA     10.1.1.0/30 [110/20] via 10.1.1.50, 01:26:38, Ethernet1/0
O        10.1.1.4/30 [110/20] via 10.1.1.18, 01:52:40, Ethernet0/1
O        10.1.1.8/30 [110/20] via 10.1.1.22, 01:52:40, Ethernet0/0
C        10.1.1.16/30 is directly connected, Ethernet0/1
L        10.1.1.17/32 is directly connected, Ethernet0/1
C        10.1.1.20/30 is directly connected, Ethernet0/0
L        10.1.1.21/32 is directly connected, Ethernet0/0
C        10.1.1.24/30 is directly connected, Ethernet0/3
L        10.1.1.25/32 is directly connected, Ethernet0/3
C        10.1.1.28/30 is directly connected, Ethernet0/2
L        10.1.1.29/32 is directly connected, Ethernet0/2
C        10.1.1.48/30 is directly connected, Ethernet1/0
L        10.1.1.49/32 is directly connected, Ethernet1/0
O        10.1.1.52/30 [110/20] via 10.1.1.22, 01:52:50, Ethernet0/0
                      [110/20] via 10.1.1.18, 01:52:40, Ethernet0/1
      12.0.0.0/32 is subnetted, 1 subnets
O        12.12.12.12 [110/11] via 10.1.1.18, 01:52:40, Ethernet0/1
      13.0.0.0/32 is subnetted, 1 subnets
O        13.13.13.13 [110/11] via 10.1.1.22, 01:52:50, Ethernet0/0
      14.0.0.0/32 is subnetted, 1 subnets
O        14.14.14.14 [110/11] via 10.1.1.50, 01:52:50, Ethernet1/0
      15.0.0.0/32 is subnetted, 1 subnets
C        15.15.15.15 is directly connected, Loopback1
      19.0.0.0/32 is subnetted, 1 subnets
O IA     19.19.19.19 [110/21] via 10.1.1.50, 01:11:23, Ethernet1/0
```
</details>

######  R12-R13 в зоне 10 и получили всё + default

<details>
<summary>R12 ip ospf neighbour</summary>

```
R12#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
14.14.14.14       1   FULL/DR         00:00:33    10.1.1.5        Ethernet0/2
15.15.15.15       1   FULL/DR         00:00:34    10.1.1.17       Ethernet0/3
13.13.13.13       1   FULL/DR         00:00:32    10.1.1.54       Ethernet1/0
R12#
```
</details>

<details>
<summary>R12 ip ospf database</summary>

```
R12#show ip ospf database

            OSPF Router with ID (12.12.12.12) (Process ID 1)

Router Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum Link count
12.12.12.12     12.12.12.12     970         0x800000EA 0x000587 4
13.13.13.13     13.13.13.13     901         0x800000E5 0x00D79C 4
14.14.14.14     14.14.14.14     968         0x800000DE 0x005225 2
15.15.15.15     15.15.15.15     1091        0x800000DD 0x000B35 2

Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.5        14.14.14.14     968         0x800000DB 0x00BCF5
10.1.1.9        14.14.14.14     968         0x800000D9 0x00CAE1
10.1.1.17       15.15.15.15     1091        0x800000DB 0x004856
10.1.1.21       15.15.15.15     1092        0x800000D9 0x005642
10.1.1.54       13.13.13.13     902         0x800000DA 0x00CEBB

Summary Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     968         0x80000004 0x0006FC
0.0.0.0         15.15.15.15     1091        0x80000004 0x00E717
10.1.1.0        14.14.14.14     1459        0x80000003 0x00B63B
10.1.1.0        15.15.15.15     1332        0x80000003 0x00FCE6
10.1.1.24       14.14.14.14     476         0x80000003 0x002AA5
10.1.1.24       15.15.15.15     569         0x80000003 0x00A72E
10.1.1.48       14.14.14.14     968         0x800000DA 0x0025C4
10.1.1.48       15.15.15.15     1092        0x800000DA 0x0007DE
14.14.14.14     14.14.14.14     968         0x80000005 0x007D4C
14.14.14.14     15.15.15.15     1091        0x80000005 0x00C3F7
15.15.15.15     14.14.14.14     968         0x80000005 0x00B308
15.15.15.15     15.15.15.15     1091        0x80000005 0x003190
19.19.19.19     14.14.14.14     476         0x80000003 0x00FEAE
19.19.19.19     15.15.15.15     568         0x80000003 0x00455A
R12#   

```
</details>
<details>
<summary>R12 ip route </summary>

```
R12#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.1.1.17 to network 0.0.0.0

O*IA  0.0.0.0/0 [110/11] via 10.1.1.17, 01:56:20, Ethernet0/3
         [110/11] via 10.1.1.5, 01:57:19, Ethernet0/2
10.0.0.0/8 is variably subnetted, 15 subnets, 2 masks
O IA     10.1.1.0/30 [110/20] via 10.1.1.5, 01:30:24, Ethernet0/2
C        10.1.1.4/30 is directly connected, Ethernet0/2
L        10.1.1.6/32 is directly connected, Ethernet0/2
O        10.1.1.8/30 [110/20] via 10.1.1.54, 01:56:42, Ethernet1/0
              [110/20] via 10.1.1.5, 01:56:42, Ethernet0/2
C        10.1.1.16/30 is directly connected, Ethernet0/3
L        10.1.1.18/32 is directly connected, Ethernet0/3
O        10.1.1.20/30 [110/20] via 10.1.1.54, 01:56:20, Ethernet1/0
               [110/20] via 10.1.1.17, 01:56:20, Ethernet0/3
O IA     10.1.1.24/30 [110/20] via 10.1.1.17, 01:16:45, Ethernet0/3
C        10.1.1.32/30 is directly connected, Ethernet0/0.11
L        10.1.1.33/32 is directly connected, Ethernet0/0.11
C        10.1.1.36/30 is directly connected, Ethernet0/1.11
L        10.1.1.37/32 is directly connected, Ethernet0/1.11
O IA     10.1.1.48/30 [110/20] via 10.1.1.17, 01:56:20, Ethernet0/3
               [110/20] via 10.1.1.5, 01:57:19, Ethernet0/2
C        10.1.1.52/30 is directly connected, Ethernet1/0
L        10.1.1.53/32 is directly connected, Ethernet1/0
12.0.0.0/32 is subnetted, 1 subnets
C        12.12.12.12 is directly connected, Loopback1
13.0.0.0/32 is subnetted, 1 subnets
O        13.13.13.13 [110/11] via 10.1.1.54, 01:56:42, Ethernet1/0
14.0.0.0/32 is subnetted, 1 subnets
O IA     14.14.14.14 [110/11] via 10.1.1.5, 01:57:19, Ethernet0/2
15.0.0.0/32 is subnetted, 1 subnets
O IA     15.15.15.15 [110/11] via 10.1.1.17, 01:56:20, Ethernet0/3
19.0.0.0/32 is subnetted, 1 subnets
O IA     19.19.19.19 [110/21] via 10.1.1.5, 01:15:08, Ethernet0/2
R12#
```
</details>

###  

<details>
<summary>R13 ip ospf neighbour</summary>

```
R13#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
15.15.15.15       1   FULL/DR         00:00:34    10.1.1.21       Ethernet0/2
14.14.14.14       1   FULL/DR         00:00:34    10.1.1.9        Ethernet0/3
12.12.12.12       1   FULL/BDR        00:00:34    10.1.1.53       Ethernet1/0
R13#
```
</details>
<details>
<summary>R13 ip ospf database</summary>

```
R13#show ip ospf database

            OSPF Router with ID (13.13.13.13) (Process ID 1)

Router Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum Link count
12.12.12.12     12.12.12.12     1286        0x800000EA 0x000587 4
13.13.13.13     13.13.13.13     1214        0x800000E5 0x00D79C 4
14.14.14.14     14.14.14.14     1284        0x800000DE 0x005225 2
15.15.15.15     15.15.15.15     1405        0x800000DD 0x000B35 2

Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.5        14.14.14.14     1283        0x800000DB 0x00BCF5
10.1.1.9        14.14.14.14     1283        0x800000D9 0x00CAE1
10.1.1.17       15.15.15.15     1405        0x800000DB 0x004856
10.1.1.21       15.15.15.15     1405        0x800000D9 0x005642
10.1.1.54       13.13.13.13     1214        0x800000DA 0x00CEBB

Summary Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     1283        0x80000004 0x0006FC
0.0.0.0         15.15.15.15     1405        0x80000004 0x00E717
10.1.1.0        14.14.14.14     1774        0x80000003 0x00B63B
10.1.1.0        15.15.15.15     1646        0x80000003 0x00FCE6
10.1.1.24       14.14.14.14     790         0x80000003 0x002AA5
10.1.1.24       15.15.15.15     882         0x80000003 0x00A72E
10.1.1.48       14.14.14.14     1284        0x800000DA 0x0025C4
10.1.1.48       15.15.15.15     1405        0x800000DA 0x0007DE
14.14.14.14     14.14.14.14     1283        0x80000005 0x007D4C
14.14.14.14     15.15.15.15     1405        0x80000005 0x00C3F7
15.15.15.15     14.14.14.14     1283        0x80000005 0x00B308
15.15.15.15     15.15.15.15     1405        0x80000005 0x003190
19.19.19.19     14.14.14.14     790         0x80000003 0x00FEAE
19.19.19.19     15.15.15.15     882         0x80000003 0x00455A
R13#    
```
</details>
<details>
<summary>R13 ip route</summary>

```
R13#show ip route        
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.1.1.21 to network 0.0.0.0

O*IA  0.0.0.0/0 [110/11] via 10.1.1.21, 02:00:37, Ethernet0/2
                [110/11] via 10.1.1.9, 02:01:04, Ethernet0/3
      10.0.0.0/8 is variably subnetted, 15 subnets, 2 masks
O IA     10.1.1.0/30 [110/20] via 10.1.1.9, 01:34:41, Ethernet0/3
O        10.1.1.4/30 [110/20] via 10.1.1.53, 02:01:04, Ethernet1/0
                     [110/20] via 10.1.1.9, 02:01:04, Ethernet0/3
C        10.1.1.8/30 is directly connected, Ethernet0/3
L        10.1.1.10/32 is directly connected, Ethernet0/3
O        10.1.1.16/30 [110/20] via 10.1.1.53, 02:00:37, Ethernet1/0
                      [110/20] via 10.1.1.21, 02:00:37, Ethernet0/2
C        10.1.1.20/30 is directly connected, Ethernet0/2
L        10.1.1.22/32 is directly connected, Ethernet0/2
O IA     10.1.1.24/30 [110/20] via 10.1.1.21, 01:21:02, Ethernet0/2
C        10.1.1.40/30 is directly connected, Ethernet0/1.12
L        10.1.1.41/32 is directly connected, Ethernet0/1.12
C        10.1.1.44/30 is directly connected, Ethernet0/0.12
L        10.1.1.45/32 is directly connected, Ethernet0/0.12
O IA     10.1.1.48/30 [110/20] via 10.1.1.21, 02:00:37, Ethernet0/2
                      [110/20] via 10.1.1.9, 02:01:04, Ethernet0/3
C        10.1.1.52/30 is directly connected, Ethernet1/0
L        10.1.1.54/32 is directly connected, Ethernet1/0
      12.0.0.0/32 is subnetted, 1 subnets
O        12.12.12.12 [110/11] via 10.1.1.53, 02:01:04, Ethernet1/0
      13.0.0.0/32 is subnetted, 1 subnets
C        13.13.13.13 is directly connected, Loopback1
      14.0.0.0/32 is subnetted, 1 subnets
O IA     14.14.14.14 [110/11] via 10.1.1.9, 02:01:04, Ethernet0/3
      15.0.0.0/32 is subnetted, 1 subnets
O IA     15.15.15.15 [110/11] via 10.1.1.21, 02:00:37, Ethernet0/2
      19.0.0.0/32 is subnetted, 1 subnets
O IA     19.19.19.19 [110/21] via 10.1.1.9, 01:19:26, Ethernet0/3
```
</details>

#####  R18 в зоне 101 и получил только default

<details>
<summary>R19 ip ospf neighbour</summary>

```
R19#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
14.14.14.14       1   FULL/BDR        00:00:32    10.1.1.1        Ethernet0/0
R19#
```
</details>
<details>
<summary>R19 ip ospf database</summary>

```
R19#show ip ospf database

            OSPF Router with ID (19.19.19.19) (Process ID 1)

Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
14.14.14.14     14.14.14.14     494         0x80000009 0x007423 1
19.19.19.19     19.19.19.19     1612        0x80000009 0x00C948 2

Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.2        19.19.19.19     331         0x80000008 0x00FA5E

Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         14.14.14.14     26          0x80000008 0x00FD01
R19#
```
</details>
<details>
<summary>R19 ip route</summary>

```
R19#show ip route  
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.1.1.1 to network 0.0.0.0

O*IA  0.0.0.0/0 [110/11] via 10.1.1.1, 00:00:01, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.1.1.0/30 is directly connected, Ethernet0/0
L        10.1.1.2/32 is directly connected, Ethernet0/0
      19.0.0.0/32 is subnetted, 1 subnets
C        19.19.19.19 is directly connected, Loopback1
```
</details>

##### R20 в зоне 102 и получил все маршруты кроме маршрута(19.19.19.19) из зоны 101

<details>
<summary>R20 ip ospf neighbour</summary>

```
R20#show ip ospf ne

Neighbor ID     Pri   State           Dead Time   Address         Interface
15.15.15.15       1   FULL/BDR        00:00:33    10.1.1.25       Ethernet0/0
R20#
```
</details>
<details>
<summary>R20 ip ospf database</summary>

```
R20#show ip ospf database

            OSPF Router with ID (20.20.20.20) (Process ID 1)

Router Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum Link count
15.15.15.15     15.15.15.15     1009        0x80000004 0x00560C 1
20.20.20.20     20.20.20.20     1056        0x80000004 0x00E356 1

Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.26       20.20.20.20     1056        0x80000003 0x002C0C

Summary Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.0        15.15.15.15     1009        0x80000003 0x00DE03
10.1.1.4        15.15.15.15     1009        0x80000003 0x00B627
10.1.1.8        15.15.15.15     1009        0x80000003 0x008E4B
10.1.1.16       15.15.15.15     1009        0x80000003 0x00D902
10.1.1.20       15.15.15.15     1009        0x80000003 0x00B126
10.1.1.48       15.15.15.15     1009        0x80000003 0x009823
10.1.1.52       15.15.15.15     1009        0x80000003 0x00D4D8
12.12.12.12     15.15.15.15     1009        0x80000003 0x0006BD
13.13.13.13     15.15.15.15     1009        0x80000003 0x00D7E7
14.14.14.14     15.15.15.15     1009        0x80000003 0x00A912
15.15.15.15     15.15.15.15     1009        0x80000003 0x0017AA
```
</details>
<details>
<summary>R20 ip route</summary>

```
R20#  show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
O IA     10.1.1.0/30 [110/30] via 10.1.1.25, 01:23:01, Ethernet0/0
O IA     10.1.1.4/30 [110/30] via 10.1.1.25, 01:23:01, Ethernet0/0
O IA     10.1.1.8/30 [110/30] via 10.1.1.25, 01:23:01, Ethernet0/0
O IA     10.1.1.16/30 [110/20] via 10.1.1.25, 01:23:01, Ethernet0/0
O IA     10.1.1.20/30 [110/20] via 10.1.1.25, 01:23:01, Ethernet0/0
C        10.1.1.24/30 is directly connected, Ethernet0/0
L        10.1.1.26/32 is directly connected, Ethernet0/0
O IA     10.1.1.48/30 [110/20] via 10.1.1.25, 01:23:01, Ethernet0/0
O IA     10.1.1.52/30 [110/30] via 10.1.1.25, 01:23:01, Ethernet0/0
      12.0.0.0/32 is subnetted, 1 subnets
O IA     12.12.12.12 [110/21] via 10.1.1.25, 01:23:01, Ethernet0/0
      13.0.0.0/32 is subnetted, 1 subnets
O IA     13.13.13.13 [110/21] via 10.1.1.25, 01:23:01, Ethernet0/0
      14.0.0.0/32 is subnetted, 1 subnets
O IA     14.14.14.14 [110/21] via 10.1.1.25, 01:23:01, Ethernet0/0
      15.0.0.0/32 is subnetted, 1 subnets
O IA     15.15.15.15 [110/11] via 10.1.1.25, 01:23:01, Ethernet0/0
R20#
```
</details>
