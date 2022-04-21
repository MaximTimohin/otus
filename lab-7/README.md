#### IS-IS
Настраиваем IS-IS в офисе Триада.


* R23,R25 находятся в зоне 2222
* R26 находится в зоне 26
* R24 находится в зоне 24
* Проверяем результаты настройки

#### Схема сети:

  ![alt-текст](/lab-7/img/502.png)


##### Настраиваем R23
<details>
    <summary>config-R23</summary>

```
interface Loopback1
ip address 23.23.23.23 255.255.255.255
ip router isis 2222
!
interface Ethernet0/1
description R25
ip address 10.4.0.6 255.255.255.252
ip router isis 2222
!
interface Ethernet0/2
description R24
ip address 10.4.0.1 255.255.255.252
ip router isis 2222
!
router isis 2222
net 49.2222.0000.0023.00
!
```
</details>

##### Настраиваем R25
<details>
    <summary>config-R25</summary>

```
interface Loopback1
 ip address 25.25.25.25 255.255.255.255
 ip router isis 2222
!
interface Ethernet0/0
 description R23
 ip address 10.4.0.5 255.255.255.252
 ip router isis 2222
!
interface Ethernet0/2
 description R26
 ip address 10.4.0.13 255.255.255.252
 ip router isis 2222
!
router isis 2222
 net 49.2222.0000.0025.00
!
```
</details>

##### Настраиваем R24
<details>
    <summary>config-R24</summary>

```
interface Loopback1
 ip address 24.24.24.24 255.255.255.255
 ip router isis 24
!
interface Ethernet0/1
 description R26
 ip address 10.4.0.9 255.255.255.252
 ip router isis 24
!
interface Ethernet0/2
 description R23
 ip address 10.4.0.2 255.255.255.252
 ip router isis 24
!
router isis 24
 net 49.0024.0000.0024.00
```
</details>

##### Настраиваем R26
<details>
    <summary>config-R26</summary>

```
interface Loopback1
 ip address 26.26.26.26 255.255.255.255
 ip router isis 26
!
interface Ethernet0/0
 description R24
 ip address 10.4.0.10 255.255.255.252
 ip router isis 26
!
interface Ethernet0/2
 description R25
 ip address 10.4.0.14 255.255.255.252
 ip router isis 26
!
router isis 26
 net 49.0026.0000.0026.00
```
</details>

#### Результаты настройки  

<details>
    <summary>R23 isis neighbors</summary>

```
    R23#show isis neighbors

    Tag 2222:
    System Id      Type Interface   IP Address      State Holdtime Circuit Id
    R24            L1   Et0/2       10.4.0.2        UP    7        R24.01             
    R24            L2   Et0/2       10.4.0.2        UP    8        R24.01             
    R25            L1   Et0/1       10.4.0.5        UP    8        R25.01             
    R25            L2   Et0/1       10.4.0.5        UP    7        R25.01            
```
</details>

<details>
    <summary>R25 isis neighbors</summary>

```
R25#show isis neighbors

Tag 2222:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R26            L1   Et0/2       10.4.0.14       UP    9        R26.01             
R26            L2   Et0/2       10.4.0.14       UP    8        R26.01             
R23            L1   Et0/0       10.4.0.6        UP    27       R25.01             
R23            L2   Et0/0       10.4.0.6        UP    24       R25.01             
R25#

```
</details>

<details>
    <summary>R24 isis neighbors</summary>

```
R24#show isis neighbors

Tag 24:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R26            L1   Et0/1       10.4.0.10       UP    7        R26.02             
R26            L2   Et0/1       10.4.0.10       UP    9        R26.02             
R23            L1   Et0/2       10.4.0.1        UP    26       R24.01             
R23            L2   Et0/2       10.4.0.1        UP    22       R24.01             
R24#
```
</details>

<details>
    <summary>R26 isis neighbors</summary>

```
R26#show isis neighbors

Tag 26:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L1   Et0/0       10.4.0.9        UP    22       R26.02             
R24            L2   Et0/0       10.4.0.9        UP    23       R26.02             
R25            L1   Et0/2       10.4.0.13       UP    24       R26.01             
R25            L2   Et0/2       10.4.0.13       UP    25       R26.01             
```
</details>

####  

<details>
    <summary>R23 ip route isis</summary>

```
R23#show ip route isis | beg Gateway
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
i L1     10.4.0.8/30 [115/20] via 10.4.0.2, 00:39:20, Ethernet0/2
i L1     10.4.0.12/30 [115/20] via 10.4.0.5, 00:38:40, Ethernet0/1
      24.0.0.0/32 is subnetted, 1 subnets
i L1     24.24.24.24 [115/20] via 10.4.0.2, 00:39:20, Ethernet0/2
      25.0.0.0/32 is subnetted, 1 subnets
i L1     25.25.25.25 [115/20] via 10.4.0.5, 00:38:40, Ethernet0/1
      26.0.0.0/32 is subnetted, 1 subnets
i L1     26.26.26.26 [115/30] via 10.4.0.5, 00:40:23, Ethernet0/1
                     [115/30] via 10.4.0.2, 00:40:23, Ethernet0/2
R23#
```
</details>

<details>
    <summary>R25 ip route isis</summary>

```
R25#show ip route isis | beg Gateway
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
i L1     10.4.0.0/30 [115/20] via 10.4.0.6, 00:38:20, Ethernet0/0
i L1     10.4.0.8/30 [115/20] via 10.4.0.14, 00:40:57, Ethernet0/2
      23.0.0.0/32 is subnetted, 1 subnets
i L1     23.23.23.23 [115/20] via 10.4.0.6, 00:38:20, Ethernet0/0
      24.0.0.0/32 is subnetted, 1 subnets
i L1     24.24.24.24 [115/30] via 10.4.0.14, 00:39:54, Ethernet0/2
                     [115/30] via 10.4.0.6, 00:39:54, Ethernet0/0
      26.0.0.0/32 is subnetted, 1 subnets
i L1     26.26.26.26 [115/20] via 10.4.0.14, 00:40:57, Ethernet0/2
R25#

```
</details>

<details>
    <summary>R24 ip route isis</summary>

```
R24#show ip route isis | beg Gateway
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
i L1     10.4.0.4/30 [115/20] via 10.4.0.1, 00:38:38, Ethernet0/2
i L1     10.4.0.12/30 [115/20] via 10.4.0.10, 00:41:15, Ethernet0/1
      23.0.0.0/32 is subnetted, 1 subnets
i L1     23.23.23.23 [115/20] via 10.4.0.1, 00:38:38, Ethernet0/2
      25.0.0.0/32 is subnetted, 1 subnets
i L1     25.25.25.25 [115/30] via 10.4.0.10, 00:39:32, Ethernet0/1
                     [115/30] via 10.4.0.1, 00:39:32, Ethernet0/2
      26.0.0.0/32 is subnetted, 1 subnets
i L1     26.26.26.26 [115/20] via 10.4.0.10, 00:41:15, Ethernet0/1
R24#

```
</details>

<details>
    <summary>R26 ip route isis</summary>

```
R26#show ip route isis | beg Gateway
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 12 subnets, 3 masks
i L1     10.4.0.0/30 [115/20] via 10.4.0.9, 00:41:29, Ethernet0/0
i L1     10.4.0.4/30 [115/20] via 10.4.0.13, 00:40:50, Ethernet0/2
      23.0.0.0/32 is subnetted, 1 subnets
i L1     23.23.23.23 [115/30] via 10.4.0.13, 00:39:55, Ethernet0/2
                     [115/30] via 10.4.0.9, 00:39:55, Ethernet0/0
      24.0.0.0/32 is subnetted, 1 subnets
i L1     24.24.24.24 [115/20] via 10.4.0.9, 00:41:29, Ethernet0/0
      25.0.0.0/32 is subnetted, 1 subnets
i L1     25.25.25.25 [115/20] via 10.4.0.13, 00:40:50, Ethernet0/2
R26#


```
</details>
