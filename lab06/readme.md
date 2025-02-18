# VxLAN. L3 VNI

### Задачи:
Настроить маршрутизацию в рамках Overlay между клиентами

### CLOS топология сети
![image](https://github.com/user-attachments/assets/70164b6d-ef30-4a6e-9d35-8e6abeb6a26e)

### Настройка L3 EVPN

1. Включаем фичи для поддержки int vlan и anycast gw.

> feature interface-vlan  
feature vn-segment-vlan-based  

2. Настраиваем второй пользовательский vlan и мапим к нему vni.
> vlan 20  
  name CUSTOMER-2  
  vn-segment 10020  

3. Настраиваем vlan для роутинга между vni.
> vlan 100  
  name L3VNI  
  vn-segment 100100  

4. Настраиваем vrf.
> vrf context CUSTOMER-1  
  vni 100100  
  rd auto  
  address-family ipv4 unicast  
    route-target both auto  
    route-target both auto evpn  

5. Настраиваем anycast gw mac-address.
> fabric forwarding anycast-gateway-mac 0000.0000.1234  

6. Настраиваем гейтвеи для вланов, добавляем anycast gw.
> interface Vlan10  
  no shutdown  
  vrf member CUSTOMER-1  
  ip address 192.168.10.10/24  
  fabric forwarding mode anycast-gateway  
  
> interface Vlan20  
  no shutdown  
  vrf member CUSTOMER-1  
  ip address 192.168.20.20/24  
  fabric forwarding mode anycast-gateway  
  
> interface Vlan100  
  mtu 9000  
  vrf member CUSTOMER-1  
  ip forward  
  
7. Настраиваем nve интерфейс, объявляем новые вланы.
  >  interface nve1  
  no shutdown  
  host-reachability protocol bgp  
  source-interface loopback1  
  member vni 10010  
    ingress-replication protocol bgp  
  member vni 10020  
    ingress-replication protocol bgp  
  member vni 100100 associate-vrf  
   
7. Пингуем устройства в разных вланах за разными лифами, чтобы проверить работоспособность L3 VNI (т.к. при пинге в пределах одногои лифа vxlan роутинг работать не будет. будет обычный роутинг).

### Leaf1 mac table
![image](https://github.com/user-attachments/assets/431770f2-7d5f-49fb-acfa-d948527d5cc8)

### Leaf1 evpn summary
![image](https://github.com/user-attachments/assets/30e37c09-65d3-43c7-a546-32148981b7ca)

### Leaf1 evpn routes
![image](https://github.com/user-attachments/assets/332881ee-13e8-45fc-b194-ebdc8e6fe2c5)

### Leaf2 evpn routes
![image](https://github.com/user-attachments/assets/b0e8aed3-e0e1-43e5-8cd7-3f1780300e17)

### Ping Customer-10-1 -> Customer-20-2
![image](https://github.com/user-attachments/assets/c2f0b436-69f6-413b-ad61-707ce078cfce)

