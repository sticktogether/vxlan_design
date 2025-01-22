## Выводы для траблшутинга


Конфигурация соседений на спайнах:
> neighbor 10.0.0.0/8 remote-as route-map RM_Leaves_BGP  
    bfd  
    update-source loopback1  
    ebgp-multihop 5  
    address-family ipv4 unicast  
    address-family l2vpn evpn  
      send-community  
      send-community extended  
      route-map NEXT-HOP-UNCH out  

### rewrite-rt-asn не хочет применяется к динамической настройке bgp соседей
![image](https://github.com/user-attachments/assets/f4fc50a3-fcfc-4599-8916-9f32e4f8667d)

Переконфигурируем соседей статически:
template peer LEAF  
    bfd  
    update-source loopback1  
    ebgp-multihop 5  
    address-family ipv4 unicast  
    address-family l2vpn evpn  
      send-community  
      send-community extended  
      route-map NEXT-HOP-UNCH out  
      rewrite-evpn-rt-asn  
  neighbor 10.0.0.1  
    inherit peer LEAF  
    remote-as 65001  
  neighbor 10.0.0.2  
    inherit peer LEAF  
    remote-as 65002  


### Leaf1 mac table


### Leaf1 bgp summary


### Leaf1 evpn summary


### Leaf1 evpn routes


### Leaf1 nve peers


### Spine1 bgp summary


### Spine1 evpn summary


### Spine1 evpn routes


### Spine1 adv routes to Leaf1

