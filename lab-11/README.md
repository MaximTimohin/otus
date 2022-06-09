#### BGP фильтрация.
###  




##### Цель:

* Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика
* Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика.
* Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
* Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.


#### Схема сети:

  ![alt-текст](/lab-9/img/bgp.png)

  #### Анонсируемые сети по AS
  <details>
  <summary>Таблица</summary>

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
  | 2042 | 10.5.0.0/16|Стыковка и клиентские сети
  |      |18.18.18.0/24|Loopback R18|

  </details>

  ### Настройка:

  ###### Настраиваем BGP AS1001 R14-R15
  <details>
  <summary>config-R14</summary>

  ```
  Фильтруем транзитный трафик.

  router bgp 1001
   neighbor 10.1.1.14 remote-as 101
   neighbor 10.1.1.14 soft-reconfiguration inbound
   neighbor 10.1.1.14 filter-list 10 out
  !
   ip as-path access-list 10 permit ^&
   ip as-path access-list 10 deny .*
  ```
  </details>

  <details>
  <summary>config-R15</summary>

  ```
  Фильтруем транзитный трафик.
router bgp 1001
 neighbor 10.1.1.30 remote-as 301
 neighbor 10.1.1.30 soft-reconfiguration inbound
 neighbor 10.1.1.30 filter-list 10 out
!
ip as-path access-list 10 permit ^$
ip as-path access-list 10 deny .*
!
```
Принимаем от R21 только AS301 и AS2042
```
ip as-path access-list 101 permit _2042$
ip as-path access-list 101 permit ^301$
ip as-path access-list 101 deny .*
!
route-map AS301_IN permit 10
 match as-path 101
!
router bgp 1001
 bgp router-id 15.15.15.15
 neighbor 10.1.1.30 route-map AS301_IN in
!
```
  </details>

<details>
<summary>config-R21</summary>

```
Отправляем к R15 только default и сети анонсируемые AS2042

router bgp 301
 neighbor 10.1.1.29 remote-as 1001
 neighbor 10.1.1.29 default-originate
 neighbor 10.1.1.29 soft-reconfiguration inbound
 neighbor 10.1.1.29 route-map AS1001_OUT out
!         
ip as-path access-list 101 permit _2042$
ip as-path access-list 101 deny .*
!
route-map AS1001_OUT permit 10
 match as-path 101
!
```
</details>

<details>
<summary>config-R22</summary>

```
Отправляем к R14 только default

router bgp 101
 neighbor 10.1.1.13 remote-as 1001
 neighbor 10.1.1.13 default-originate
 neighbor 10.1.1.13 soft-reconfiguration inbound
 neighbor 10.1.1.13 prefix-list R14_OUT out
!         
ip prefix-list R14_OUT seq 5 permit 0.0.0.0/0
!
```
</details>
<details>
<summary>config-R18</summary>

```
Отправляем к R24,R26 только сети принадлежащие AS2042

router bgp 2042
 neighbor 10.5.0.10 remote-as 520
 neighbor 10.5.0.10 prefix-list AS520_OUT out
 neighbor 10.5.0.13 remote-as 520
 neighbor 10.5.0.13 prefix-list AS520_OUT out
 maximum-paths 2
!
ip prefix-list AS520_OUT seq 5 permit 10.5.0.0/16
ip prefix-list AS520_OUT seq 10 permit 18.18.18.0/24
!
```
</details>


##### Проверка результатов настройки
<details>
<summary>R14</summary>

```
R14#  show ip bgp neighbors 10.1.1.14   received-routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  0.0.0.0          10.1.1.14                              0 101 i

Total number of prefixes 1
R14#

R14#  show ip bgp | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 * i 0.0.0.0          15.15.15.15              0    100      0 301 i
 *>                   10.1.1.14                              0 101 i
 * i 10.1.0.0/16      15.15.15.15              0    100      0 i
 *>                   0.0.0.0                  0         32768 i
 *>i 10.5.0.0/16      15.15.15.15              0    100      0 301 520 2042 i
 *>  14.14.14.0/24    0.0.0.0                  0         32768 i
 *>i 15.15.15.0/24    15.15.15.15              0    100      0 i
 *>i 18.18.18.0/24    15.15.15.15              0    100      0 301 520 2042 i
R14#
```
</details>


<details>
<summary>R15</summary>

```
R15#show ip bgp neighbors 10.1.1.30 received-routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  0.0.0.0          10.1.1.30                              0 301 i
 *>  10.5.0.0/16      10.1.1.30                              0 301 520 2042 i
 *>  18.18.18.0/24    10.1.1.30                              0 301 520 2042 i

Total number of prefixes 3
R15#

R15#show ip bgp  | beg Network                                   
     Network          Next Hop            Metric LocPrf Weight Path
 *>  0.0.0.0          10.1.1.30                              0 301 i
 * i                  14.14.14.14              0    100      0 101 i
 *>  10.1.0.0/16      0.0.0.0                  0         32768 i
 * i                  14.14.14.14              0    100      0 i
 *>  10.5.0.0/16      10.1.1.30                              0 301 520 2042 i
 r>i 14.14.14.0/24    14.14.14.14              0    100      0 i
 *>  15.15.15.0/24    0.0.0.0                  0         32768 i
 *>  18.18.18.0/24    10.1.1.30                              0 301 520 2042 i
R15#
```
</details>

<details>
<summary>R24</summary>

```
R24#show ip bgp neighbors 10.5.0.9 received-routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.5.0.0/16      10.5.0.9                 0             0 2042 i
 *>  18.18.18.0/24    10.5.0.9                 0             0 2042 i

Total number of prefixes 2
R24#
```
</details>

<details>
<summary>R26</summary>

```
R26#show ip bgp neighbors 10.5.0.14 received-routes | beg Network
     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.5.0.0/16      10.5.0.14                0             0 2042 i
 *>  18.18.18.0/24    10.5.0.14                0             0 2042 i

Total number of prefixes 2
R26#
```
</details>
