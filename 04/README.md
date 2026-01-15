Проектирование адресного пространства
=====================================

### Цель: 

- Собрать схему CLOS
- Распределить адресное пространство

### Настроить BGP для Underlay сети.
### Описание/Пошаговая инструкция выполнения домашнего задания:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

- Настроите BGP в Underlay сети, для IP связанности между всеми сетевыми устройствами. iBGP или eBGP - решать вам!
- Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
- Убедитесь в наличии IP связанности между устройствами в BGP домене

### Топология сети

![scheme_addr.png](scheme_addr2.png)

### Таблица адресов

|Device|Interface|IP Address|Subnet Mask
|---|---|---|---|
Spine1|lo1|10.0.1.1|255.255.255.255
Spine1|eth1|10.2.1.1|255.255.255.252
Spine1|eth2|10.2.1.5|255.255.255.252
Spine1|eth3|10.2.1.9|255.255.255.252
Spine2|lo1|10.0.2.1|255.255.255.255
Spine2|eth1|10.2.2.1|255.255.255.252
Spine2|eth2|10.2.2.5|255.255.255.252
Spine2|eth3|10.2.2.9|255.255.255.252
Leaf1|lo1|10.1.1.1|255.255.255.255
Leaf1|eth1|10.2.1.2|255.255.255.252
Leaf1|eth2|10.2.2.2|255.255.255.252
Leaf1|eth3|192.168.1.1|255.255.255.0
Leaf2|lo1|10.1.2.1|255.255.255.255
Leaf2|eth1|10.2.1.6|255.255.255.252
Leaf2|eth2|10.2.2.6|255.255.255.252
Leaf2|eth3|192.168.2.1|255.255.255.0
Leaf3|lo1|10.1.3.1|255.255.255.255
Leaf3|eth1|10.2.1.10|255.255.255.252
Leaf3|eth2|10.2.2.10|255.255.255.252
Leaf3|eth3|192.168.3.1|255.255.255.0
Leaf3|eth4|192.168.4.1|255.255.255.0
Client1|eth0|192.168.1.100|255.255.255.0
Client2|eth0|192.168.2.100|255.255.255.0
Client3|eth0|192.168.3.100|255.255.255.0
Client4|eth0|192.168.4.100|255.255.255.0


<details>

<summary> Общая информация </summary>

BGP (англ. Border Gateway Protocol, протокол граничного шлюза) — дистанционно-векторный протокол динамической маршрутизации. Относится к классу протоколов маршрутизации внешнего шлюза (англ. EGP — Exterior Gateway Protocol).</br>
Протокол BGP предназначен для обмена информацией о достижимости подсетей между автономными системами (АС, англ. AS — autonomous system), то есть группами маршрутизаторов под единым техническим и административным управлением, использующими протокол внутридоменной маршрутизации для определения маршрутов внутри себя и протокол междоменной маршрутизации для определения маршрутов доставки пакетов в другие АС. Передаваемая информация включает в себя список АС, к которым имеется доступ через данную систему. Выбор наилучших маршрутов осуществляется исходя из правил, принятых в сети.</br>
BGP является протоколом прикладного уровня и функционирует поверх протокола транспортного уровня TCP (порт 179). После установки соединения передаётся информация обо всех маршрутах, предназначенных для экспорта. В дальнейшем передаётся только информация об изменениях в таблицах маршрутизации. При закрытии соединения удаляются все маршруты, информация о которых передана противоположной стороной.</br>
Когда протокол BGP используется между двумя узлами в одной автономной системе (AS), он называется внутренним BGP (iBGP или внутренний протокол пограничного шлюза). Когда он используется между разными автономными системами, он называется внешним BGP (eBGP или внешний протокол пограничного шлюза).</br>

BGP имеет следующие преимущества:

- Отличная масштабируемость;
- Поддержка сетевых масок переменной длины (VLSM);
- Оптимальное использование пропускной способности с построением дерева кратчайших путей;
- Возможность поддерживать огромные таблицы маршрутов;
- Субсекундная конвергенция. 

</details>

### Выполнение:


Произведем начальную настройку коммутаторов, в которой выполним команды конфигурирования адресного пространства:
<details>

<summary> Начальная настройка </summary>
  
#### Spine 1
```
hostname Spine1
!
interface Ethernet1
   no switchport
   ip address 10.2.1.1/30
!
interface Ethernet2
   no switchport
   ip address 10.2.1.5/30
!
interface Ethernet3
   no switchport
   ip address 10.2.1.9/30
!
interface Loopback0
   ip address 10.0.1.1/32
!
```
#### Spine 2
```
hostname Spine2
!
interface Ethernet1
   no switchport
   ip address 10.2.2.1/30
!
interface Ethernet2
   no switchport
   ip address 10.2.2.5/30
!
interface Ethernet3
   no switchport
   ip address 10.2.2.9/30
!
interface Loopback0
   ip address 10.0.2.1/32
!
```
#### Leaf 1
```
hostname Leaf1
!
interface Ethernet1
   no switchport
   ip address 10.2.1.2/30
!
interface Ethernet2
   no switchport
   ip address 10.2.2.2/30
!
interface Ethernet3
   no switchport
   ip address 192.168.1.1/24
!
interface Loopback0
   ip address 10.1.1.1/32
!
```

#### Leaf 2
```
hostname Leaf2
!
interface Ethernet1
   no switchport
   ip address 10.2.1.6/30
!
interface Ethernet2
   no switchport
   ip address 10.2.2.6/30
!
interface Ethernet3
   no switchport
   ip address 192.168.2.1/24
!
interface Loopback0
   ip address 10.1.2.1/32
!
```

#### Leaf 3
```
hostname Leaf3
!
interface Ethernet1
   no switchport
   ip address 10.2.1.10/30
!
interface Ethernet2
   no switchport
   ip address 10.2.2.10/30
!
interface Ethernet3
   no switchport
   ip address 192.168.3.1/24
!
interface Ethernet4
   no switchport
   ip address 192.168.4.1/24
!
interface Loopback0
   ip address 10.1.3.1/32
!
```
#### Client 1
```
VPCS> ip 192.168.1.100 255.255.255.0 192.168.1.1
```
#### Client 2
```
VPCS> ip 192.168.2.100 255.255.255.0 192.168.2.1
```
#### Client 3
```
VPCS> ip 192.168.3.100 255.255.255.0 192.168.3.1
```
#### Client 4
```
VPCS> ip 192.168.4.100 255.255.255.0 192.168.4.1
```
</details>

Нам необходимо настроить сеть таким образом, чтобы клиенты видели друг друга и могли передавать траффик. 

Для выполнения лабораторной работы выберем eBGP, так как этот протокол легче масштабировать. Соседство будем строить между внешними интерфейсами.

Протокол eBGP является протоколом, соединяющим различные автономные системы. В данной лабораторной работе мы поместим спайны в автономную систему 65000, а каждый из лифов в отдельную автономную систему: leaf1 - 65001, leaf2 - 65002, leaf3 - 65003. Для избежания неконтролируемого и избыточного прохождения траффика не будем разрешать разрешать локальные номера автономных систем (AS) в обновлениях BGP. Так же для упрощения настройки соседей введем peer группы в глобальном модуле BGP и будем активировать соседей в address-family ipv4.
```
neighbor LEAF_NEIGHBOR peer group
neighbor SPINE_NEIGHBOR peer group
```

Значения всех таймеров кроме таймера задержки отправки BGP UPDATE сообщений оставим по умолчанию, а для быстрого обнаружения проблем на линках включим протокол BFD.
```
neighbor хххх bfd
```

На leaf1, leaf2, leaf3 включим балансировку ECMP для балансировки траффика по спайнам:
```
maximum-paths 2 ecmp 2
```

На spine1, spine2 включим динамическое BGP подключение соседей с требуемых автономных систем:
```
peer-filter LEAF_PF
   10 match as-range 65001-65003 result accept
!

bgp listen range 10.2.1.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
```

Таким образом, итоговые конфигурации коммутаторов будут выглядеть так:

<details>
<summary> Итоговая конфигурация </summary>
  
#### Spine 1
```
hostname Spine1
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.2.1.1/30
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.2.1.5/30
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 10.2.1.9/30
!
interface Loopback0
   ip address 10.0.1.1/32
!
ip routing
!
peer-filter LEAF_PF
   10 match as-range 65001-65003 result accept
!
router bgp 65000
   router-id 10.0.1.1
   bgp listen range 10.2.1.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   !
   address-family ipv4
      neighbor LEAF_NEIGHBOR activate
      network 10.0.1.1/32
!
end

```
  
#### Spine 2
```
hostname Spine2
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.2.2.1/30
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.2.2.5/30
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 10.2.2.9/30
!
interface Loopback0
   ip address 10.0.2.1/32
!
ip routing
!
peer-filter LEAF_PF
   10 match as-range 65001-65003 result accept
!
router bgp 65000
   router-id 10.0.2.1
   bgp listen range 10.2.2.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   !
   address-family ipv4
      neighbor LEAF_NEIGHBOR activate
      network 10.0.2.1/32
!
end

```
  
#### Leaf 1
```

hostname Leaf1
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.2.1.2/30
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.2.2.2/30
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 192.168.1.1/24
!
interface Loopback0
   ip address 10.1.1.1/32
!
ip routing
!
router bgp 65001
   router-id 10.1.1.1
   maximum-paths 2 ecmp 2
   neighbor SPINE_NEIGHBOR peer group
   neighbor SPINE_NEIGHBOR remote-as 65000
   neighbor SPINE_NEIGHBOR out-delay 0
   neighbor SPINE_NEIGHBOR bfd
   neighbor 10.2.1.1 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.1 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.1.1/32
      network 192.168.1.0/24
!
end

```

#### Leaf 2
```

hostname Leaf2
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.2.1.6/30
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.2.2.6/30
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 192.168.2.1/24
!
interface Loopback0
   ip address 10.1.2.1/32
!
ip routing
!
router bgp 65002
   router-id 10.1.2.1
   maximum-paths 2 ecmp 2
   neighbor SPINE_NEIGHBOR peer group
   neighbor SPINE_NEIGHBOR remote-as 65000
   neighbor SPINE_NEIGHBOR out-delay 0
   neighbor SPINE_NEIGHBOR bfd
   neighbor 10.2.1.5 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.5 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.2.1/32
      network 192.168.2.0/24
!
end

```

#### Leaf 3
```
hostname Leaf3
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.2.1.10/30
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.2.2.10/30
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 192.168.3.1/24
!
interface Ethernet4
   mtu 9000
   no switchport
   ip address 192.168.4.1/24
!
interface Loopback0
   ip address 10.1.3.1/32
!
ip routing
!
router bgp 65003
   router-id 10.1.3.1
   maximum-paths 2 ecmp 2
   neighbor SPINE_NEIGHBOR peer group
   neighbor SPINE_NEIGHBOR remote-as 65000
   neighbor SPINE_NEIGHBOR out-delay 0
   neighbor SPINE_NEIGHBOR bfd
   neighbor 10.2.1.9 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.9 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.3.1/32
      network 192.168.3.0/24
      network 192.168.4.0/24
!
end

```
</details>

После настройки на сетевых устройствах протокола маршрутизации проверим результаты.

 Пробуем с Client1 "достучаться" до Client2, Client3 и Client4:
 #### Clients
 ![ping.png](ping.png)
 
 Как видим Client1 видит других клиентов.


 Далее посмотрим eBGP соседей на спайнах:
<details>
<summary> Neighbors </summary>
  
 #### Spine 1
 ```
BGP neighbor is 10.2.1.2, remote AS 65001, external link
  BGP version 4, remote router ID 10.1.1.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read never, last write never
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:45:38
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                8         3
    Keepalives:          9939      9940
    Route-Refresh:          0         0
    Total messages:      9948      9944
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           6         2              2                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 1
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.1.1
TTL is 1
Local TCP address is 10.2.1.1, local port is 179
Remote TCP address is 10.2.1.2, remote port is 35251
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 0/32768
  Outgoing Maximum Segment Size (MSS): 8948
  Total Number of TCP retransmissions: 2
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 216.0ms
    Round-trip Time (rtt/rtvar): 14.5ms/1.8ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 49.46 Mbps
    Recv Round-trip Time (rcv_rtt): 199795.2ms
    Advertised Recv Window (rcv_space): 65219


BGP neighbor is 10.2.1.6, remote AS 65002, external link
  BGP version 4, remote router ID 10.1.2.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read never, last write 00:00:01
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:44:15
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                6         3
    Keepalives:          9856      9857
    Route-Refresh:          0         0
    Total messages:      9863      9861
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           6         2              2                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 1
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.1.1
TTL is 1
Local TCP address is 10.2.1.5, local port is 179
Remote TCP address is 10.2.1.6, remote port is 44159
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 0/32768
  Outgoing Maximum Segment Size (MSS): 1448
  Total Number of TCP retransmissions: 0
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 220.0ms
    Round-trip Time (rtt/rtvar): 18.3ms/9.8ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 6.33 Mbps
    Recv Round-trip Time (rcv_rtt): 199797.7ms
    Advertised Recv Window (rcv_space): 65219


BGP neighbor is 10.2.1.10, remote AS 65003, external link
  BGP version 4, remote router ID 10.1.3.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read 00:00:01, last write never
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:40:11
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                4         3
    Keepalives:          9613      9612
    Route-Refresh:          0         0
    Total messages:      9618      9616
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           5         3              3                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 1
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.1.1
TTL is 1
Local TCP address is 10.2.1.9, local port is 179
Remote TCP address is 10.2.1.10, remote port is 43183
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 0/32768
  Outgoing Maximum Segment Size (MSS): 1448
  Total Number of TCP retransmissions: 1
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 228.0ms
    Round-trip Time (rtt/rtvar): 26.1ms/19.6ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 4.43 Mbps
    Recv Round-trip Time (rcv_rtt): 199814.9ms
    Advertised Recv Window (rcv_space): 65223


 ```
 #### Spine 2
 ```
BGP neighbor is 10.2.2.2, remote AS 65001, external link
  BGP version 4, remote router ID 10.1.1.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read never, last write never
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:46:00
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                8         9
    Keepalives:          9963      9962
    Route-Refresh:          0         0
    Total messages:      9972      9972
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           6         2              2                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 5
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.2.1
TTL is 1
Local TCP address is 10.2.2.1, local port is 179
Remote TCP address is 10.2.2.2, remote port is 38575
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 19/32768
  Outgoing Maximum Segment Size (MSS): 8948
  Total Number of TCP retransmissions: 1
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 220.0ms
    Round-trip Time (rtt/rtvar): 16.5ms/7.8ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 43.42 Mbps
    Recv Round-trip Time (rcv_rtt): 183798.8ms
    Advertised Recv Window (rcv_space): 65231


BGP neighbor is 10.2.2.6, remote AS 65002, external link
  BGP version 4, remote router ID 10.1.2.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read never, last write never
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:45:06
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                6         7
    Keepalives:          9908      9908
    Route-Refresh:          0         0
    Total messages:      9915      9916
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           6         2              2                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 4
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.2.1
TTL is 1
Local TCP address is 10.2.2.5, local port is 179
Remote TCP address is 10.2.2.6, remote port is 36047
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 0/32768
  Outgoing Maximum Segment Size (MSS): 1448
  Total Number of TCP retransmissions: 0
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 224.0ms
    Round-trip Time (rtt/rtvar): 21.3ms/3.0ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 5.43 Mbps
    Recv Round-trip Time (rcv_rtt): 187798.5ms
    Advertised Recv Window (rcv_space): 65215


BGP neighbor is 10.2.2.10, remote AS 65003, external link
  BGP version 4, remote router ID 10.1.3.1, VRF default
  Inherits configuration from and member of peer-group LEAF_NEIGHBOR
  Negotiated BGP version 4
  Member of update group 2
  Last read never, last write never
  Hold time is 3, keepalive interval is 1 seconds
  Configured hold time is 3, keepalive interval is 1 seconds
  Connect timer is inactive
  Idle-restart timer is inactive
  BGP state is Established, up for 02:41:01
  Number of transitions to established: 1
  Last state was OpenConfirm
  Last event was RecvKeepAlive
  Neighbor Capabilities:
    Multiprotocol IPv4 Unicast: advertised and received and negotiated
    Four Octet ASN: advertised and received and negotiated
    Route Refresh: advertised and received and negotiated
    Send End-of-RIB messages: advertised and received and negotiated
    Additional-paths recv capability:
      IPv4 Unicast: advertised
    Additional-paths send capability:
      IPv4 Unicast: received
  Restart timer is inactive
  End of rib timer is inactive
  Message Statistics:
    InQ depth is 0
    OutQ depth is 0
                         Sent      Rcvd
    Opens:                  1         1
    Notifications:          0         0
    Updates:                4         5
    Keepalives:          9663      9663
    Route-Refresh:          0         0
    Total messages:      9668      9669
  Prefix Statistics:
                         Sent      Rcvd     Best Paths     Best ECMP Paths
    IPv4 Unicast:           5         3              3                   0
    IPv6 Unicast:           0         0              0                   0
    IPv4 SR-TE:             0         0              0                   0
    IPv6 SR-TE:             0         0              0                   0
  Inbound updates dropped by reason:
    AS path loop detection: 3
    Enforced First AS: 0
    Originator ID matches local router ID: 0
    Nexthop matches local IP address: 0
    Unexpected IPv6 nexthop for IPv4 routes: 0
    Nexthop invalid for single hop eBGP: 0
  Inbound updates with attribute errors:
    Resulting in removal of all paths in update (treat-as-withdraw): 0
    Resulting in AFI/SAFI disable: 0
    Resulting in attribute ignore: 0
  Inbound paths dropped by reason:
    IPv4 labeled-unicast NLRIs dropped due to excessive labels: 0
    IPv6 labeled-unicast NLRIs dropped due to excessive labels: 0
  Outbound paths dropped by reason:
    IPv4 local address not available: 0
    IPv6 local address not available: 0
    Inbound policy
    Outbound policy
Local AS is 65000, local router ID 10.0.2.1
TTL is 1
Local TCP address is 10.2.2.9, local port is 179
Remote TCP address is 10.2.2.10, remote port is 40581
Auto-Local-Addr is disabled
Bfd is enabled and state is Up
Private AS numbers removed from outbound updates to this neighbor if only privat
TCP Socket Information:
  TCP state is ESTABLISHED
  Recv-Q: 0/32768
  Send-Q: 0/32768
  Outgoing Maximum Segment Size (MSS): 1448
  Total Number of TCP retransmissions: 1
  Options:
    Timestamps enabled: yes
    Selective Acknowledgments enabled: yes
    Window Scale enabled: yes
    Explicit Congestion Notification (ECN) enabled: no
  Socket Statistics:
    Window Scale (wscale): 7,7
    Retransmission Timeout (rto): 220.0ms
    Round-trip Time (rtt/rtvar): 17.8ms/0.8ms
    Delayed Ack Timeout (ato): 40.0ms
    Congestion Window (cwnd): 10
    TCP Throughput: 6.52 Mbps
    Recv Round-trip Time (rcv_rtt): 193806.4ms
    Advertised Recv Window (rcv_space): 65229

 ```
 </details>

 
 Так же проверим Route Table на каждом коммутаторе:
 
 <details>
 <summary> Route Table </summary>
   
 #### Spine 1
 ```

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 C        10.0.1.1/32 is directly connected, Loopback0
 B E      10.1.1.1/32 [200/0] via 10.2.1.2, Ethernet1
 B E      10.1.2.1/32 [200/0] via 10.2.1.6, Ethernet2
 B E      10.1.3.1/32 [200/0] via 10.2.1.10, Ethernet3
 C        10.2.1.0/30 is directly connected, Ethernet1
 C        10.2.1.4/30 is directly connected, Ethernet2
 C        10.2.1.8/30 is directly connected, Ethernet3
 B E      192.168.1.0/24 [200/0] via 10.2.1.2, Ethernet1
 B E      192.168.2.0/24 [200/0] via 10.2.1.6, Ethernet2
 B E      192.168.3.0/24 [200/0] via 10.2.1.10, Ethernet3
 B E      192.168.4.0/24 [200/0] via 10.2.1.10, Ethernet3


 ```

 #### Spine 2
 ```

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 C        10.0.2.1/32 is directly connected, Loopback0
 B E      10.1.1.1/32 [200/0] via 10.2.2.2, Ethernet1
 B E      10.1.2.1/32 [200/0] via 10.2.2.6, Ethernet2
 B E      10.1.3.1/32 [200/0] via 10.2.2.10, Ethernet3
 C        10.2.2.0/30 is directly connected, Ethernet1
 C        10.2.2.4/30 is directly connected, Ethernet2
 C        10.2.2.8/30 is directly connected, Ethernet3
 B E      192.168.1.0/24 [200/0] via 10.2.2.2, Ethernet1
 B E      192.168.2.0/24 [200/0] via 10.2.2.6, Ethernet2
 B E      192.168.3.0/24 [200/0] via 10.2.2.10, Ethernet3
 B E      192.168.4.0/24 [200/0] via 10.2.2.10, Ethernet3


 ```

 #### Leaf 1
 ```

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 B E      10.0.1.1/32 [200/0] via 10.2.1.1, Ethernet1
 B E      10.0.2.1/32 [200/0] via 10.2.2.1, Ethernet2
 C        10.1.1.1/32 is directly connected, Loopback0
 B E      10.1.2.1/32 [200/0] via 10.2.1.1, Ethernet1
                              via 10.2.2.1, Ethernet2
 B E      10.1.3.1/32 [200/0] via 10.2.1.1, Ethernet1
                              via 10.2.2.1, Ethernet2
 C        10.2.1.0/30 is directly connected, Ethernet1
 C        10.2.2.0/30 is directly connected, Ethernet2
 C        192.168.1.0/24 is directly connected, Ethernet3
 B E      192.168.2.0/24 [200/0] via 10.2.1.1, Ethernet1
                                 via 10.2.2.1, Ethernet2
 B E      192.168.3.0/24 [200/0] via 10.2.1.1, Ethernet1
                                 via 10.2.2.1, Ethernet2
 B E      192.168.4.0/24 [200/0] via 10.2.1.1, Ethernet1
                                 via 10.2.2.1, Ethernet2


 ```

 #### Leaf 2
 ```

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 B E      10.0.1.1/32 [200/0] via 10.2.1.5, Ethernet1
 B E      10.0.2.1/32 [200/0] via 10.2.2.5, Ethernet2
 B E      10.1.1.1/32 [200/0] via 10.2.1.5, Ethernet1
                              via 10.2.2.5, Ethernet2
 C        10.1.2.1/32 is directly connected, Loopback0
 B E      10.1.3.1/32 [200/0] via 10.2.1.5, Ethernet1
                              via 10.2.2.5, Ethernet2
 C        10.2.1.4/30 is directly connected, Ethernet1
 C        10.2.2.4/30 is directly connected, Ethernet2
 B E      192.168.1.0/24 [200/0] via 10.2.1.5, Ethernet1
                                 via 10.2.2.5, Ethernet2
 C        192.168.2.0/24 is directly connected, Ethernet3
 B E      192.168.3.0/24 [200/0] via 10.2.1.5, Ethernet1
                                 via 10.2.2.5, Ethernet2
 B E      192.168.4.0/24 [200/0] via 10.2.1.5, Ethernet1
                                 via 10.2.2.5, Ethernet2


 ```

 #### Leaf 3
 ```

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 B E      10.0.1.1/32 [200/0] via 10.2.1.9, Ethernet1
 B E      10.0.2.1/32 [200/0] via 10.2.2.9, Ethernet2
 B E      10.1.1.1/32 [200/0] via 10.2.1.9, Ethernet1
                              via 10.2.2.9, Ethernet2
 B E      10.1.2.1/32 [200/0] via 10.2.1.9, Ethernet1
                              via 10.2.2.9, Ethernet2
 C        10.1.3.1/32 is directly connected, Loopback0
 C        10.2.1.8/30 is directly connected, Ethernet1
 C        10.2.2.8/30 is directly connected, Ethernet2
 B E      192.168.1.0/24 [200/0] via 10.2.1.9, Ethernet1
                                 via 10.2.2.9, Ethernet2
 B E      192.168.2.0/24 [200/0] via 10.2.1.9, Ethernet1
                                 via 10.2.2.9, Ethernet2
 C        192.168.3.0/24 is directly connected, Ethernet3
 C        192.168.4.0/24 is directly connected, Ethernet4


 ```
</details>
Как видим, в таблицах маршрутизации присутствуют маршруты, полученные из протокола eBGP.</br>

Проверим состояние подключения лифов к спайнам

 #### Leaf 1
 ```
BGP summary information for VRF default
Router identifier 10.1.1.1, local AS number 65001
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.2.1.1         4  65000          14664     14667    0    0 00:26:11 Estab   6      6
  10.2.2.1         4  65000          14663     14671    0    0 00:26:09 Estab   6      6
 ```
 #### Leaf 2
 ```
BGP summary information for VRF default
Router identifier 10.1.2.1, local AS number 65002
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.2.1.5         4  65000          13691     13691    0    0 00:36:48 Estab   6      6
  10.2.2.5         4  65000          13691     13695    0    0 00:36:04 Estab   6      6
 ```
 #### Leaf 3
 ```
BGP summary information for VRF default
Router identifier 10.1.3.1, local AS number 65003
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.2.1.9         4  65000          13470     13471    0    0 00:37:13 Estab   5      5
  10.2.2.9         4  65000          13469     13472    0    0 00:36:29 Estab   5      5

 ```

Как видим, все соединения в состоянии established.


Так же посмотрим на работу протокола ECMP:

 #### Leaf 1
 ```
BGP routing table information for VRF default
Router identifier 10.1.1.1, local AS number 65001
Route status codes: * - valid, > - active, # - not installed, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

         Network                Next Hop            Metric  LocPref Weight  Path
 * >     10.0.1.1/32            10.2.1.1              0       100     0       65000 i
 * >     10.0.2.1/32            10.2.2.1              0       100     0       65000 i
 * >     10.1.1.1/32            -                     0       0       -       i
 * >Ec   10.1.2.1/32            10.2.1.1              0       100     0       65000 65002 i
 *  ec   10.1.2.1/32            10.2.2.1              0       100     0       65000 65002 i
 * >Ec   10.1.3.1/32            10.2.1.1              0       100     0       65000 65003 i
 *  ec   10.1.3.1/32            10.2.2.1              0       100     0       65000 65003 i
 * >     192.168.1.0/24         -                     1       0       -       i
 * >Ec   192.168.2.0/24         10.2.1.1              0       100     0       65000 65002 i
 *  ec   192.168.2.0/24         10.2.2.1              0       100     0       65000 65002 i
 * >Ec   192.168.3.0/24         10.2.1.1              0       100     0       65000 65003 i
 *  ec   192.168.3.0/24         10.2.2.1              0       100     0       65000 65003 i
 * >Ec   192.168.4.0/24         10.2.1.1              0       100     0       65000 65003 i
 *  ec   192.168.4.0/24         10.2.2.1              0       100     0       65000 65003 i

 ```

 #### Leaf 2
 ```
BGP routing table information for VRF default
Router identifier 10.1.2.1, local AS number 65002
Route status codes: * - valid, > - active, # - not installed, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

         Network                Next Hop            Metric  LocPref Weight  Path
 * >     10.0.1.1/32            10.2.1.5              0       100     0       65000 i
 * >     10.0.2.1/32            10.2.2.5              0       100     0       65000 i
 * >Ec   10.1.1.1/32            10.2.1.5              0       100     0       65000 65001 i
 *  ec   10.1.1.1/32            10.2.2.5              0       100     0       65000 65001 i
 * >     10.1.2.1/32            -                     0       0       -       i
 * >Ec   10.1.3.1/32            10.2.1.5              0       100     0       65000 65003 i
 *  ec   10.1.3.1/32            10.2.2.5              0       100     0       65000 65003 i
 * >Ec   192.168.1.0/24         10.2.1.5              0       100     0       65000 65001 i
 *  ec   192.168.1.0/24         10.2.2.5              0       100     0       65000 65001 i
 * >     192.168.2.0/24         -                     1       0       -       i
 * >Ec   192.168.3.0/24         10.2.1.5              0       100     0       65000 65003 i
 *  ec   192.168.3.0/24         10.2.2.5              0       100     0       65000 65003 i
 * >Ec   192.168.4.0/24         10.2.1.5              0       100     0       65000 65003 i
 *  ec   192.168.4.0/24         10.2.2.5              0       100     0       65000 65003 i

 ```

 #### Leaf 3
 ```
BGP routing table information for VRF default
Router identifier 10.1.3.1, local AS number 65003
Route status codes: * - valid, > - active, # - not installed, E - ECMP head, e P
                    S - Stale, c - Contributing to ECMP, b - backup, L - labelet
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Lp

         Network                Next Hop            Metric  LocPref Weight  Path
 * >     10.0.1.1/32            10.2.1.9              0       100     0       6i
 * >     10.0.2.1/32            10.2.2.9              0       100     0       6i
 * >Ec   10.1.1.1/32            10.2.1.9              0       100     0       6i
 *  ec   10.1.1.1/32            10.2.2.9              0       100     0       6i
 * >Ec   10.1.2.1/32            10.2.1.9              0       100     0       6i
 *  ec   10.1.2.1/32            10.2.2.9              0       100     0       6i
 * >     10.1.3.1/32            -                     0       0       -       i
 * >Ec   192.168.1.0/24         10.2.1.9              0       100     0       6i
 *  ec   192.168.1.0/24         10.2.2.9              0       100     0       6i
 * >Ec   192.168.2.0/24         10.2.1.9              0       100     0       6i
 *  ec   192.168.2.0/24         10.2.2.9              0       100     0       6i
 * >     192.168.3.0/24         -                     1       0       -       i
 * >     192.168.4.0/24         -                     1       0       -       i

 ```

Как видим, ECMP отрабатывает корректно.
