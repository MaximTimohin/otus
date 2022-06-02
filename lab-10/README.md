#### IBGP
###  




##### Цель:  
* Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.
* Настроите iBGP в провайдере Триада.
* Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
* Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.


#### Схема сети:

  ![alt-текст](/lab-9/img/bgp.png)

#### Анонсируемые сети по AS

   |AS-number| Network| comment|
   |:----:|:------|:--------|
   | 1001 | 10.1.0.0/16|Стыковка и клиентские сети|
   |      |14.14.14.0/24|Loopback R14|
   |      |15.15.15.0/24|Loopback R15|
   | 101 | 10.2.0.0/16|Стыковка и клиентские сети|
   |      |22.22.22.0/24|Loopback R22|
   | 301 | 10.3.0.0/16|Стыковка и клиентские сети|
   |      |21.21.21.0/24|Loopback R21|
   | 520 | 10.4.0.0/16|Стыковка и клиентские сети|
   |      |23.23.23.0/24|Loopback R23|
   |      |24.24.24.0/24|Loopback R24|
   | 2042 | 10.5.0.0/16|Стыковка и клиентские сети|
   |      |18.18.18.0/24|Loopback R18|

### Настройка:

###### Настраиваем BGP AS1001 R14-R15
<details>
<summary>config-R14</summary>

```
router bgp 1001
 neighbor 15.15.15.15 remote-as 1001
 neighbor 15.15.15.15 update-source Loopback1
 neighbor 15.15.15.15 next-hop-self
!
```
</details>
<details>
<summary>config-R15</summary>

```
router bgp 1001
 neighbor 14.14.14.14 remote-as 1001
 neighbor 14.14.14.14 update-source Loopback1
 neighbor 14.14.14.14 next-hop-self
```
</details>

###### Настраиваем BGP AS520 R23-R26

<details>
<summary>config-R23</summary>

```
router bgp 520
 neighbor 24.24.24.24 remote-as 520
 neighbor 24.24.24.24 update-source Loopback1
 neighbor 24.24.24.24 next-hop-self
 neighbor 25.25.25.25 remote-as 520
 neighbor 25.25.25.25 update-source Loopback1
 neighbor 25.25.25.25 next-hop-self
 neighbor 26.26.26.26 remote-as 520
 neighbor 26.26.26.26 update-source Loopback1
 neighbor 26.26.26.26 next-hop-self
```
</details>
<details>
<summary>config-R24</summary>

```
router bgp 520
 neighbor 23.23.23.23 remote-as 520
 neighbor 23.23.23.23 update-source Loopback1
 neighbor 25.25.25.25 remote-as 520
 neighbor 25.25.25.25 update-source Loopback1
 neighbor 26.26.26.26 remote-as 520
 neighbor 26.26.26.26 update-source Loopback1
```
</details>
<details>
<summary>config-R25</summary>

```
router bgp 520
 neighbor 23.23.23.23 remote-as 520
 neighbor 23.23.23.23 update-source Loopback1
 neighbor 24.24.24.24 remote-as 520
 neighbor 24.24.24.24 update-source Loopback1
 neighbor 26.26.26.26 remote-as 520
 neighbor 26.26.26.26 update-source Loopback1
```
</details>
<details>
<summary>config-R26</summary>

```
router bgp 520
 neighbor 23.23.23.23 remote-as 520
 neighbor 23.23.23.23 update-source Loopback1
 neighbor 24.24.24.24 remote-as 520
 neighbor 24.24.24.24 update-source Loopback1
 neighbor 25.25.25.25 remote-as 520
 neighbor 25.25.25.25 update-source Loopback1
```
</details>

<details>
<summary>config-R18</summary>

```
На настроено распределение по двум линкам одновременно

router bgp 2042
 neighbor 10.5.0.10 remote-as 520
 neighbor 10.5.0.13 remote-as 520
 maximum-paths 2
!

```
</details>


##### Проверка результатов настройки

<details>
<summary>show ip bgp ... R14</summary>

```
R14#show ip bgp summary
BGP router identifier 14.14.14.14, local AS number 1001
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.14       4          101   13558   13565      141    0    0 1w1d           11
15.15.15.15     4         1001   12539   12536      141    0    0 1w0d           11
R14#


R14#show ip bgp  neighbors 15.15.15.15 routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 * i 10.1.0.0/16      15.15.15.15              0    100      0 i
 *>i 10.3.0.0/16      15.15.15.15              0    100      0 301 i
 * i 10.4.0.0/16      15.15.15.15              0    100      0 301 520 i
 *>i 10.5.0.0/16      15.15.15.15              0    100      0 301 520 2042 i
 *>i 15.15.15.0/24    15.15.15.15              0    100      0 i
 *>i 18.18.18.0/24    15.15.15.15              0    100      0 301 520 2042 i
 *>i 21.21.21.0/24    15.15.15.15              0    100      0 301 i
 * i 24.24.24.0/24    15.15.15.15              0    100      0 301 520 i
 *>i 27.27.27.0/24    15.15.15.15              0    100      0 301 520 i
 * i 28.28.28.0/24    15.15.15.15              0    100      0 301 520 i
 * i 29.29.29.0/24    15.15.15.15              0    100      0 301 520 i

Total number of prefixes 11
```
</details>

<details>
<summary>show ip bgp ... R15</summary>

```
R15#show ip bgp summary
BGP router identifier 15.15.15.15, local AS number 1001
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.30       4          301   13549   13546      121    0    0 1w1d           11
14.14.14.14     4         1001   12539   12542      121    0    0 1w0d            8
R15#


R15#show ip bgp neighbors 14.14.14.14 routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 * i 10.1.0.0/16      14.14.14.14              0    100      0 i
 *>i 10.2.0.0/16      14.14.14.14              0    100      0 101 i
 * i 10.4.0.0/16      14.14.14.14              0    100      0 101 520 i
 r>i 14.14.14.0/24    14.14.14.14              0    100      0 i
 *>i 22.22.22.0/24    14.14.14.14              0    100      0 101 i
 * i 24.24.24.0/24    14.14.14.14              0    100      0 101 520 i
 * i 28.28.28.0/24    14.14.14.14              0    100      0 101 520 i
 * i 29.29.29.0/24    14.14.14.14              0    100      0 101 520 i

Total number of prefixes 8
R15#
```
</details>


<details>
<summary>show ip bgp ... R23</summary>

```
R23#show ip bgp summ
BGP router identifier 23.23.23.23, local AS number 520
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.2.0.1        4          101   12468   12475       80    0    0 1w0d            7
24.24.24.24     4          520   12439   12439       80    0    0 1w0d            9
25.25.25.25     4          520   12475   12488       80    0    0 1w0d            0
26.26.26.26     4          520   12486   12481       80    0    0 1w0d            4
R23#
```
</details>

<details>
<summary>show ip bgp ... R24</summary>

```
R24#show ip bgp summary
BGP router identifier 24.24.24.24, local AS number 520

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.3.0.2        4          301   12451   12440       34    0    0 1w0d            7
10.5.0.9        4         2042    7578    7586       34    0    0 4d18h           2
23.23.23.23     4          520   12440   12441       34    0    0 1w0d            6
25.25.25.25     4          520   12431   12440       34    0    0 1w0d            0
26.26.26.26     4          520   12442   12427       34    0    0 1w0d            4
R24#
```
</details>

<details>
<summary>show ip bgp ... R25</summary>

```
R25#sh ip bgp sum
BGP router identifier 25.25.25.25, local AS number 520

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
23.23.23.23     4          520   12491   12477       88    0    0 1w0d            6
24.24.24.24     4          520   12440   12431       88    0    0 1w0d           10
26.26.26.26     4          520   12476   12481       88    0    0 1w0d            4
R25#  
```
</details>

<details>
<summary>show ip bgp ... R26</summary>

```
R26#show ip bgp sum
BGP router identifier 26.26.26.26, local AS number 520

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.5.0.14       4         2042    7594    7595      104    0    0 4d18h           2
23.23.23.23     4          520   12484   12488      104    0    0 1w0d            6
24.24.24.24     4          520   12428   12443      104    0    0 1w0d           10
25.25.25.25     4          520   12482   12477      104    0    0 1w0d            0
R26#  


```
</details>


##### Проверка связанности сети

В качестве примера проверим доступность IP сети между Москва и Санкт-Петербург
<details>
<summary>ping + traceroute SW2</summary>

```
Пингуем от имени Клиентской сети(10.1.255.0/24) на AS1001 клиетнскую сеть(10.5.128.0/24) на AS2042

SW2#ping 10.5.128.1 source 10.1.255.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.5.128.1, timeout is 2 seconds:
Packet sent with a source address of 10.1.255.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/5 ms
SW2#

SW2#traceroute 10.5.128.1
Type escape sequence to abort.
Tracing the route to 10.5.128.1
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.100.254 1 msec 1 msec 1 msec
  2 10.1.1.45 2 msec 1 msec 1 msec
  3 10.1.1.21 2 msec 2 msec 1 msec
  4 10.1.1.30 2 msec 1 msec 1 msec
  5 10.3.0.1 2 msec 2 msec 2 msec
  6 10.5.0.9 2 msec 1 msec 2 msec
  7 10.5.0.5 3 msec 2 msec 2 msec
  8 10.5.0.30 3 msec *  3 msec
SW2#
```

</details>

<details>
<summary>ping + traceroute SW10</summary>

```
SW10#ping 10.1.255.1 source 10.5.128.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.255.1, timeout is 2 seconds:
Packet sent with a source address of 10.5.128.1
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/6 ms
SW10#

SW10#traceroute 10.1.255.1
Type escape sequence to abort.
Tracing the route to 10.1.255.1
VRF info: (vrf in name/id, vrf out name/id)
  1 10.5.0.29 0 msec 1 msec 0 msec
  2 10.5.0.6 0 msec 0 msec 0 msec
  3 10.5.0.10 1 msec 1 msec 1 msec
  4 10.3.0.2 2 msec 2 msec 1 msec
  5 10.1.1.29 2 msec 2 msec 2 msec
  6 10.1.1.22 3 msec 2 msec 2 msec
  7 10.1.1.46 3 msec 2 msec 2 msec
  8 10.1.100.2 4 msec *  4 msec
SW10#
```
</details>
