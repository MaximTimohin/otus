#### Архитектура сети IPv4/6  
#  

#### Схема сети:

  ![alt-текст](/lab-4/img/main_map.png "Схема сети")


##### Общая информация:
Для настройки сети и клиентов используем сети:  
IPv4 сеть  10.0.0.0/8  
IPv6 сеть  20ff:aaaa:bbbb::/48  
Используем Link-local адреса FE80::/10
Для каждого устройства link-local соответствовать его номеру, например R28 FE80::28

Далее по каждой AS описание используемых подсетей.
<details>
<summary>AS1001</summary>

  ```
Используем IPv4 сеть  10.1.0.0/16
10.1.0.0/21 для управления и стыковки.
10.1.128.0/17 для пользователей
Используем IPv6 сеть 20ff:aaaa:bbbb:0100::/56

```
</details>

<details>
  <summary>AS101</summary>

  ```
  Используем IPv4 сеть  10.1.0.0/16
  10.2.0.0/21 для управления и стыковки.
  10.2.128.0/17 для пользователей
  Используем IPv6 сеть 20ff:aaaa:bbbb:0200::/56


```
</details>

<details>
  <summary>AS301</summary>

  ```
  Используем IPv4 сеть  10.0.0.0/16
  10.3.0.0/21 для управления и стыковки.
  10.3.128.0/17 для пользователей
  Используем IPv6 сеть 20ff:aaaa:bbbb:0300::/56

```
</details>

<details>
  <summary>AS520</summary>

  ```
  Используем IPv4 сеть  10.0.0.0/16
  10.4.0.0/21 для управления и стыковки.
  10.4.128.0/17 для пользователей
  Используем IPv6 сеть 20ff:aaaa:bbbb:0400::/56

```
</details>

<details>
  <summary>AS2042</summary>

  ```
  Используем IPv4 сеть  10.0.0.0/16
  10.5.0.0/21 для управления и стыковки.
  10.5.128.0/17 для пользователей
Используем IPv6 сеть 20ff:aaaa:bbbb:0500::/56

```
</details>  

<details>
  <summary>Таблица распределения IP адресов на оборудовании</summary>


|location        | v4_network | v4_prefix|v6_network| comments|
|:-----------:|:--------:|:---------------:|:-------------:|:---:|
|AS1001|10.1.0.0|16|20ff:aaaa:bbbb:0100::/56||
||10.1.1.0|30|20ff:aaaa:bbbb:0100::/64|R14-R19|
||10.1.1.4|30|20ff:aaaa:bbbb:0101::/64|R14-R12|
||10.1.1.8|30|20ff:aaaa:bbbb:0102::/64|R14-R13|
||10.1.1.12|30|20ff:aaaa:bbbb:0103::/64|R14-R22|
||10.1.1.16|30|20ff:aaaa:bbbb:0104::/64|R15-R12|
||10.1.1.20|30|20ff:aaaa:bbbb:0105::/64|R15-R13|
||10.1.1.24|30|20ff:aaaa:bbbb:0106::/64|R15-R20|
||10.1.1.28|30|20ff:aaaa:bbbb:0107::/64|R15-R21|
||10.1.1.32|30|20ff:aaaa:bbbb:0108::/64|R12-SW4|
||10.1.1.36|30|20ff:aaaa:bbbb:0109::/64|R12-SW5|
||10.1.1.40|30|20ff:aaaa:bbbb:010a::/64|R13-SW4|
||10.1.1.44|30|20ff:aaaa:bbbb:010b::/64|R13-SW5|
||10.1.10.0|24|20ff:aaaa:bbbb:010c::/64|switches vlan10|
||10.1.100.0|24|20ff:aaaa:bbbb:011::/64|Vlan200-SW2|
||10.1.101.0|24|20ff:aaaa:bbbb:012::/64|Vlan300-SW3|
||10.1.255.0|24|20ff:aaaa:bbbb:010d::/64|VPC1|
||10.1.254.0|24|20ff:aaaa:bbbb:010e::/64|VPC7|
|AS101|10.2.0.0|16|20ff:aaaa:bbbb:0200::/56||
||10.2.0.0|30|20ff:aaaa:bbbb:0200::/64|R22-R23|
||10.2.0.4|30|20ff:aaaa:bbbb:0201::/64|R22-R21|
|AS301|10.3.0.0|16|20ff:aaaa:bbbb:0300::/56||
||10.3.0.0|30|20ff:aaaa:bbbb:0300::/64|R21-R24|
|AS520|10.4.0.0|16|20ff:aaaa:bbbb:0400::/56||
||10.4.0.0|30|20ff:aaaa:bbbb:0400::/64|R23-R24|
||10.4.0.4|30|20ff:aaaa:bbbb:0401::/64|R23-R25|
||10.4.0.8|30|20ff:aaaa:bbbb:0402::/64|R24-R26|
||10.4.0.12|30|20ff:aaaa:bbbb:0403::/64|R25-R26|
|Лабытнанги|10.4.0.16|30|20ff:aaaa:bbbb:0404::/64|R25-R27|
|Чукордах|10.4.0.20|30|20ff:aaaa:bbbb:0510::/64|R25-R28|
||10.4.0.24|30|20ff:aaaa:bbbb:0405::/64|R26-R28|
||10.4.0.28|30|20ff:aaaa:bbbb:0406::/64|R28-SW29|
||10.4.255.0|24|20ff:aaaa:bbbb:0407::/64|VPC30|
||10.4.254.0|24|20ff:aaaa:bbbb:0408::/64|VPC31|
|AS2042|10.5.0.0|16|20ff:aaaa:bbbb:0500::/56|R18-R17|
||10.5.0.4|30|20ff:aaaa:bbbb:0500::/64|R18-R16|
||10.5.0.8|30|20ff:aaaa:bbbb:0501::/64|R18-R24|
||10.5.0.12|30|20ff:aaaa:bbbb:0502::/64|R18-R26|
||10.5.0.16|30|20ff:aaaa:bbbb:0503::/64|R17-SW9|
||10.5.0.20|30|20ff:aaaa:bbbb:0504::/64|R17-SW10|
||10.5.0.24|30|20ff:aaaa:bbbb:0505::/64|R16-SW9|
||10.5.0.28|30|20ff:aaaa:bbbb:0506::/64|R16-SW10|
||10.5.0.32|30|20ff:aaaa:bbbb:0507::/64|R16-R32|
||10.5.255.0|24|20ff:aaaa:bbbb:0508::/64|VPC8|
||10.5.254.0|24|20ff:aaaa:bbbb:0509::/64|VPC|


</details>  

#  

##### Рассмотрим настройки оборудования AS1001 в качестве примера настройки всей сети.

Внутри офиса 1000 предполагается следующая схема:

Vlan10 управления свичами.  
Vlan200 резервируется через STP и идёт до SW5  
Vlan300 резервируется через STP и идёт до SW4  
Access_l3 на свичах SW2(vlan35),SW3(vlan36) с адресом шлюза на свиче для клиентов.  



<details>
  <summary>Настройка L3 access на свичах SW2-SW3(часть конфигурации)</summary>

#### SW2
```
### Настройка порта в сторону клиента
interface Ethernet0/2
 description VPC7
 switchport access vlan 35

### Включаем маршрутизацию
ip routing
ipv6 unicast-routing

### Настраиваем влан для VPC7
 interface Vlan35
 ip address 10.1.255.1 255.255.255.0
 ipv6 address FE80::2 link-local
 ipv6 address 20FF:AAAA:BBBB:10E::1/64
 ipv6 enable
### Настраиваем влан для основного маршрута
 interface Vlan200
  ip address 10.1.100.2 255.255.255.0
  ipv6 address FE80::2 link-local
  ipv6 address 20FF:AAAA:BBBB:11::101/64
  ipv6 enable

### Не испольуемые порты:
switchport access vlan 997
 shutdown
```

#### SW3

```

### Настройка порта в сторону клиента
interface Ethernet0/2
 description VPC1
 switchport access vlan 36

### Включаем маршрутизацию
ip routing
ipv6 unicast-routing

### Настраиваем влан для VPC
interface Vlan36
 ip address 10.1.255.1 255.255.255.0
 ipv6 address FE80::3 link-local
 ipv6 address 20FF:AAAA:BBBB:10D::1/64
 ipv6 enable

### Настраиваем влан для основного маршрута
interface Vlan300
 ip address 10.1.101.3 255.255.255.0
 ipv6 address FE80::3 link-local
 ipv6 address 20FF:AAAA:BBBB:12::101/64
 ipv6 enable


### Не испольуемые порты:
switchport access vlan 997
shutdown

```
</details>

<details>
  <summary>Настройка SW4(часть конфигурации)</summary>

#### SW4
```
ip routing
!
ipv6 unicast-routing
!
interface Ethernet0/0
 description SW3
 switchport trunk allowed vlan 10,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/1
 description SW2
 switchport trunk allowed vlan 10,200
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/2
 description SW3-e0/2
 switchport trunk allowed vlan 10,200,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/3
 description SW3-e0/3
 switchport trunk allowed vlan 10,200,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/0
 description SW3
 switchport trunk allowed vlan 10,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/1
 description SW2
 switchport trunk allowed vlan 10,200
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/2
 description SW3-e0/2
 switchport trunk allowed vlan 10,200,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Ethernet0/3
 description SW3-e0/3
 switchport trunk allowed vlan 10,200,300
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 999
 switchport mode trunk
!
interface Vlan10
 description management
 ip address 10.1.10.4 255.255.255.0
 ipv6 address FE80::4 link-local
 ipv6 address 20FF:AAAA:BBBB:10C::52/64
 ipv6 enable
!
interface Vlan11
 ip address 10.1.1.34 255.255.255.252
 ipv6 address FE80::4 link-local
 ipv6 address 20FF:AAAA:BBBB:108::34/64
 ipv6 enable
!
interface Vlan12
 no ip address
 ipv6 address FE80::4 link-local
 ipv6 address 20FF:AAAA:BBBB:10A::42/64
 ipv6 enable
!
interface Vlan200
 ip address 10.1.100.253 255.255.255.0
 ipv6 address FE80::4 link-local
 ipv6 address 20FF:AAAA:BBBB:11::101/64
 ipv6 enable
!
interface Vlan300
 ip address 10.1.101.253 255.255.255.0
 ipv6 address FE80::4 link-local
ipv6 address 20FF:AAAA:BBBB:12::101/64
ipv6 enable

```

 </details>

 <details>
   <summary>Настройка SW5(часть конфигурации)</summary>

 #### SW5
 ```
 ip routing
 !
 ipv6 unicast-routing
 !
 interface Ethernet0/0
  description SW2
  switchport trunk allowed vlan 10,200
  switchport trunk encapsulation dot1q
  switchport trunk native vlan 999
  switchport mode trunk
 !
 interface Ethernet0/1
  description SW3
  switchport trunk allowed vlan 10,300
  switchport trunk encapsulation dot1q
  switchport trunk native vlan 999
  switchport mode trunk
 !
 interface Ethernet0/2
  description SW4-e0/2
  switchport trunk allowed vlan 10,200,300
  switchport trunk encapsulation dot1q
  switchport trunk native vlan 999
  switchport mode trunk
 !
 interface Ethernet0/3
  description SW4-e0/3
  switchport trunk allowed vlan 10,200,300
  switchport trunk encapsulation dot1q
  switchport trunk native vlan 999
  switchport mode trunk
  !
  interface Ethernet1/0
 description R13
 switchport trunk allowed vlan 12
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
 description R12
 switchport trunk allowed vlan 11
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
 interface Vlan10
 description management
 ip address 10.1.10.5 255.255.255.0
 ipv6 address FE80::5 link-local
 ipv6 address 20FF:AAAA:BBBB:10C::51/64
 ipv6 enable
 !
 interface Vlan200
 ip address 10.1.100.254 255.255.255.0
 ipv6 address FE80::5 link-local
 ipv6 address 20FF:AAAA:BBBB:11::100/64
 ipv6 enable
!
interface Vlan300
 ip address 10.1.101.254 255.255.255.0
 ipv6 address FE80::5 link-local
ipv6 address 20FF:AAAA:BBBB:12::100/64
ipv6 enable
!

 ```

  </details>
