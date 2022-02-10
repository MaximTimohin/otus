# Развертывание коммутируемой сети с резервными каналами

###  Задание:
1. Создать сеть и настроить основные параметры устройств.
2. Выбрать корневой мост.
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.


### 1.	Создаём сеть и настраиваем основные параметры устройств.
#### Схема сети:

  ![alt-текст](/lab-2/1.png "Схема STP")



<details>
  <summary>Таблица коммутации оборудования</summary>


   |IP         | Sw_name| from interface|to Sw_name | to interface|
   |:-----------:|:--------:|:---------------:|:------:|:-------------:|
   | 192.168.1.1 | Sw-1 | e0/0 | Sw-3 | e0/0 |
   |             | Sw-1 | e0/1 | Sw-3 | e0/1 |
   |             | Sw-1 | e1/0 | Sw-2 | e1/0 |
   |             | Sw-1 | e1/1 | Sw-2 | e1/1 |

   |IP         | Sw_name| from interface|to Sw_name | to interface|
   |:-----------:|:--------:|:---------------:|:------:|:-------------:|
   | 192.168.1.2 | Sw-2 | e1/0 | Sw-1 | e1/0 |
   |             | Sw-2 | e1/1 | Sw-1 | e1/1 |
   |             | Sw-2 | e2/0 | Sw-3 | e2/0 |
   |             | Sw-2 | e2/1 | Sw-3 | e2/1 |

   |IP         | Sw_name| from interface|to Sw_name | to interface|
   |:-----------:|:--------:|:---------------:|:------:|:-------------:|
   | 192.168.1.3 | Sw-3 | e0/0 | Sw-1 | e0/0 |
   |             | Sw-3 | e0/1 | Sw-1 | e0/1 |
   |             | Sw-3 | e2/0 | Sw-2 | e2/0 |
   |             | Sw-3 | e2/1 | Sw-2 | e2/1 |


</details>










#### Настраиваем Sw-1
<details>
  <summary>Производим основные настройки</summary>

```
no ip domain lookup
banner motd «This is a secure system. Authorized Access Only!»
interface vlan1
ip address 192.168.1.1 255.255.255.0
no shut
```

</details>

<details>
  <summary>Настраиваем spanning-tree</summary>

```
spanning-tree mode rapid-pvst
wr
reload

```

</details>

<details>
<summary>Настраиваем и подписываем интерфейсы</summary>

```
interface 0/0    
  desc Sw-3_0/0   
interface 0/1    
  desc Sw-3_0/1
interface 1/0    
  desc Sw-2_1/0
interface 1/1    
  desc Sw-2_1/1      

### Настраиваем интерфейсы
#Выключаем все интерфейсы
interface range e0/0-3, e1/0-3, e2/0-3
shut
exit

#Включаем и настраиваем используемые интерфейсы
interface range e0/0-1, e1/0-1
switchport trunk encapsulation dot1q
switchport mode trunk  
no shut

```

</details>

<details>
  <summary>Смотрим состояние spanning-tree</summary>

```
Sw-1#show span

VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             Cost        100
             Port        5 (Ethernet1/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.5100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et1/0               Root FWD 100       128.5    Shr
Et1/1               Altn BLK 100       128.6    Shr
```
</details>


<details>
  <summary>Проверка связи между коммутаторами</summary>

 - Ping Sw-1 to Sw-2

```
Sw-1#ping 192.168.1.2 source 192.168.1.1 repeat 2
Sending 2, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
Packet sent with a source address of 192.168.1.1
!!
Success rate is 100 percent (2/2), round-trip min/avg/max = 1/1/1 ms
```

- Ping Sw-1 to Sw-3

```
Sw-1#ping 192.168.1.3 source 192.168.1.1 repeat 2
Sending 2, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
Packet sent with a source address of 192.168.1.1
!!
Success rate is 100 percent (2/2), round-trip min/avg/max = 1/1/1 ms
```


</details>









#### Настраиваем Sw-2

<details>
  <summary>Производим основные настройки</summary>

```
no ip domain lookup
banner motd «This is a secure system. Authorized Access Only!»
interface vlan1
ip address 192.168.1.2 255.255.255.0
no shut
```

</details>

<details>
  <summary>Настраиваем spanning-tree</summary>

```
spanning-tree mode rapid-pvst
wr
reload

```

</details>

<details>
<summary>Настраиваем и подписываем интерфейсы</summary>

```
interface 1/0    
  desc Sw-1_1/0   
interface 1/1    
  desc Sw-1_1/1
interface 2/0    
  desc Sw-3_2/0
interface 2/1    
  desc Sw-3_2/1      

### Настраиваем интерфейсы
#Выключаем все интерфейсы
interface range e0/0-3, e1/0-3, e2/0-3
shut
exit

#Включаем и настраиваем используемые интерфейсы
interface range e1/0-1, e2/0-1
switchport trunk encapsulation dot1q
switchport mode trunk  
no shut

```

</details>

<details>
  <summary>Смотрим состояние spanning-tree</summary>

```
Sw-2#show span

VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.4100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et1/0               Desg FWD 100       128.5    Shr
Et1/1               Desg FWD 100       128.6    Shr
Et2/0               Desg FWD 100       128.9    Shr
Et2/1               Desg FWD 100       128.10   Shr
```

</details>


<details>
  <summary>Проверка связи между коммутаторами</summary>

 - Ping Sw-2 to Sw-3

```
Sw-2#ping 192.168.1.3 source 192.168.1.2 repeat 2
Sending 2, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
Packet sent with a source address of 192.168.1.2
!!
Success rate is 100 percent (2/2), round-trip min/avg/max = 1/1/1 ms
```

</details>






#### Настраиваем Sw-3

<details>
  <summary>Производим основные настройки</summary>

```
no ip domain lookup
banner motd «This is a secure system. Authorized Access Only!»
interface vlan1
ip address 192.168.1.3 255.255.255.0
no shut
```

</details>

<details>
  <summary>Настраиваем spanning-tree</summary>

```
spanning-tree mode rapid-pvst
wr
reload

```

</details>

<details>
<summary>Настраиваем и подписываем интерфейсы</summary>

```
interface 0/0    
  desc Sw-1_0/0   
interface 0/1    
  desc Sw-1_0/1
interface 2/0    
  desc Sw-2_2/0
interface 2/1    
  desc Sw-2_2/1      

## Настраиваем интерфейсы
#Выключаем все интерфейсы
interface range e0/0-3, e1/0-3, e2/0-3
shut
exit

#Включаем и настраиваем используемые интерфейсы
interface range e0/0-1, e2/0-1
switchport trunk encapsulation dot1q
switchport mode trunk  
no shut

```

</details>

<details>
  <summary>Смотрим состояние spanning-tree</summary>

```
Sw-3#show span

VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             Cost        100
             Port        9 (Ethernet2/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.6100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr
Et0/1               Altn BLK 100       128.2    Shr
Et2/0               Root FWD 100       128.9    Shr
Et2/1               Altn BLK 100       128.10   Shr
```
</details>



### 2. Выбор корневого моста:
<details>
<summary>Определяем ROOT коммутатор</summary>
В выше приведённой конфигурации ROOT стал Sw-2 по тому что имеет наименьшее значение мак адреса:

```
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.4100
```

</details>

###### Ответы на вопросы:

<details>
<summary>Рассмотрим состояние коммутатора Sw-1(для ответа на некоторые вопросы)</summary>

1 - **Какие порты на коммутаторе являются корневыми портами.**
```
На примере коммутатора Sw-1
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et1/0               Root FWD 100       128.5    Shr
Et1/1               Altn BLK 100       128.6    Shr

Корневым портом стал - Et1/0

```
2 - **Какие порты на коммутаторе являются назначенными портами?**
```
На примере коммутатора Sw-1
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et1/0               Root FWD 100       128.5    Shr
Et1/1               Altn BLK 100       128.6    Shr

Назначенными портами стали e0/0-1
```
3 - **Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?**
```
На примере коммутатора Sw-1
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et1/0               Root FWD 100       128.5    Shr
Et1/1               Altn BLK 100       128.6    Shr

Порт e1/1 является алтернативным и в настоящее время заблокирован.
```
4 - **Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?**
```
На примере коммутатора Sw-1
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    Shr
Et0/1               Desg FWD 100       128.2    Shr
Et1/0               Root FWD 100       128.5    Shr
Et1/1               Altn BLK 100       128.6    Shr

Порт e1/0 был выбран в качестве корневого по тому что он соединён в порт с наименьшим номером e1/0,
второй линк между свичами осуществлён в порты e1/1 >>> e1/1 именно по этому порт e1/1 был выбран в качестве невыделенного(заблокированного).
```
</details>


### 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.

- Отключаем по одному линку между каждым свичём.
- Смотрим результаты:

<details>
<summary>Sw-1 - show spanning tree</summary>

```
Sw-1(config-if)#do show span

VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             Cost        100
             Port        6 (Ethernet1/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.5100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Desg FWD 100       128.2    Shr
Et1/1               Root FWD 100       128.6    Shr
```

</details>

<details>
<summary>Sw-2 - show spanning tree</summary>

```
Sw-2(config-if)#do show span
VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.4100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et1/1               Desg FWD 100       128.6    Shr
Et2/1               Desg FWD 100       128.10   Shr
```
</details>


<details>
<summary>Sw-3 - show spanning tree</summary>

```Sw-3(config-if)#do show span

VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     aabb.cc00.4100
             Cost        100
             Port        10 (Ethernet2/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.6100
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Altn BLK 100       128.2    Shr
Et2/1               Root FWD 100       128.10   Shr
```
</details>


- Меняем cost на Sw-3 e2/1 на 99
- Смотрим результаты:

<details>
<summary>Sw-1 - show spanning tree</summary>

###### после внесённых изменений на Sw-1 e0/1 стал  Altn/BLK

```

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Altn BLK 100       128.2    Shr
Et1/1               Root FWD 100       128.6    Shr
```

</details>

<details>
<summary>Sw-2 - show spanning tree</summary>

###### после внесённых изменений на Sw-3 e0/1 стал  Desg/FWD

```
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Desg FWD 100       128.2    Shr
Et2/1               Root FWD 99        128.10   Shr
```
</details>

 - Убираем cost на Sw-3 e2/1 и проверяем что все изменения отменились.

### 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.

 - Включаем все ранее выключенные линки.

 <details>
 <summary>Sw-3 show spanning tree</summary>

 ```
 Interface           Role Sts Cost      Prio.Nbr Type
 Et0/0               Altn BLK 100       128.1    Shr
 Et0/1               Altn BLK 100       128.2    Shr
 Et2/0               Root FWD 100       128.9    Shr
 Et2/1               Altn BLK 100       128.10   Shr
```
##### root портом стал e2/0 т.к он имеет наименьший номер связанный с root коммутатором.
 </details>
