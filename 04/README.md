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

BGP (англ. Border Gateway Protocol, протокол граничного шлюза) — протокол динамической маршрутизации. Относится к классу протоколов маршрутизации внешнего шлюза (англ. EGP — Exterior Gateway Protocol).</br>
Протокол BGP предназначен для обмена информацией о достижимости подсетей между автономными системами (АС, англ. AS — autonomous system), то есть группами маршрутизаторов под единым техническим и административным управлением, использующими протокол внутридоменной маршрутизации для определения маршрутов внутри себя и протокол междоменной маршрутизации для определения маршрутов доставки пакетов в другие АС. Передаваемая информация включает в себя список АС, к которым имеется доступ через данную систему. Выбор наилучших маршрутов осуществляется исходя из правил, принятых в сети.</br>
BGP является протоколом прикладного уровня и функционирует поверх протокола транспортного уровня TCP (порт 179). После установки соединения передаётся информация обо всех маршрутах, предназначенных для экспорта. В дальнейшем передаётся только информация об изменениях в таблицах маршрутизации. При закрытии соединения удаляются все маршруты, информация о которых передана противоположной стороной.</br>
Когда протокол BGP используется между двумя узлами в одной автономной системе (AS), он называется внутренним BGP (iBGP или внутренний протокол пограничного шлюза). Когда он используется между разными автономными системами, он называется внешним BGP (eBGP или внешний протокол пограничного шлюза).</br>

BGP имеет следующие преимущества:

- Высокая скорость сходимости по сравнению с дистанционно-векторными протоколами маршрутизации;
- Поддержка сетевых масок переменной длины (VLSM);
- Оптимальное использование пропускной способности с построением дерева кратчайших путей.
- Быстрая сходимость и отличная масштабируемость. 

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

Нам необходимо настроить сеть таким образом, чтобы Клиенты видели друг друга и могли передавать траффик. 

Для выполнения лабораторной работы выберем eBGP, так как этот протокол более легко масштабируется.

Протокол IS-IS является мультизональным протоколом. Однако в данном случае ограничимся одной зоной, в которую поместим все коммутаторы, так как разделения на зоны здесь является избыточным. Поскольку маршрутизаторы находятся в одной зоне и имеют полную связанность, установим между ними соседство уровня 1 (L1).
```

```

Протокол IS-IS является протоколом типа Link-State с выбором сетевого устройства с ролью DIS,  который генерирует pseudo-node LSP(виртуальные роутер) для оптимизации топологии и сокращения флуда LSP. Поскольку наша зона делится на сегменты сети состоящих из p2p линков, мы можем обойтись без DIS. Обозначим нашу сеть как point-to-point.
```

```

Значения параметров hello-interval и hello-multiplier оставим по умолчанию(для избежания лишнего hello флуда), а для быстрого обнаружения проблем на линках включим протокол BFD.
```

```

На оконечных сетевых интерфейсах Leaf1 настроем аутентифиукацмю по паролю.
```

```

Отфильтруем IP активных интерфейсов и все не используемые IP TLV. Так же включим защиту от спуфинга.
```

```

Таким образом, итоговые конфигурации коммуторов будут выглядеть так:

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
   maximum-paths 2 ecmp 2
   bgp listen range 10.2.1.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 1 3
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
   maximum-paths 2 ecmp 2
   bgp listen range 10.2.2.0/28 peer-group LEAF_NEIGHBOR peer-filter LEAF_PF
   neighbor LEAF_NEIGHBOR peer group
   neighbor LEAF_NEIGHBOR out-delay 0
   neighbor LEAF_NEIGHBOR bfd
   neighbor LEAF_NEIGHBOR timers 1 3
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
   neighbor SPINE_NEIGHBOR timers 1 3
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
   neighbor SPINE_NEIGHBOR timers 1 3
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
   neighbor SPINE_NEIGHBOR timers 1 3
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
 #### Client 2
 ![ping2.png](ping2.png)
 
 #### Client 3
 ![ping3.png](ping3.png)
 
 #### Client 4
 ![ping4.png](ping4.png)
 Как видим Client1 видит других клиентов.

 Далее посмотрим eBGP соседей на спайнах:
 #### Spine 1
 ```

 ```
#### Spine 1
 ```

 ```

 Так же проверим Route Table на каждом коммутаторе:
 
 <details>
 <summary> Route Table </summary>
   
 #### Spine 1
 ```

 ```

 #### Spine 2
 ```

 ```

 #### Leaf 1
 ```

 ```

 #### Leaf 2
 ```

 ```

 #### Leaf 3
 ```

 ```
</details>
Как видим, в таблицах маршрутизации присутствуют маршруты, полученные из протокола eBGP.

Так же посмотрим на базу данных протокола:

```

```
