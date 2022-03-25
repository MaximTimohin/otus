#### PBR

#### Схема сети:

  ![alt-текст](/lab-4/img/main_map.png "Схема сети")
#### Цель:

Настроим и оборудование в Чокурдах и выполним следующие задачи:
1. Разделим тафик между двумя провайдерами R25,R26.
2. Настроим политику маршрутизации в офисе.
3. Настроим основной маршрут и отслеживание линков через IP SLA

<details>
  <summary>Настройка SW29</summary>

##### Настраиваем влан  и порты для VPC3,VPC31
```
interface Ethernet0/0
 description VPC30
 switchport access vlan 30
!
interface Ethernet0/1
 description VPC31
 switchport access vlan 31
 !
interface Vlan30
 ip address 10.4.255.1 255.255.255.0
 ipv6 address FE80::29 link-local
 ipv6 address 20FF:AAAA:BBBB:407::1/64
 ipv6 enable
!
interface Vlan31
 ip address 10.4.254.1 255.255.255.0
 ipv6 address FE80::29 link-local
 ipv6 address 20FF:AAAA:BBBB:408::1/64
 ipv6 enable
```
##### Настраиваем связь с R28

  ```
ipv6 unicast-routing
ip routing

interface Ethernet0/2
 description R28
 switchport trunk allowed vlan 29
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Vlan29
 ip address 10.4.0.30 255.255.255.252
 ipv6 address FE80::29 link-local
 ipv6 address 20FF:AAAA:BBBB:406::29/64
 ipv6 enable
!
 ip route 0.0.0.0 0.0.0.0 10.4.0.29
!
ipv6 route 20FF:AAAA:BBBB::/48 20FF:AAAA:BBBB:406::28
  ```
</details>

<details>
<summary>Настройка R28</summary>

##### Настройка интерфейсов
```
interface Ethernet0/0
 description R26
 ip address 10.4.0.26 255.255.255.252
 ipv6 address FE80::28 link-local
 ipv6 address 20FF:AAAA:BBBB:405::28/64
 ipv6 enable
!
interface Ethernet0/1
 description R25
 ip address 10.4.0.22 255.255.255.252
 ipv6 address FE80::28 link-local
 ipv6 address 20FF:AAAA:BBBB:510::28/64
 ipv6 enable
!
interface Ethernet0/2
 description SW29
 no ip address
!
interface Ethernet0/2.29
 encapsulation dot1Q 29
 ip address 10.4.0.29 255.255.255.252
 ip policy route-map VPC31
 ipv6 address FE80::28 link-local
 ipv6 address 20FF:AAAA:BBBB:406::28/64
 ipv6 enable
!
```
##### Маршруты

```
ip route 0.0.0.0 0.0.0.0 10.4.0.25 10 track 1
ip route 0.0.0.0 0.0.0.0 10.4.0.21 20 track 2
ip route 10.4.254.0 255.255.255.0 10.4.0.30
ip route 10.4.255.0 255.255.255.0 10.4.0.30
pv6 route 20FF:AAAA:BBBB:407::/64 20FF:AAAA:BBBB:406::29
ipv6 route 20FF:AAAA:BBBB:408::/64 20FF:AAAA:BBBB:406::29
```

##### Route-map

```
ip access-list extended VPC31
 permit ip host 10.4.254.31 host 10.4.0.13
!
route-map VPC31 permit 10
 match ip address VPC31
 set ip next-hop 10.4.0.21
!         
route-map VPC31 deny 20
```

##### Мониторинг SLA

```
track 1 ip sla 1 reachability
!
track 2 ip sla 2 reachability
!
track 3 ip sla 3 reachability
!
track 4 ip sla 4 reachability
!
ip sla 1
 icmp-echo 10.4.0.25 source-ip 10.4.0.26
 threshold 2
 frequency 5
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 10.4.0.21 source-ip 10.4.0.22
 threshold 2
 frequency 5
ip sla schedule 2 life forever start-time now
ip sla 3
 icmp-echo 20FF:AAAA:BBBB:405::26 source-ip 20FF:AAAA:BBBB:405::28
 threshold 2
 frequency 5
ip sla schedule 3 life forever start-time now
ip sla 4
 icmp-echo 20FF:AAAA:BBBB:510::25 source-ip 20FF:AAAA:BBBB:510::28
 threshold 2
 frequency 5
ip sla schedule 4 life forever start-time now

```
</details>

#  

###### Настроим VPC

<details>
<summary>Настройка VPC30</summary>

```
VPCS> show ip
NAME        : VPCS[1]
IP/MASK     : 10.4.255.30/24
GATEWAY     : 10.4.255.1

VPCS> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:681e/64
GLOBAL SCOPE      : 20ff:aaaa:bbbb:407::30/64
DNS               :
```
</details>

<details>
<summary>Настройка VPC31</summary>

```

VPCS> show ip    

NAME        : VPCS[1]
IP/MASK     : 10.4.254.31/24
GATEWAY     : 10.4.254.1

VPCS> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:681f/64
GLOBAL SCOPE      : 20ff:aaaa:bbbb:408::31/64
D
```
</details>

##### Результат проведенных настроек

<details>
<summary>IP SLA  на R28</summary>

```
R28#show ip sla stat
IPSLAs Latest Operation Statistics

IPSLA operation id: 1
Latest RTT: 1 milliseconds
Latest operation start time: 11:39:16 EET Fri Mar 25 2022
Latest operation return code: OK
Number of successes: 92
Number of failures: 0
Operation time to live: Forever



IPSLA operation id: 2
Latest RTT: 1 milliseconds
Latest operation start time: 11:39:13 EET Fri Mar 25 2022
Latest operation return code: OK
Number of successes: 73
Number of failures: 0
Operation time to live: Forever



IPSLA operation id: 3
        Latest RTT: 1 milliseconds
Latest operation start time: 11:39:17 EET Fri Mar 25 2022
Latest operation return code: OK
Number of successes: 25
Number of failures: 0
Operation time to live: Forever



IPSLA operation id: 4
Latest RTT: 1 milliseconds
Latest operation start time: 11:39:16 EET Fri Mar 25 2022
Latest operation return code: OK
Number of successes: 678
Number of failures: 0
Operation time to live: Forever
```

```
R28# show track
Track 1
  IP SLA 1 reachability
  Reachability is Up
    1 change, last change 01:07:14
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Static IP Routing 0
Track 2
  IP SLA 2 reachability
  Reachability is Up
    1 change, last change 01:05:58
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Static IP Routing 0
Track 3
  IP SLA 3 reachability
  Reachability is Up
    2 changes, last change 00:54:58
  Latest operation return code: OK
  Latest RTT (millisecs) 1
Track 4
  IP SLA 4 reachability
  Reachability is Up
    2 changes, last change 00:56:33
  Latest operation return code: OK
  Latest RTT (millisecs) 1
```
</details>


<details>
<summary>ROUTE-MAP на R28</summary>


```
R28#show route-map
route-map VPC31, permit, sequence 10
  Match clauses:
    ip address (access-lists): VPC31
  Set clauses:
    ip next-hop 10.4.0.21
  Policy routing matches: 13 packets, 1398 bytes
route-map VPC31, deny, sequence 20
  Match clauses:
  Set clauses:
  Policy routing matches: 0 packets, 0 bytes
  ```
</details>

  <details>  
  <summary>Трассировка до IP адреса на R25 с VPC30</summary>

  ```
  VPCS> trace 10.4.0.13
trace to 10.4.0.13, 8 hops max, press Ctrl+C to stop
 1   10.4.255.1   0.503 ms  0.303 ms  0.637 ms
 2   10.4.0.29   1.085 ms  0.906 ms  0.768 ms
 3   10.4.0.25   1.376 ms  1.044 ms  1.128 ms
 4   *10.4.0.13   1.225 ms (ICMP type:3, code:3, Destination port unreachable)  *

  ```

</details>
<details>
<summary>Трассировка до IP адреса на R25 с VPC31</summary>

  ```
  VPCS> trace 10.4.0.13
trace to 10.4.0.13, 8 hops max, press Ctrl+C to stop
 1   10.4.254.1   0.424 ms  0.296 ms  0.288 ms
 2   10.4.0.29   0.739 ms  0.593 ms  0.519 ms
 3   *10.4.0.21   1.710 ms (ICMP type:3, code:3, Destination port unreachable)  *
  ```
</details>

<details>
<summary>PING по IP адресу на R25 с VPC31</summary>

```
VPCS> ping 10.4.0.13

84 bytes from 10.4.0.13 icmp_seq=1 ttl=253 time=1.958 ms
84 bytes from 10.4.0.13 icmp_seq=2 ttl=253 time=1.790 ms
84 bytes from 10.4.0.13 icmp_seq=3 ttl=253 time=1.886 ms
84 bytes from 10.4.0.13 icmp_seq=4 ttl=253 time=1.607 ms
^C
```
</details>

<details>
<summary>PING по IP адресу на R25 с VPC31</summary>

```
VPCS> ping 10.4.0.13

84 bytes from 10.4.0.13 icmp_seq=1 ttl=253 time=1.511 ms
84 bytes from 10.4.0.13 icmp_seq=2 ttl=253 time=1.191 ms
84 bytes from 10.4.0.13 icmp_seq=3 ttl=253 time=1.244 ms
84 bytes from 10.4.0.13 icmp_seq=4 ttl=253 time=1.598 ms
^C
```
</details>
