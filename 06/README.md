VxLAN. L3 VNI
=====================================

### Цель: 

- Настроить Overlay на основе VxLAN EVPN для L3 связанности между клиентами.

### Описание/Пошаговая инструкция выполнения домашнего задания:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

- Настроите BGP peering между Leaf и Spine в AF l3vpn evpn
- Настроите связанность между клиентами в первой зоне и убедитесь в её наличии
- Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств

### Топология сети

![scheme_addr.png](scheme_addr3.png)

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
Client1|eth0|192.168.10.101|255.255.255.0
Client2|eth0|192.168.20.102|255.255.255.0
Client3|eth0|192.168.10.103|255.255.255.0
Client4|eth0|192.168.20.104|255.255.255.0


<details>

<summary> Общая информация </summary>

Virtual Extensible LAN (VXLAN) является технологией сетевой виртуализации, созданной для решения проблем масштабируемости в больших системах облачных вычислений. Она использует схожую с VLAN технику для MAC инкапсуляции Layer 2 Ethernet кадров в UDP-пакеты, порт 4789.</br>
VXLAN является развитием усилий по стандартизации на оверлейном протоколе инкапсуляции. Он увеличивает масштабируемость до 16 миллионов логических сетей и позволяет сетям 2 уровня одновременно сосуществовать по IP-сетям. При этом multicast или unicast (с Head-End Replication) используются для передачи широковещательного трафика (broadcast, multicast и Unicast flood).</br>
Принцип работы:
VXLAN устанавливает логический туннель между устройствами источника и назначения. Процесс инкапсуляции: 

 - VXLAN инкапсулирует оригинальные Ethernet-фреймы, отправленные виртуальной машиной, в UDP-пакеты.
 - Затем инкапсулирует UDP-пакеты с заголовком IP и заголовком Ethernet физической сети в качестве внешних заголовков, что позволяет передавать эти пакеты по сети, как обычные IP-пакеты.</br>
Упаковка и распаковка пакетов производятся конечными устройствами туннеля VXLAN (VTEP).</br>

Пакет протокола VXLAN состоит из следующих компонентов: 

 - Заголовок Ethernet — включает MAC-адрес отправителя, MAC-адрес получателя и тип протокола (например, IPv4, IPv6).
 - Заголовок IP — обеспечивает маршрутизацию пакета через IP-сеть. Поля: IP-адрес отправителя, IP-адрес получателя, другие поля (версия протокола, длина заголовка и т. д.).
 - Заголовок UDP — обеспечивает транспортировку инкапсулированных Ethernet-кадров. Поля: порт отправителя, порт получателя, длина UDP-пакета, контрольная сумма.
 - Заголовок VXLAN — состоит из полей: Flags (8 бит), Reserved1 (24 бит), VXLAN Network Identifier (VNI) (24 бит), Reserved2 (8 бит).
 - Внутренний Ethernet-кадр — содержит оригинальные MAC-адреса отправителя и получателя, а также полезную нагрузку данных.

EVPN (Ethernet Virtual Private Network) — технология, которая обеспечивает связь уровней 2 и 3 между различными сетевыми сегментами.</br>
Некоторые особенности EVPN:

 - Поддержка передачи трафика второго уровня по множественным путям. Для этого достаточно указать точку назначения туннеля в IP заголовке внешнего пакета, и у транзитных коммутаторов появляется возможность использовать все возможные пути.
 - Снижение уровня широковещательного трафика. Коммутаторы EVPN используют программный метод обучения MAC-адресов на базе обмена BGP сообщениями. После появления нового MAC-адреса на порту доступа выполняется синхронизация таблиц коммутации по всей сети.
 - Маршрутизация между сетями в Active-Active режиме. Устройства ведут себя как распределённый маршрутизатор, каждая часть которого имеет один и тот же IP и MAC-адреса.
 - Механизмы обмена не только MAC, но и IP информацией. Поэтому табличные данные и поведение распределённого маршрутизатора будут идентичны на всех его компонентах.
</details>

### Выполнение:


Произведем начальную настройку коммутаторов, в которой выполним команды конфигурирования адресного пространства, а так же настроим протокол динамической маршрутизации eBGP для обеспечения связаности лифов, которые будут анонсировать свой loopback. Первый и третий клиенты у нас находятся в сети 192.168.10.0/24, а второй и четвертый в сети 192.168.10.0/24:
<details>

<summary> Начальная настройка </summary>
  
#### Spine 1
```
hostname Spine1
!
spanning-tree mode mstp
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
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.0.1.1/32
!
interface Management1
!
ip routing
!
peer-filter LEAF_PF
   10 match as-range 65001-65003 result accept
!
router bgp 65000
   router-id 10.0.1.1
   maximum-paths 3 ecmp 3
   bgp listen range 10.2.1.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 3 9
   !
   address-family ipv4
      neighbor LEAF_NEIGHBOR activate
      network 10.0.1.1/32
!
```
#### Spine 2
```
hostname Spine2
!
spanning-tree mode mstp
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
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.0.2.1/32
!
interface Management1
!
ip routing
!
peer-filter LEAF_PF
   10 match as-range 65001-65003 result accept
!
router bgp 65000
   router-id 10.0.2.1
   maximum-paths 3 ecmp 3
   bgp listen range 10.2.2.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 3 9
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor 10.2.1.1 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.1 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.1.1/32
!
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor 10.2.1.5 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.5 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.2.1/32
!
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
!
interface Ethernet4
   mtu 9000
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor 10.2.1.9 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.9 peer group SPINE_NEIGHBOR
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.3.1/32
!
```
#### Client 1
```
VPCS> ip 192.168.10.101 255.255.255.0 192.168.1.1
```
#### Client 2
```
VPCS> ip 192.168.20.102 255.255.255.0 192.168.2.1
```
#### Client 3
```
VPCS> ip 192.168.10.103 255.255.255.0 192.168.3.1
```
#### Client 4
```
VPCS> ip 192.168.20.104 255.255.255.0 192.168.4.1
```
</details>

Нам необходимо настроить сеть таким образом, чтобы клиенты находились в разных L2 сегментах, и клиенты в одном VLAN видели друг друга и могли передавать траффик. 

Использовать будем VLAN-Based метод, так как он самый наглядный.

Определим Client 1 и Client 3 во влан VLAN 10, в Client 2 и Client 4 во VLAN 20. Для этого определи назначенные порты коммутатора в соответствующие VLAN:

```
switchport access vlan 10
switchport access vlan 20
```

Теперь настроим интерфейс VXLAN. Здесь мы указываем, что VTEP Source IP-адрес нужно брать с интерфейса Loopback0. Этот IP-адрес будет использоваться как внешний Source IP в VXLAN-пакетах. Далее мы задаем соответствие VLAN 10 к VNI 10010, а VLAN 20 к VNI 10020. Трафик из VLAN при выходе из VXLAN-интерфейса должен инкапсулироваться в VNI и наоборот, при получении пакета из overlay-сети, мы декапсулируем его из VXLAN и направляем в порты VLAN'ов. Так же блокируем data-plane обучение VTEP от любых IP-адресов, кроме тех что разрешены EVPN control-plane (BGP).
```
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan ХХ vni 100ХХ
   vxlan learn-restrict any
!
```

Отдельных нейборов для BGP-сессий для EVPN, в отличии от IPv4-BGP-сессий для underlay, где нейборы были определены по адресам интерфейсов линковочных сетей, мы прописывать будем исходя из loopback интерфейсов коммутаnоров(будем использовать peer группы). Здесь же разрешим отсылку расширенных комьюнити:
```
   neighbor ХХХ_NEIGHBOR_VXLAN update-source Loopback0
   neighbor ХХХ_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor ХХХ_NEIGHBOR_VXLAN send-community extended
```

Теперьв глобальных настройках включим режим multi-agent, а так же включим AF EVPN в настройках процесса BGP:
```
service routing protocols model multi-agent
   address-family evpn
      neighbor ХХХ_NEIGHBOR_VXLAN activate
   !
```

Далее настроим описание VLANы в протоколе eBGP.
Значение RD (Route Distinguisher) нужно установить для того, чтобы сделать уникальными маршруты к одному и тому же префиксу, но которые принадлежат разным VLANам. Политика экспорта из VLAN на анонсирующем VTEP должна совпадать с политикой импорта в VLAN назначения на принимающем VTEP. Зададим route-target как хх:100хх (как на импорт, так и на экспорт). Так же настроим распространение в EVPN информацию о всех изученных локально хостах в данных VLANах.:
```
  vlan ХХ
      rd auto
      route-target both ХХ:100ХХ
      redistribute learned
   !
```

Таким образом, итоговые конфигурации коммутаторов будут выглядеть так:

<details>
<summary> Итоговая конфигурация </summary>
  
#### Spine 1
```
service routing protocols model multi-agent
!
hostname Spine1
!
spanning-tree mode mstp
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
   maximum-paths 3 ecmp 3
   bgp listen range 10.2.1.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   bgp listen range 10.1.0.0/22 peer-group LEAF_NEIGHBOR_VXLAN peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 3 9
   neighbor LEAF_NEIGHBOR_VXLAN peer group
   neighbor LEAF_NEIGHBOR_VXLAN next-hop-unchanged
   neighbor LEAF_NEIGHBOR_VXLAN update-source Loopback0
   neighbor LEAF_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor LEAF_NEIGHBOR_VXLAN send-community extended
   !
   address-family evpn
      neighbor LEAF_NEIGHBOR_VXLAN activate
   !
   address-family ipv4
      neighbor LEAF_NEIGHBOR activate
      network 10.0.1.1/32
!
end

```
  
#### Spine 2
```
service routing protocols model multi-agent
!
hostname Spine2
!
spanning-tree mode mstp
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
   maximum-paths 3 ecmp 3
   bgp listen range 10.2.2.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   bgp listen range 10.1.0.0/22 peer-group LEAF_NEIGHBOR_VXLAN peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 3 9
   neighbor LEAF_NEIGHBOR_VXLAN peer group
   neighbor LEAF_NEIGHBOR_VXLAN next-hop-unchanged
   neighbor LEAF_NEIGHBOR_VXLAN update-source Loopback0
   neighbor LEAF_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor LEAF_NEIGHBOR_VXLAN send-community extended
   !
   address-family evpn
      neighbor LEAF_NEIGHBOR_VXLAN activate
   !
   address-family ipv4
      neighbor LEAF_NEIGHBOR activate
      network 10.0.2.1/32
!
end


```
  
#### Leaf 1
```

service routing protocols model multi-agent
!
hostname Leaf1
!
spanning-tree mode mstp
!
vlan 10
   name L2_NET1
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
   switchport access vlan 10
   !
!
interface Loopback0
   ip address 10.1.1.1/32
!
interface Vlan10
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan learn-restrict any
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor SPINE_NEIGHBOR_VXLAN peer group
   neighbor SPINE_NEIGHBOR_VXLAN remote-as 65000
   neighbor SPINE_NEIGHBOR_VXLAN update-source Loopback0
   neighbor SPINE_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor SPINE_NEIGHBOR_VXLAN send-community extended
   neighbor 10.0.1.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.0.2.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.2.1.1 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.1 peer group SPINE_NEIGHBOR
   !
   vlan 10
      rd auto
      route-target both 10:10010
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_NEIGHBOR_VXLAN activate
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.1.1/32
!
end


```

#### Leaf 2
```

service routing protocols model multi-agent
!
hostname Leaf2
!
spanning-tree mode mstp
!
vlan 20
   name L2_NET2
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
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.2.1/32
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 20 vni 10020
   vxlan learn-restrict any
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor SPINE_NEIGHBOR_VXLAN peer group
   neighbor SPINE_NEIGHBOR_VXLAN remote-as 65000
   neighbor SPINE_NEIGHBOR_VXLAN update-source Loopback0
   neighbor SPINE_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor SPINE_NEIGHBOR_VXLAN send-community extended
   neighbor 10.0.1.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.0.2.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.2.1.5 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.5 peer group SPINE_NEIGHBOR
   !
   vlan 20
      rd auto
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_NEIGHBOR_VXLAN activate
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.2.1/32
!
end

```

#### Leaf 3
```
service routing protocols model multi-agent
!
hostname Leaf3
!
spanning-tree mode mstp
!
vlan 10
   name L2_NET1
!
vlan 20
   name L2_NET2
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
   switchport access vlan 10
!
interface Ethernet4
   mtu 9000
   switchport access vlan 20
!
interface Loopback0
   ip address 10.1.3.1/32
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan learn-restrict any
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
   neighbor SPINE_NEIGHBOR timers 3 9
   neighbor SPINE_NEIGHBOR_VXLAN peer group
   neighbor SPINE_NEIGHBOR_VXLAN remote-as 65000
   neighbor SPINE_NEIGHBOR_VXLAN update-source Loopback0
   neighbor SPINE_NEIGHBOR_VXLAN ebgp-multihop 3
   neighbor SPINE_NEIGHBOR_VXLAN send-community extended
   neighbor 10.0.1.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.0.2.1 peer group SPINE_NEIGHBOR_VXLAN
   neighbor 10.2.1.9 peer group SPINE_NEIGHBOR
   neighbor 10.2.2.9 peer group SPINE_NEIGHBOR
   !
   vlan 10
      rd auto
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_NEIGHBOR_VXLAN activate
   !
   address-family ipv4
      neighbor SPINE_NEIGHBOR activate
      network 10.1.3.1/32
!
end

```
</details>

После настройки на сетевых устройствах протокола маршрутизации проверим результаты.

 Пробуем с Client1 "достучаться" до Client3(VLA10),а с Client2 до Client4(VLAN20):
 #### Clients
 ![pingl2cl1.png](pingl2cl1.png)
 ![pingl2cl2.png](pingl2cl2.png)
 
 Как видим Client1 видит Client3, а Client2 видит Client4. Так же они не видят других клиентов, находящихся в другом L2 сегменте.


 Далее посмотрим eBGP соседей на спайнах, а так же состояние сессий:
<details>
<summary> Neighbors </summary>
  
 #### Spine 1
 ```

Spine1#sh bgp sum
BGP summary information for VRF default
Router identifier 10.0.1.1, local AS number 65000
Neighbor           AS Session State AFI/SAFI                AFI/SAFI State   NLRI Rcd   NLRI Acc
--------- ----------- ------------- ----------------------- -------------- ---------- ----------
10.1.1.1        65001 Established   IPv4 Unicast            Negotiated              1          1
10.1.1.1        65001 Established   L2VPN EVPN              Negotiated              1          1
10.1.2.1        65002 Established   IPv4 Unicast            Negotiated              1          1
10.1.2.1        65002 Established   L2VPN EVPN              Negotiated              1          1
10.1.3.1        65003 Established   IPv4 Unicast            Negotiated              1          1
10.1.3.1        65003 Established   L2VPN EVPN              Negotiated              2          2
10.2.1.2        65001 Established   IPv4 Unicast            Negotiated              1          1
10.2.1.6        65002 Established   IPv4 Unicast            Negotiated              1          1
10.2.1.10       65003 Established   IPv4 Unicast            Negotiated              1          1

 ```
 #### Spine 2
 ```
Spine2#sh bgp sum
BGP summary information for VRF default
Router identifier 10.0.2.1, local AS number 65000
Neighbor           AS Session State AFI/SAFI                AFI/SAFI State   NLRI Rcd   NLRI Acc
--------- ----------- ------------- ----------------------- -------------- ---------- ----------
10.1.1.1        65001 Established   IPv4 Unicast            Negotiated              1          1
10.1.1.1        65001 Established   L2VPN EVPN              Negotiated              1          1
10.1.2.1        65002 Established   IPv4 Unicast            Negotiated              1          1
10.1.2.1        65002 Established   L2VPN EVPN              Negotiated              1          1
10.1.3.1        65003 Established   IPv4 Unicast            Negotiated              1          1
10.1.3.1        65003 Established   L2VPN EVPN              Negotiated              2          2
10.2.2.2        65001 Established   IPv4 Unicast            Negotiated              1          1
10.2.2.6        65002 Established   IPv4 Unicast            Negotiated              1          1
10.2.2.10       65003 Established   IPv4 Unicast            Negotiated              1          1

 ```
 </details>

Как видим EVPN соседи активны.
 
 Так же посмотрим EVPN маршруты типа 2 и 3 на каждом leaf-коммутаторе:
 
 <details>
 <summary> Type 2,3 </summary>
 

 #### Leaf 1
 ```

Leaf1#sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.1.1.1, local AS number 65001
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.1.1.1:10 mac-ip 0050.7966.6806
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.2.1:20 mac-ip 0050.7966.6807
                                 10.1.2.1              -       100     0       65000 65002 i
 *  ec    RD: 10.1.2.1:20 mac-ip 0050.7966.6807
                                 10.1.2.1              -       100     0       65000 65002 i
 * >Ec    RD: 10.1.3.1:10 mac-ip 0050.7966.6808
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:10 mac-ip 0050.7966.6808
                                 10.1.3.1              -       100     0       65000 65003 i
 * >Ec    RD: 10.1.3.1:20 mac-ip 0050.7966.6809
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:20 mac-ip 0050.7966.6809
                                 10.1.3.1              -       100     0       65000 65003 i
 * >      RD: 10.1.1.1:10 imet 10.1.1.1
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.2.1:20 imet 10.1.2.1
                                 10.1.2.1              -       100     0       65000 65002 i
 *  ec    RD: 10.1.2.1:20 imet 10.1.2.1
                                 10.1.2.1              -       100     0       65000 65002 i
 * >Ec    RD: 10.1.3.1:10 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:10 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 * >Ec    RD: 10.1.3.1:20 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:20 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
Leaf1#

 ```

 #### Leaf 2
 ```

Leaf2#sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.1.2.1, local AS number 65002
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.1.1.1:10 mac-ip 0050.7966.6806
                                 10.1.1.1              -       100     0       65000 65001 i
 *  ec    RD: 10.1.1.1:10 mac-ip 0050.7966.6806
                                 10.1.1.1              -       100     0       65000 65001 i
 * >      RD: 10.1.2.1:20 mac-ip 0050.7966.6807
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.3.1:10 mac-ip 0050.7966.6808
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:10 mac-ip 0050.7966.6808
                                 10.1.3.1              -       100     0       65000 65003 i
 * >Ec    RD: 10.1.3.1:20 mac-ip 0050.7966.6809
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:20 mac-ip 0050.7966.6809
                                 10.1.3.1              -       100     0       65000 65003 i
 * >Ec    RD: 10.1.1.1:10 imet 10.1.1.1
                                 10.1.1.1              -       100     0       65000 65001 i
 *  ec    RD: 10.1.1.1:10 imet 10.1.1.1
                                 10.1.1.1              -       100     0       65000 65001 i
 * >      RD: 10.1.2.1:20 imet 10.1.2.1
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.3.1:10 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:10 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 * >Ec    RD: 10.1.3.1:20 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i
 *  ec    RD: 10.1.3.1:20 imet 10.1.3.1
                                 10.1.3.1              -       100     0       65000 65003 i

 ```

 #### Leaf 3
 ```

Leaf3#sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.1.3.1, local AS number 65003
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >Ec    RD: 10.1.1.1:10 mac-ip 0050.7966.6806
                                 10.1.1.1              -       100     0       65000 65001 i
 *  ec    RD: 10.1.1.1:10 mac-ip 0050.7966.6806
                                 10.1.1.1              -       100     0       65000 65001 i
 * >Ec    RD: 10.1.2.1:20 mac-ip 0050.7966.6807
                                 10.1.2.1              -       100     0       65000 65002 i
 *  ec    RD: 10.1.2.1:20 mac-ip 0050.7966.6807
                                 10.1.2.1              -       100     0       65000 65002 i
 * >      RD: 10.1.3.1:10 mac-ip 0050.7966.6808
                                 -                     -       -       0       i
 * >      RD: 10.1.3.1:20 mac-ip 0050.7966.6809
                                 -                     -       -       0       i
 * >Ec    RD: 10.1.1.1:10 imet 10.1.1.1
                                 10.1.1.1              -       100     0       65000 65001 i
 *  ec    RD: 10.1.1.1:10 imet 10.1.1.1
                                 10.1.1.1              -       100     0       65000 65001 i
 * >Ec    RD: 10.1.2.1:20 imet 10.1.2.1
                                 10.1.2.1              -       100     0       65000 65002 i
 *  ec    RD: 10.1.2.1:20 imet 10.1.2.1
                                 10.1.2.1              -       100     0       65000 65002 i
 * >      RD: 10.1.3.1:10 imet 10.1.3.1
                                 -                     -       -       0       i
 * >      RD: 10.1.3.1:20 imet 10.1.3.1
                                 -                     -       -       0       i

 ```
</details>
Как видим, достигается полная связность с использованием протокола ECMP: любой хост → любой хост по VXLAN.</br>
</br>
Проверим таблицы мак адресов:

 #### Leaf 1
 ```
Leaf1#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  10    0050.7966.6806    DYNAMIC     Et3        1       0:04:37 ago
  10    0050.7966.6808    DYNAMIC     Vx1        1       0:04:32 ago
Total Mac Addresses for this criterion: 2

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0

 ```
 #### Leaf 2
 ```
Leaf2#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  20    0050.7966.6807    DYNAMIC     Et3        1       0:04:52 ago
  20    0050.7966.6809    DYNAMIC     Vx1        1       0:04:47 ago
Total Mac Addresses for this criterion: 2

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0

 ```
 #### Leaf 3
 ```
Leaf3#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  10    0050.7966.6806    DYNAMIC     Vx1        1       0:05:08 ago
  10    0050.7966.6808    DYNAMIC     Et3        1       0:05:05 ago
  20    0050.7966.6807    DYNAMIC     Vx1        1       0:05:06 ago
  20    0050.7966.6809    DYNAMIC     Et4        1       0:05:03 ago
Total Mac Addresses for this criterion: 4

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0


 ```

Как видим, все mac адреса корректно изучаются на всех лифах.
