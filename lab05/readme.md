# VxLAN. L2 VNI

### Задачи:
Настроить Overlay на основе VxLAN EVPN для L2 связанности между клиентами

### CLOS топология сети
![image](https://github.com/user-attachments/assets/2a44d3e9-178a-4f14-a322-41e0278c5079)

### Настройка L2 EVPN

1. Включаем фичи, необходимые для работы с vxlan.

> nv overlay evpn  
feature bgp  
feature vn-segment-vlan-based  
feature nv overlay  

2. Настраиваем vlan и мапим к нему vni.
> vlan 10  
  name CUSTOMER-1  
  vn-segment 10010  

3. Настраиваем nve интерфейс, указывая сурс адрес, с которого будет строится vxlan.

  >  interface nve1  
  no shutdown  
  host-reachability protocol bgp  
  source-interface loopback1  
  member vni 10010  
    ingress-replication protocol bgp  
   
4. При настройке bgp пиров указываем update-source адрес, с которого будет строится соседство по bgp, настраиваем ebgp-multihop для увеличения TTL, включаем ext community.

5. На спайнах создаем роут-мапу, которая оставляет поле next-hop в bgp update неизменным (чтобы мы видели своего nve соседа).
6. Поскольку фабрика строится на лифах в разных AS и RT гегениируется автоматически, даже в одинаковых mac-vrf RT для разных устройств будет разным. 
> Пример:  
LEAF1 AS 65001, VNI 10010: RT - 65001:10010.  
LEAF2 AS 65002, VNI 10010: RT - 65002:10010.  
  
Несмотря на одинаковые VNI, лифы не смогут импортировать маршруты в свою таблицу, тк RT не совпадут. Необходимо поменять номер AS, чтобы локальный RT совпал с тем, который анонсирует SPINE.  
Команда: rewrite-evpn-rt-asn. Настраивается у соседа в l2 evpn AF.  

> Из гайдлайна циски: 
This command requires that it be configured on all BGP speakers in the fabric.

Они разбираюот пример с передачей RT _Leaf2 -> Spine1 -> Leaf1_, указывая, что Spine1 также меняет AS в RT на локальную. **Зачем?**  
rewrite-evpn-rt-asn работает на входящие апдейты и Leaf1 поменял бы любое значение AS в RT на свое даже без участия Spine. Это должно работать и без команды на спайне (но не работает - проверено ;D ). **Для чего Spine1 это делает и почему без него это не работает?**  

### Конфигурация соседей на спайнах:
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

### Переконфигурируем соседей статически:
> template peer LEAF  
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

7. Пингуем устройства в одном влане за разными лифами. Пинг прошел. Успех!

### Leaf1 mac table
![image](https://github.com/user-attachments/assets/183aa3a5-2590-415b-afbe-53da20a433a8)

### Leaf1 nve peers
![image](https://github.com/user-attachments/assets/5d0b78dc-31d8-4203-b454-60cefec94a15)

### Leaf1 bgp summary
![image](https://github.com/user-attachments/assets/da7e741b-bce2-4620-afdb-c138ff4a83c9)

### Leaf1 evpn summary
![image](https://github.com/user-attachments/assets/29b6c4c1-4a5e-49cd-a024-028aa00f38c3)

### Leaf1 evpn routes
![image](https://github.com/user-attachments/assets/67157e77-441d-406d-a328-4536e09a7af4)

### Leaf2 evpn routes
![image](https://github.com/user-attachments/assets/21f83850-1c73-4836-bca0-f74e3a91767b)

### Ping Customer-1 -> Customer-2
![image](https://github.com/user-attachments/assets/a6481092-29bb-4468-b301-d3b85b6c050e)

