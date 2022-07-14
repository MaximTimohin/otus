#### Основные протоколы сети интернет
###  




##### Цель:

* Настроить GRE поверх IPSec между офисами Москва и С.-Петербург
* Настроить DMVPN поверх IPSec между офисами Москва и Чокурдах, Лабытнанги
* Для IPSec используем CA и сертификаты.

#### Схема сети:
![alt-текст](/lab-9/img/bgp.png)

### Настройка:

###### Настраиваем

<details>
<summary>настройка СА на R14</summary>

```
### Запускаем СА
ip domain-name otuslab.ru
ip http server
crypto key generate rsa general-keys label IPSECVPN exportable modulus 2048
crypto pki server IPSECVPN
no shut

####Сертификат для самого R14
crypto key generate rsa label IPSECVPNTP14 modulus 2048
crypto pki trustpoint IPSECVPNTP14
enrollment url http://14.14.14.14
subject-name CN=R14,OU=IPSECVPN,O=otuslab,C=ru
rsakeypair IPSECVPNTP14
revocation-check none

###запрашиваем сертификат
crypto pki auth IPSECVPNTP14
###отправляем свой
crypto pki enroll  IPSECVPNTP14

```
</details>
<details>
<summary>настройка интерфейсов на R14</summary>

```
interface Tunnel100
 description GRE-R18
 tunnel protection ipsec profile IPSEC_PROF
!
interface Tunnel200
description DNVPN
tunnel protection ipsec profile IPSEC_PROF

!

```
</details>

<details>
<summary>настройка клиента R18</summary>

```
conf t
ip domain-name otuslab.ru

###Генерируем ключ и создаем траст поинт
crypto key generate rsa label IPSECVPNTP14 modulus 2048
crypto pki trustpoint IPSECVPNTP14
enrollment url http://14.14.14.14
subject-name CN=R18,OU=IPSECVPN,O=otuslab,C=ru
rsakeypair IPSECVPNTP14
revocation-check none
exit
###запрашиваем сертификат
crypto pki auth IPSECVPNTP14
###отправляем свой
crypto pki enroll  IPSECVPNTP14

crypto isakmp policy 10
authentication rsa-sig
exit
crypto ipsec transform-set IPSECSET esp-aes  esp-sha256-hmac
exit
crypto ipsec profile IPSEC_PROF
set transform-set IPSECSET

interface Tunnel100
tunnel protection ipsec profile IPSEC_PROF

```
</details>

<details>
<summary>настройка клиента R27</summary>

```
conf t
ip domain-name otuslab.ru
###Генерируем ключ и создаем траст поинт

crypto key generate rsa label IPSECVPNTP14 modulus 2048
crypto pki trustpoint IPSECVPNTP14
enrollment url http://14.14.14.14
subject-name CN=R27,OU=IPSECVPN,O=otuslab,C=ru
rsakeypair IPSECVPNTP14
revocation-check none
exit
###запрашиваем сертификат
crypto pki auth IPSECVPNTP14
###отправляем свой
crypto pki enroll  IPSECVPNTP14

crypto isakmp policy 10
authentication rsa-sig
exit
crypto ipsec transform-set IPSECSET esp-aes  esp-sha256-hmac
exit
crypto ipsec profile IPSEC_PROF
set transform-set IPSECSET

interface Tunnel200
tunnel protection ipsec profile IPSEC_PROF

```
</details>

<details>
<summary>настройка клиента R28</summary>

```
conf t
ip domain-name otuslab.ru
###Генерируем ключ и создаем траст поинт

crypto key generate rsa label IPSECVPNTP14 modulus 2048
crypto pki trustpoint IPSECVPNTP14
enrollment url http://14.14.14.14
subject-name CN=R28,OU=IPSECVPN,O=otuslab,C=ru
rsakeypair IPSECVPNTP14
revocation-check none
exit
###запрашиваем сертификат
crypto pki auth IPSECVPNTP14
###отправляем свой
crypto pki enroll  IPSECVPNTP14

crypto isakmp policy 10
authentication rsa-sig
exit
crypto ipsec transform-set IPSECSET esp-aes  esp-sha256-hmac
exit
crypto ipsec profile IPSEC_PROF
set transform-set IPSECSET

interface Tunnel200
tunnel protection ipsec profile IPSEC_PROF

```
</details>


<details>
<summary>Проверяем requests на R14</summary>

```
###Проверяем запросы от клиентов
show crypto pki server  IPSECVPN  requests

###Обобряем все
crypto pki server IPSECVPN grant
```
</details>

#### Проверяем результаты настройки
<details>
<summary>show crypto session на R14</summary>

```
R14#show crypto session
Crypto session current status

Interface: Tunnel100
Session status: UP-ACTIVE
Peer: 18.18.18.18 port 500
  Session ID: 0
  IKEv1 SA: local 14.14.14.14/500 remote 18.18.18.18/500 Active
  Session ID: 0
  IKEv1 SA: local 14.14.14.14/500 remote 18.18.18.18/500 Active
  IPSEC FLOW: permit 47 host 14.14.14.14 host 18.18.18.18
        Active SAs: 4, origin: crypto map

Interface: Tunnel200
Session status: UP-ACTIVE
Peer: 27.27.27.27 port 500
  Session ID: 0
  IKEv1 SA: local 14.14.14.14/500 remote 27.27.27.27/500 Active
  Session ID: 0
  IKEv1 SA: local 14.14.14.14/500 remote 27.27.27.27/500 Active
  IPSEC FLOW: permit 47 host 14.14.14.14 host 27.27.27.27
        Active SAs: 2, origin: crypto map

Interface: Tunnel200
Session status: UP-ACTIVE
Peer: 10.4.0.26 port 500
  Session ID: 0
  IKEv1 SA: local 14.14.14.14/500 remote 10.4.0.26/500 Active
  IPSEC FLOW: permit 47 host 14.14.14.14 host 10.4.0.26
        Active SAs: 2, origin: crypto map

R14#

```
</details>

<details>
<summary>show crypto ipsec  sa peer .... на R14</summary>

```
R14#show crypto ipsec  sa peer 18.18.18.18

interface: Tunnel100
    Crypto map tag: Tunnel100-head-0, local addr 14.14.14.14

   protected vrf: (none)
   local  ident (addr/mask/prot/port): (14.14.14.14/255.255.255.255/47/0)
   remote ident (addr/mask/prot/port): (18.18.18.18/255.255.255.255/47/0)
   current_peer 18.18.18.18 port 500
     PERMIT, flags={origin_is_acl,}
    #pkts encaps: 15, #pkts encrypt: 15, #pkts digest: 15
    #pkts decaps: 734, #pkts decrypt: 734, #pkts verify: 734
    #pkts compressed: 0, #pkts decompressed: 0
    #pkts not compressed: 0, #pkts compr. failed: 0
    #pkts not decompressed: 0, #pkts decompress failed: 0
    #send errors 0, #recv errors 0

     local crypto endpt.: 14.14.14.14, remote crypto endpt.: 18.18.18.18
     plaintext mtu 1438, path mtu 1500, ip mtu 1500, ip mtu idb Ethernet1/0
     current outbound spi: 0x78D9B4(7920052)
     PFS (Y/N): N, DH group: none

     inbound esp sas:
      spi: 0x707F22BB(1887380155)
        transform: esp-aes esp-sha256-hmac ,
        in use settings ={Tunnel, }
        conn id: 1, flow_id: SW:1, sibling_flags 80004040, crypto map: Tunnel100-head-0
        sa timing: remaining key lifetime (k/sec): (4608000/277)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)
      spi: 0x947455F4(2490652148)
        transform: esp-aes esp-sha256-hmac ,
        in use settings ={Tunnel, }
        conn id: 3, flow_id: SW:3, sibling_flags 80000040, crypto map: Tunnel100-head-0
        sa timing: remaining key lifetime (k/sec): (4336834/283)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     inbound ah sas:

     inbound pcp sas:

     outbound esp sas:
      spi: 0x4BD99D8C(1272552844)
        transform: esp-aes esp-sha256-hmac ,
        in use settings ={Tunnel, }
        conn id: 2, flow_id: SW:2, sibling_flags 80004040, crypto map: Tunnel100-head-0
        sa timing: remaining key lifetime (k/sec): (4608000/277)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)
      spi: 0x78D9B4(7920052)
        transform: esp-aes esp-sha256-hmac ,
        in use settings ={Tunnel, }
        conn id: 4, flow_id: SW:4, sibling_flags 80000040, crypto map: Tunnel100-head-0
        sa timing: remaining key lifetime (k/sec): (4336944/283)
        IV size: 16 bytes
        replay detection support: Y
        Status: ACTIVE(ACTIVE)

     outbound ah sas:

     outbound pcp sas:
R14#

```
</details>

<details>
<summary>Проверяем Ping с R14 до R18 через тоннель</summary>

```
R14#   ping 10.1.2.18 source 10.1.2.14
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.2.18, timeout is 2 seconds:
Packet sent with a source address of 10.1.2.14
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 6/6/6 ms
R14#

```
</details>
<details>
<summary>Проверяем Ping с R14 до R27 через тоннель</summary>

```
R14#   ping 10.1.200.27 source 10.1.200.14
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.200.27, timeout is 2 seconds:
Packet sent with a source address of 10.1.200.14
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 6/6/8 ms
R14#

```
</details>
<details>
<summary>Проверяем Ping с R14 до R28 через тоннель</summary>

```
R14#   ping 10.1.200.28 source 10.1.200.14
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.200.28, timeout is 2 seconds:
Packet sent with a source address of 10.1.200.14
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 6/6/8 ms
R14#

```
</details>
