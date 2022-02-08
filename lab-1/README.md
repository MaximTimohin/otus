
# VLAN и маршрутизация между VLAN

###  Задание:
1. Настройка DTP.
2. Добавление сетей VLAN и назначение портов.
3. Проверка доступности.

###  Решение:

#### Список назначенных оборудованию Vlan/IP
 |vlan| IP| comment|interface|
 |----|------|--------|-------|
 |  3 | 192.168.3.1 |Sw-L3-R1|Ethernet0/0.3|
 |  3 | 192.168.3.11 |Sw-1|Vlan3|
 |  3 | 192.168.3.3 |Client-1|e0|
 |  4 | 192.168.4.1 |Sw-L3-R1|Ethernet0/0.4|
 |  4 | 192.168.4.11 |Sw-2|Vlan4|
 |  4 | 192.168.4.3 |Client-2|e0|

#### Настройка L3 маршрутизатора Sw-L3-R1
 1. Производим базовые настройки и включаем routing.
 2. Настраиваем интерфейсы.
 3. Настраивает IP на интерфейсы.
 3. Сохраняем настройки.

<details>
  <summary>основные настройки</summary>

```
hostname Sw-L3-R1
no ip domain lookup
ip routing
end
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
 no shutdown
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet0/0.4</summary>

```
interface Ethernet0/0.4
 description vlan4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
 no shutdown
 end
 wr
```
</details>

#### Настройка L2 коммутатора Sw-1

1. Производим базовые настройки.
2. Настраиваем интерфейсы.
3. Настраивает IP на интерфейсы.
3. Настраиваем не используемые порты и сохраняем настройки.


<details>
  <summary>основные настройки</summary>

```
conf t
hostname Sw1
vlan 3
vlan 4
vlan 7
vlan 8
exit
interface Vlan3
 ip address 192.168.3.11 255.255.255.0
 no shutdown
exit
ip route 0.0.0.0 0.0.0.0 192.168.3.1
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet0/0 идущий в Sw-L3-R1_e0/0</summary>

```
switchport trunk allowed vlan 3,4
switchport trunk encapsulation dot1q
switchport mode trunk
switchport nonegotiate
no shutdown
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet1/1 идущий в Sw-2_e1/1</summary>

```
switchport trunk allowed vlan 3,4,8
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 8
switchport nonegotiate
no shutdown
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet2/1 идущий в Client-1_e0</summary>

```
description Clent1
switchport access vlan 3
switchport mode access
no shutdown
end
```
</details>

<details>
  <summary>Настраиваем не используемые порты</summary>

```
conf t
interface range e0/1-3, e1/0, e1/2-3, e2/0,e2/2-3, e3/0-3, e4/0-3, e5/0-3
switchport mode access
switchport access vlan 7
end
wr
```
</details>

#### Настройка L2 коммутатора Sw-2

1. Производим базовые настройки.
2. Настраиваем интерфейсы.
3. Настраивает IP на интерфейсы.
3. Настраиваем не используемые порты и сохраняем настройки.


<details>
  <summary>основные настройки</summary>

```
conf t
hostname Sw2
vlan 3
vlan 4
vlan 7
vlan 8
exit
interface Vlan3
 ip address 192.168.3.12 255.255.255.0
 no shutdown
exit
ip route 0.0.0.0 0.0.0.0 192.168.3.1
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet1/1 идущий в Sw-1_e1/1</summary>

```
switchport trunk allowed vlan 3,4,8
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk native vlan 8
switchport nonegotiate
no shutdown
```
</details>
<details>
  <summary>Настраиваем интерфейс Ethernet2/1 идущий в Client-2_e0</summary>

```
description Clent2
switchport access vlan 4
switchport mode access
no shutdown
end
```
</details>
<details>
  <summary>Настраиваем не используемые порты</summary>

```
conf t
interface range e0/0, e0/2-3, e1/0, e1/2-3, e2/0, e2/2-3, e3/0-3, e4/0-3, e5/0-3
switchport mode access
switchport access vlan 7
end
wr
```
</details>

#### Настройка Настраиваем компьютер Client-1

<details>
  <summary>интерфейс e0</summary>

```
Прописываем IP адрес 192.168.3.3 основной шлюз 192.168.3.1
```
</details>

#### Настройка Настраиваем компьютер Client-2
<details>
  <summary>интерфейс e0</summary>

```
Прописываем IP адрес 192.168.4.3 основной шлюз 192.168.4.1
```
</details>

#### Схема сети

![alt-текст](/lab-1/lab-1.png "Схема lab-1")
