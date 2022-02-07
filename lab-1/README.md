
# VLAN и маршрутизация между VLAN

###  Задание:
1. Настройка DTP.
2. Добавление сетей VLAN и назначение портов.
3. Проверка доступности.

###  Решение:

###### Список назначенных оборудованию Vlan/IP
 |vlan| IP| comment|interface|
 |----|------|--------|-------|
 |  3 | 192.168.3.1 |Sw-L3-R1|Ethernet0/0.3|
 |  3 | 192.168.3.11 |Sw-1|Vlan3|
 |  3 | 192.168.3.4 |Client-1 - Laptop|em0|
 |  4 | 192.168.4.1 |Sw-L3-R1|Ethernet0/0.4|
 |  4 | 192.168.4.11 |Sw-2|Vlan4|
 |  4 | 192.168.4.4 |Client-2 Laptop|em0|

###### Настройка L3 маршрутизатора Sw-L3-R1
<details>
  <summary>Включаем routing</summary>

```
Sw-L3-R1(config)#ip routing
Sw-L3-R1(config)#end
```
</details>

<details>
  <summary>Настраиваем интерфейс Ethernet0/0 идущий в сторону свича Sw-1</summary>

```
interface Ethernet0/0
description Sw-1_e0/0
no ip address
no shutdown
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet0/0.3</summary>

```
interface Ethernet0/0.3
 description vlan3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet0/0.4</summary>

```
interface Ethernet0/0.4
 description vlan4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
```
</details>
