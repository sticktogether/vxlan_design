# VxLAN. Routing.

### Задачи:
Реализовать маршрутизацию между "клиентами" через EVPN route-type 5

### CLOS топология сети
![image](https://github.com/user-attachments/assets/be8b2962-2872-44f2-ae7c-c9417d5aae77)


### Настройка Routing

1. Подготавливаем второй ip-vrf инстанс. Создаем int vlan 40 в новом vrf с anycast gw. L3 VNI будет int vlan 200. Обновляем конфигурацию nve интерфейса. Разливаем конфиг на все лифы. Подключаем Client-40-1, Client-40-2. Названия vpn-инстансов Customer-1 и Client-1, чтобы не запутаться :) Vrf Customer-1 содержит клиентов vlan 10,20,30; vrf Client-1 содержит клиентов vlan 40.
> vrf context Client-1  
  vni 100200  
  rd auto  
  address-family ipv4 unicast  
    route-target both auto  
    route-target both auto evpn  
  
> vlan 40  
  name Client-1  
  vn-segment 10040  
interface Vlan40  
  no shutdown  
  vrf member Client-1  
  ip address 192.168.40.40/24  
  fabric forwarding mode anycast-gateway  
  
> interface Vlan200  
  no shutdown  
  mtu 9000  
  vrf member Client-1  
  ip forward  

> interface nve1
  member vni 10040  
    ingress-replication protocol bgp  
  member vni 100200 associate-vrf  

2. Подключаем к Leaf1 Border Router, на котором будет происходить обмен маршрутами между двумя ip-vrf. Выполняем конфигурацию bgp со стороны Leaf 1.

> router bgp 65001  
vrf CUSTOMER-1  
    address-family ipv4 unicast  
      redistribute direct route-map PERMIT-IPv4  
      redistribute hmm route-map PERMIT-IPv4  
    neighbor 10.100.101.2  
      remote-as 65100  
      address-family ipv4 unicast  
        send-community  
        send-community extended  
  vrf Client-1  
    address-family ipv4 unicast  
      redistribute direct route-map PERMIT-IPv4  
      redistribute hmm route-map PERMIT-IPv4  
    neighbor 10.200.201.2  
      remote-as 65100  
      address-family ipv4 unicast  
        send-community  
        send-community extended  

3. Конфигурируем Border Router, дополнительно анонсируем лупбек 8.8.8.8 (типа интернет, все дела).
> interface Loopback0  
 ip address 10.10.10.10 255.255.255.255  
!  
interface Loopback1  
 ip address 8.8.8.8 255.255.255.255  
!  
interface GigabitEthernet0/0  
 no ip address  
 duplex auto  
 speed auto  
 media-type rj45  
!  
interface GigabitEthernet0/0.101  
 encapsulation dot1Q 101  
 ip address 10.100.101.2 255.255.255.252  
!  
interface GigabitEthernet0/0.201  
 encapsulation dot1Q 201  
 ip address 10.200.201.2 255.255.255.252  

> router bgp 65100  
 bgp router-id 10.10.10.10  
 bgp log-neighbor-changes  
 neighbor 10.100.101.1 remote-as 65001  
 neighbor 10.200.201.1 remote-as 65001  
 !  
 address-family ipv4  
  redistribute connected route-map REDIS_INET  
  neighbor 10.100.101.1 activate  
  neighbor 10.200.201.1 activate  
 exit-address-family  

4. Смотрим полученные маршруты на Leaf1. Замечаем, что ничего, кроме connected сетей Border Leaf в таблице маршрутизации нет. Исправляем, добавляя опцию as-override для bgp соседей.
> router bgp 65100  
 address-family ipv4  
  neighbor 10.200.201.1 as-override  
  neighbor 10.100.101.1 as-override  

5. Получили все необходимые маршруты через type-5 evpn route. Проверяем связность между разными vrf.

### Leaf1 mac table
![image](https://github.com/user-attachments/assets/edf8cfc1-17d7-48d0-b707-932f7bd57b63)

### Leaf1 bgp vrf peers
![image](https://github.com/user-attachments/assets/1dc3de4c-0a06-4f65-9e1e-f32d2e494b82)

### Leaf1 ip route vrf Customer-1
![image](https://github.com/user-attachments/assets/8872f52f-97eb-4e0e-a7d7-ec0084321432)

### Leaf1 ip route vrf Client-1
![image](https://github.com/user-attachments/assets/2b78ab72-5a33-4aa2-870e-e4de43f00574)


### Leaf2 mac table
![image](https://github.com/user-attachments/assets/1b269193-3de6-4c80-865a-bd9ffe22ea6d)


### Leaf2 evpn routes
![image](https://github.com/user-attachments/assets/091f5ab5-9807-46ab-852f-a4bfbdb0ce2b)
![image](https://github.com/user-attachments/assets/6692e175-5497-4a63-923c-96f71be13ee0)
![image](https://github.com/user-attachments/assets/616e5eec-11e0-4d66-8620-9b16d8110fcc)
![image](https://github.com/user-attachments/assets/13b18ee1-e6bd-41d5-a989-71cd3e68fd58)
![image](https://github.com/user-attachments/assets/1393fcc4-2633-4708-8f3a-29426bb5e208)


### Leaf3 ip route vrf Customer-1
![image](https://github.com/user-attachments/assets/9c3aa4e9-3bcf-4b3c-bb9b-0036a855e6a6)

### BorderRouter ip route
![image](https://github.com/user-attachments/assets/0fefe34c-2dcf-4346-8778-8ac89e95a878)

### Ping Customer-30-1 -> Client-40-1
![image](https://github.com/user-attachments/assets/60437a72-a1e8-431a-bff0-024cca639fec)

### Ping Client-40-2 -> Customer-10-1/ Customer-10-2/ Customer-10-3/ Customer-20-1/ Customer-20-2
![image](https://github.com/user-attachments/assets/2abf584c-9369-4de1-845d-02bfd46cf4b8)



