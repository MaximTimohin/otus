#### Основные протоколы сети интернет
###  




##### Цель:

* Настроить DHCP в офисе Москва
* Настроить синхронизацию времени в офисе Москва
* Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах


#### Схема сети:

  ![alt-текст](/lab-9/img/bgp.png)

  ### Настройка:

  ###### Настраиваем DHCP в офисе Москва
  <details>
  <summary>config-R12</summary>

#### Настариваем DHCP с резервированием на случай отказа R13

  ```
  !
ip dhcp pool VPC7
 host 10.1.255.254 255.255.255.0
 client-identifier 0100.5079.6668.07
 default-router 10.1.255.1
 domain-name ccna-lab.com
 lease 2 12 30
!
ip dhcp pool VPC1
 host 10.1.254.254 255.255.255.0
 client-identifier 0100.5079.6668.01
 default-router 10.1.254.1
 domain-name ccna-lab.com
 lease 2 12 30
!


  ```
  </details>

  <details>
  <summary>config-R13</summary>


#### Настариваем DHCP с резервированием на случай отказа R12


  ```
  !
  ip dhcp pool VPC7
   host 10.1.255.254 255.255.255.0
   client-identifier 0100.5079.6668.07
   default-router 10.1.255.1
   domain-name ccna-lab.com
   lease 2 12 30
  !
  ip dhcp pool VPC1
   host 10.1.254.254 255.255.255.0
   client-identifier 0100.5079.6668.01
   default-router 10.1.254.1
   domain-name ccna-lab.com
   lease 2 12 30
  !
  ```
  </details>

  <details>
  <summary>config-SW2</summary>

  ```
  interface Vlan35
   ip helper-address 12.12.12.12
   ip helper-address 13.13.13.13
  !
  ```
  </details>

  <details>
  <summary>config-SW3</summary>

  ```
  interface Vlan36
   ip helper-address 13.13.13.13
   ip helper-address 12.12.12.12
  !
  ```
  </details>

<details>
<summary>Проверка работы DHCP на VPC</summary>

```
VPCS> show ip

IP/MASK     : 10.1.255.254/24
GATEWAY     : 10.1.255.1
DNS         :
DHCP SERVER : 10.1.1.45
DHCP LEASE  : 147123, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:07
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS>
```
</details>


######  Настариваем NTP в офисе Москва

<details>
<summary>config-NTP-R12-R13</summary>

#### Такую настройку делаем на R13

```
clock timezone MSK 3 0
!
ntp master 2
ntp server 216.239.35.12
ntp server 216.239.35.4
!
```

</details>
<details>
<summary>config-NTP-остльные</summary>

#### Такую настройку делаем на всех устройствах

```
clock timezone MSK 3 0
!
ntp update-calendar
ntp server 12.12.12.12
ntp server 13.13.13.13
!
```
</details>

</details>
<details>
<summary>Проверяем NTP status на свичах</summary>


```
SW2#show ntp status
Clock is synchronized, stratum 3, reference is 12.12.12.12    
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 40300 (1/100 of seconds), resolution is 4000
reference time is E66D4054.47EF9E78 (13:32:52.281 MSK Mon Jul 4 2022)
clock offset is 0.5000 msec, root delay is 1.00 msec
root dispersion is 261.56 msec, peer dispersion is 5.27 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 64, last update was 393 sec ago.
SW2#

```
</details>


###### Настраиваем NAT

##### AS1001
<details>
<summary>config-R14-AS1001</summary>

```
ip nat inside source route-map NAT1 interface Ethernet0/2 overload
!
!
route-map NAT1 permit 10
 match ip address 1
 match interface Ethernet0/2
!
!
access-list 1 permit 10.1.255.0 0.0.0.255
access-list 1 permit 10.1.254.0 0.0.0.255
!
```
</details>

<details>
<summary>config-R15-AS1001</summary>

```
interface Ethernet0/2
 ip nat outside
 ip virtual-reassembly in
!
interface Ethernet0/0
 ip nat inside
!
interface Ethernet0/1
 ip nat inside
!
interface Ethernet1/0
 ip nat inside
!
ip nat inside source route-map NAT1 interface Ethernet0/2 overload
!
route-map NAT1 permit 10
 match ip address 1
 match interface Ethernet0/2
!
access-list 1 permit 10.1.254.0 0.0.0.255
access-list 1 permit 10.1.255.0 0.0.0.255
!
```
</details>

<details>
<summary>show ip NAT translations-AS1001</summary>

```
R15#show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
icmp 10.1.1.29:32376   10.1.255.7:32376   24.24.24.24:32376  24.24.24.24:32376
icmp 10.1.1.29:32632   10.1.255.7:32632   24.24.24.24:32632  24.24.24.24:32632
icmp 10.1.1.29:32888   10.1.255.7:32888   24.24.24.24:32888  24.24.24.24:32888
icmp 10.1.1.29:33144   10.1.255.7:33144   24.24.24.24:33144  24.24.24.24:33144
icmp 10.1.1.29:33400   10.1.255.7:33400   24.24.24.24:33400  24.24.24.24:33400
R15#
```
</details>

##### AS2042
<details>
<summary>config-R18-AS2024</summary>

```
interface Ethernet0/0
 description R16
 ip nat inside
 ip virtual-reassembly in
!
interface Ethernet0/1
 description R17
 ip nat inside
 ip virtual-reassembly in
!
interface Ethernet0/2
 description R24
 ip nat outside
 ip virtual-reassembly in
!
interface Ethernet0/3
 description R26
 ip nat outside
 ip virtual-reassembly in
!
ip nat pool NAT-POOL-1 10.5.0.49 10.5.0.54 prefix-length 28
ip nat inside source route-map NAT1 pool NAT-POOL-1 overload
ip nat inside source route-map NAT2 pool NAT-POOL-1 overload
!
route-map NAT2 permit 10
 match ip address 1
 match interface Ethernet0/3
!
route-map NAT1 permit 10
 match ip address 1
 match interface Ethernet0/2
!
!
access-list 1 permit 10.5.128.0 0.0.0.255
access-list 1 permit 10.5.129.0 0.0.0.255
!
```
</details>


<details>
<summary>config-R28-Чокурдах</summary>

```
interface Ethernet0/0
 description R26
 ip nat outside
 ip virtual-reassembly in

!
interface Ethernet0/1
 description R25
 ip nat outside
 ip virtual-reassembly in
!
interface Ethernet0/2
 description SW29
 no ip address
 ip nat inside
 ip virtual-reassembly in
!
ip nat inside source static 10.4.255.30 10.4.0.33
ip nat inside source static 10.4.254.31 10.4.0.34

```
</details>
