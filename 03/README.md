Проектирование адресного пространства
=====================================

### Цель: 

- Собрать схему CLOS
- Распределить адресное пространство

### Настроить IS-IS для Underlay сети
### Описание/Пошаговая инструкция выполнения домашнего задания:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

- Настроите IS-IS в Underlay сети, для IP связанности между всеми сетевыми устройствами.
- Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств.
- Убедитесь в наличии IP связанности между устройствами в IS-IS домене.

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

Протокол маршрутизации промежуточных систем (англ. IS-IS) — протокол динамической маршрутизации, основанный на технологии отслеживания состояния канала (link-state technology) и использующий для нахождения кратчайшего пути алгоритм Дейкстры.</br>
Протокол IS-IS представляет собой протокол внутреннего шлюза (Interior Gateway Protocol — IGP), стандартизированный ISO и использующийся в основном в крупных сетях провайдеров услуг. IS-IS может также использоваться в корпоративных сетях особо крупного масштаба.</br>
IS-IS же работает поверх канального уровня модели OSI[2], поэтому он не привязан к конкретному протоколу сетевого уровня. Также IS-IS не использует протокол IP для доставки сообщений, содержащих информацию о маршрутизации.</br>

OSPF имеет следующие преимущества:

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

Нам необходимо настроить сеть таким образом, чтобы Клиенты видели друг друга и могли передавать траффик. Как и в предыдущей лабораторной работе, предположим, что нам необходимо защитить паролем линк от Leaf 1 до Spine 1 с помощью аутентификации, для того чтобы исключить возможность подключению сторонних сетевых устройств. 

В данной лабораторной работе будем использовать протокол динамической маршрутизации IS-IS.

Протокол IS-IS является мультизональным протоколом. Однако в данном случае ограничимся одной зоной, в которую поместим все коммутаторы, так как разделения на зоны здесь является избыточным.
```
   ip ospf area 0.0.0.0
```

Протокол IS-IS является протоколом типа Link-State с выбором сетевого устройства с ролью DIS,  который генерирует pseudo-node LSP(виртуальные роутер) для оптимизации топологии и сокращения флуда LSP. Поскольку наша зона делится на сегменты сети состоящих из p2p линков, мы можем обойтись без DIS. Обозначим нашу сеть как point-to-point.
```
   ip ospf network point-to-point
```

Значения параметров hello-interval и hello-multiplier оставим по умолчанию(для избежания лишнего hello флуда), а для быстрого обнаружения проблем на линках включим протокол BFD.
```
  ip ospf neighbor bfd
```

На оконечных сетевых интерфейсах линка между Spine1 и Leaf1 настроем аутентифиукацмю по паролю.
```
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 f0x3GerlAiU=
```

Поскольку маршрутизаторы находятся в одной зоне и имеют полную связанность, установим между ними соседство уровня 1 (L1).

Отфильтруем IP активных интерфейсов и все не используемые IP TLV. Так же включим защиту от спуфинга.

Таким образом, итоговые конфигурации коммуторов будут выглядеть так:

<details>
#### Leaf 1
```
interface Ethernet1
   no switchport
   mtu 1500
   ip address 10.2.1.2/30
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.0
   ip ospf message-digest-key 1 md5 7 f0x3GerlAiU=
!
interface Ethernet2
   no switchport
   mtu 1500
   ip address 10.2.2.2/30
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   no switchport
   mtu 1500
   ip address 192.168.1.1/24
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Loopback0
   ip address 10.1.1.1/32
   ip ospf area 0.0.0.0
!
ip routing
!
router ospf 1
   router-id 10.1.1.1
   auto-cost reference-bandwidth 40000
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   log-adjacency-changes
   max-lsa 12000
!
```

</details>

После настройки на сетевых устройствах протокола маршрутизации проверим результаты.
 Пробуем с Client1 "достучаться" до Client2, Client3 и Client4:

 Как видим Client1 видит других клиентов.

 Далее посмотрим OSPF соседей на спайнах:

 Так же проверим Route Table на каждом коммутаторе:
