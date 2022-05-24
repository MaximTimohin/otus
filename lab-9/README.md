#### BGP. Основы  
###  




##### Цель:  
* Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
* Настроить eBGP между провайдерами Киторн и Ламас.
* Настроить eBGP между Ламас и Триада.
* Настроить eBGP между офисом С.-Петербург и провайдером Триада.


#### Схема сети:

  ![alt-текст](/lab-9/img/bgp.png)


### Настройка:

###### Настраиваем BGP AS1001 R14-R15
<details>
<summary>config-R14</summary>

```
router bgp 1001
 bgp router-id 14.14.14.14
 bgp log-neighbor-changes
 network 10.1.0.0 mask 255.255.0.0
 network 14.14.14.0 mask 255.255.255.0
 neighbor 10.1.1.14 remote-as 101
!
```
</details>

<details>
<summary>config-R15</summary>

```
router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 network 10.1.0.0 mask 255.255.0.0
 network 15.15.15.0 mask 255.255.255.0
 neighbor 10.1.1.30 remote-as 301
!
```
</details>

###### Настраиваем BGP AS101 R22
<details>
<summary>config-R22</summary>

```
router bgp 101
 bgp router-id 22.22.22.22
 bgp log-neighbor-changes
 network 10.2.0.0 mask 255.255.0.0
 network 22.22.22.0 mask 255.255.255.0
 neighbor 10.1.1.13 remote-as 1001
 neighbor 10.2.0.6 remote-as 301
!
```
</details>

###### Настраиваем BGP AS301 R21
<details>
<summary>config-R21</summary>

```
router bgp 301
 bgp router-id 21.21.21.21
 bgp log-neighbor-changes
 network 10.3.0.0 mask 255.255.0.0
 network 21.21.21.0 mask 255.255.255.0
 neighbor 10.1.1.29 remote-as 1001
 neighbor 10.2.0.5 remote-as 101
 neighbor 10.3.0.1 remote-as 520
!
```
</details>

###### Настраиваем BGP AS520 R24
<details>
<summary>config-R24</summary>

```
router bgp 520
 bgp router-id 24.24.24.24
 bgp log-neighbor-changes
 network 10.4.0.0 mask 255.255.0.0
 network 24.24.24.0 mask 255.255.255.0
 neighbor 10.3.0.2 remote-as 301
 neighbor 10.5.0.9 remote-as 2042
!         
```
</details>

###### Настраиваем BGP AS2042 R18
<details>
<summary>config-R18</summary>

```
router bgp 2042
 bgp router-id 18.18.18.18
 bgp log-neighbor-changes
 network 10.5.0.0 mask 255.255.0.0
 network 18.18.18.0 mask 255.255.255.0
 neighbor 10.5.0.10 remote-as 520
!
```
</details>

###  

#### Результаты настройки:

###### show ip bgp + show ip bgp summary
<details>
<summary>R14</summary>

```
R14# show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.1.0.0/16      0.0.0.0                  0         32768 i
 *>  10.2.0.0/16      10.1.1.14                0             0 101 i
 *>  10.3.0.0/16      10.1.1.14                              0 101 301 i
 *>  10.4.0.0/16      10.1.1.14                              0 101 301 520 i
 *>  10.5.0.0/16      10.1.1.14                              0 101 301 520 2042 i
 *>  14.14.14.0/24    0.0.0.0                  0         32768 i
 *>  18.18.18.0/24    10.1.1.14                              0 101 301 520 2042 i
 *>  21.21.21.0/24    10.1.1.14                              0 101 301 i
 *>  22.22.22.0/24    10.1.1.14                0             0 101 i
 *>  24.24.24.0/24    10.1.1.14                              0 101 301 520 i
R14#

R14# show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.14       4          101      42      46       27    0    0 00:31:15        8
R14#
```
</details>
<details>
<summary>R15</summary>

```
R15#show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.1.0.0/16      0.0.0.0                  0         32768 i
 *>  10.2.0.0/16      10.1.1.30                              0 301 101 i
 *>  10.3.0.0/16      10.1.1.30                0             0 301 i
 *>  10.4.0.0/16      10.1.1.30                              0 301 520 i
 *>  10.5.0.0/16      10.1.1.30                              0 301 520 2042 i
 *>  15.15.15.0/24    0.0.0.0                  0         32768 i
 *>  18.18.18.0/24    10.1.1.30                              0 301 520 2042 i
 *>  21.21.21.0/24    10.1.1.30                0             0 301 i
 *>  22.22.22.0/24    10.1.1.30                              0 301 101 i
 *>  24.24.24.0/24    10.1.1.30                              0 301 520 i
R15#


R15#show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.30       4          301      42      42       19    0    0 00:32:28        8
R15#

```
</details>
<details>
<summary>R21</summary>

```
21#show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *   10.1.0.0/16      10.2.0.5                               0 101 1001 i
 *>                   10.1.1.29                0             0 1001 i
 *>  10.2.0.0/16      10.2.0.5                 0             0 101 i
 *>  10.3.0.0/16      0.0.0.0                  0         32768 i
 *>  10.4.0.0/16      10.3.0.1                 0             0 520 i
 *>  10.5.0.0/16      10.3.0.1                               0 520 2042 i
 *>  14.14.14.0/24    10.2.0.5                               0 101 1001 i
 *>  15.15.15.0/24    10.1.1.29                0             0 1001 i
 *>  18.18.18.0/24    10.3.0.1                               0 520 2042 i
 *>  21.21.21.0/24    0.0.0.0                  0         32768 i
 *>  22.22.22.0/24    10.2.0.5                 0             0 101 i
 *>  24.24.24.0/24    10.3.0.1                 0             0 520 i
R21#


R21#show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.29       4         1001      43      43       14    0    0 00:33:32        2
10.2.0.5        4          101      45      43       14    0    0 00:33:31        4
10.3.0.1        4          520      41      44       14    0    0 00:33:31        4
R21#

```
</details>
<details>
<summary>R22</summary>

```
R22#show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.1.0.0/16      10.1.1.13                0             0 1001 i
 *                    10.2.0.6                               0 301 1001 i
 *>  10.2.0.0/16      0.0.0.0                  0         32768 i
 *>  10.3.0.0/16      10.2.0.6                 0             0 301 i
 *>  10.4.0.0/16      10.2.0.6                               0 301 520 i
 *>  10.5.0.0/16      10.2.0.6                               0 301 520 2042 i
 *>  14.14.14.0/24    10.1.1.13                0             0 1001 i
 *>  15.15.15.0/24    10.2.0.6                               0 301 1001 i
 *>  18.18.18.0/24    10.2.0.6                               0 301 520 2042 i
 *>  21.21.21.0/24    10.2.0.6                 0             0 301 i
 *>  22.22.22.0/24    0.0.0.0                  0         32768 i
 *>  24.24.24.0/24    10.2.0.6                               0 301 520 i
R22#


R22#show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.1.1.13       4         1001      49      45       15    0    0 00:34:22        2
10.2.0.6        4          301      44      46       15    0    0 00:34:27        8
R22#
```
</details>
<details>
<summary>R24</summary>

```
R24#show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.1.0.0/16      10.3.0.2                               0 301 1001 i
 *>  10.2.0.0/16      10.3.0.2                               0 301 101 i
 *>  10.3.0.0/16      10.3.0.2                 0             0 301 i
 *>  10.4.0.0/16      0.0.0.0                  0         32768 i
 *>  10.5.0.0/16      10.5.0.9                 0             0 2042 i
 *>  14.14.14.0/24    10.3.0.2                               0 301 101 1001 i
 *>  15.15.15.0/24    10.3.0.2                               0 301 1001 i
 *>  18.18.18.0/24    10.5.0.9                 0             0 2042 i
 *>  21.21.21.0/24    10.3.0.2                 0             0 301 i
 *>  22.22.22.0/24    10.3.0.2                               0 301 101 i
 *>  24.24.24.0/24    0.0.0.0                  0         32768 i
R24#


R24#show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.3.0.2        4          301      46      43       14    0    0 00:35:14        7
10.5.0.9        4         2042      25      32       14    0    0 00:19:00        2
R24#
```
</details>
<details>
<summary>R18</summary>

```
R18#show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.1.0.0/16      10.5.0.10                              0 520 301 1001 i
 *>  10.2.0.0/16      10.5.0.10                              0 520 301 101 i
 *>  10.3.0.0/16      10.5.0.10                              0 520 301 i
 *>  10.4.0.0/16      10.5.0.10                0             0 520 i
 *>  10.5.0.0/16      0.0.0.0                  0         32768 i
 *>  14.14.14.0/24    10.5.0.10                              0 520 301 101 1001 i
 *>  15.15.15.0/24    10.5.0.10                              0 520 301 1001 i
 *>  18.18.18.0/24    0.0.0.0                  0         32768 i
 *>  21.21.21.0/24    10.5.0.10                              0 520 301 i
 *>  22.22.22.0/24    10.5.0.10                              0 520 301 101 i
 *>  24.24.24.0/24    10.5.0.10                0             0 520 i
R18#


R18#show ip bgp summary | beg Neighbor
Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.5.0.10       4          520      32      26       13    0    0 00:19:43        9
R18#
```
</details>

###### Проверяем доступность офисов Москва и санкт-Петербург.
<details>
<summary>traceroute R18(as2042) с R14(as1001)</summary>

```
R14#traceroute 18.18.18.18 source 14.14.14.14
Type escape sequence to abort.
Tracing the route to 18.18.18.18
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.1.14 1 msec 1 msec 0 msec
  2 10.2.0.6 [AS 101] 0 msec 1 msec 0 msec
  3 10.3.0.1 [AS 301] 1 msec 1 msec 1 msec
  4 10.5.0.9 [AS 2042] 2 msec *  1 msec
R14#
```
</details>

<details>
<summary>traceroute R18(as2042) с R15(as1001)</summary>

```
R15#traceroute 18.18.18.18 source 15.15.15.15
Type escape sequence to abort.
Tracing the route to 18.18.18.18
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.1.30 1 msec 1 msec 0 msec
  2 10.3.0.1 [AS 301] 1 msec 1 msec 1 msec
  3 10.5.0.9 [AS 2042] 1 msec *  1 msec
R15#
```
</details>

###  

<details>
<summary>traceroute R14(as1001) с R18(as2042)</summary>

```
R18#traceroute 14.14.14.14 source 18.18.18.18
Type escape sequence to abort.
Tracing the route to 14.14.14.14
VRF info: (vrf in name/id, vrf out name/id)
  1 10.5.0.10 1 msec 1 msec 1 msec
  2 10.3.0.2 [AS 301] 1 msec 1 msec 1 msec
  3 10.2.0.5 [AS 101] 1 msec 1 msec 1 msec
  4 10.1.1.13 [AS 1001] 2 msec *  2 msec
R18#

```
</details>

<details>
<summary>traceroute R15(as1001) с R18(as2042)</summary>

```
R18#traceroute 15.15.15.15 source 18.18.18.18
Type escape sequence to abort.
Tracing the route to 15.15.15.15
VRF info: (vrf in name/id, vrf out name/id)
  1 10.5.0.10 1 msec 1 msec 0 msec
  2 10.3.0.2 [AS 301] 1 msec 1 msec 1 msec
  3 10.1.1.29 [AS 1001] 1 msec *  4 msec
R18#
```
</details>

### Выводы:

По результатам трассировки видно, что связь между маршрутизаторами Москва и Санкт-Петербург настроена.
