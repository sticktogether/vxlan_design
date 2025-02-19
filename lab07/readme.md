# VxLAN. VPC

### Задачи:
Настроить отказоустойчивое подключение клиентов с использованием EVPN Multihoming

### CLOS топология сети
![image](https://github.com/user-attachments/assets/16ec6b64-0cdb-4e6b-bd95-478bfa869416)

### Настройка vPC

1. Включаем фичи для поддержки vPC.

> feature lacp  
feature vpc  

2. Подключаем 2 сервера к Leaf3/4 (один из них в новом VNI 10030, который будет создан только на VPC паре).
>vlan 30  
  name CUSTOMER-3  
  vn-segment 10030  

> interface Vlan30  
  no shutdown  
  vrf member CUSTOMER-1  
  no ip redirects  
  ip address 192.168.30.30/24  
  no ipv6 redirects  
  fabric forwarding mode anycast-gateway  
  
>interface nve1  
member vni 10030  
    ingress-replication protocol bgp  
  member vni 100100 associate-vrf
3. Создаем vrf для peer-keepalive линка. Настраиваем vpc домен, keepalive и peer-link.
> vrf context keepalive
  
> interface Ethernet1/7  
  vrf member keepalive  
  ip address 192.168.1.1/24  
  no shutdown  

> vpc domain 100  
  peer-switch  
  system-priority 1  
  peer-keepalive destination 192.168.1.2 source 192.168.1.1 vrf keepalive  
  peer-gateway  
  ip arp synchronize  

> interface port-channel100  
  switchport  
  switchport mode trunk  
  spanning-tree port type network  
  vpc peer-link  

4. Настраиваем даунлинки Po 1/2 в сторону конечных хостов.
> interface port-channel1  
  switchport  
  switchport mode trunk  
  vpc 1  
  
> interface port-channel2  
  switchport  
  switchport mode trunk  
  vpc 2  

5. Собираем портченелы на хостах. Проверяем состояние vpc. Смотрим evpn роуты. Произвольно пингуем хосты друг с другом.


### Leaf1 nve peers
![image](https://github.com/user-attachments/assets/a74d676b-c999-4280-80c1-2636ace5ddaf)

### Leaf3 mac table
![image](https://github.com/user-attachments/assets/ad05add3-2b00-4c39-a771-be726ce1ef88)

### Leaf3 evpn summary
![image](https://github.com/user-attachments/assets/f26d27cd-9407-43ed-b6ec-20a89bd268de)

### Leaf3 evpn routes
![image](https://github.com/user-attachments/assets/f2e04aa3-23fe-4161-90cc-9b217872bc22)

### Leaf3 vpc
![image](https://github.com/user-attachments/assets/dc1aacc1-6644-4414-bb3a-c20f9d8a171e)

### Leaf4 mac table
![image](https://github.com/user-attachments/assets/d10007c6-f9d1-4404-92bc-d90cab687582)

### Leaf4 evpn routes
![image](https://github.com/user-attachments/assets/65a7088a-8110-4e93-a1d2-724e6bd2bdbe)
![image](https://github.com/user-attachments/assets/b99397ad-aa87-4d2a-bf11-e979ee4df4d5)

### Leaf4 vpc
![image](https://github.com/user-attachments/assets/5672a922-ae80-490c-a05d-7457251b6aba)

### Leaf4 vpc consistency-parameters
![image](https://github.com/user-attachments/assets/791c7766-dc19-4172-a280-a42ceaa2fd76)

### Ping Customer-10-2 -> Customer-10-1/ Customer-10-3/ Customer-20-1/ Customer-20-2/ Customer-30-1/
![image](https://github.com/user-attachments/assets/c61c5229-0341-4b5a-9424-f598801a9df0)

### Ping Customer-30-1 -> Customer-10-1/ Customer-20-1
![image](https://github.com/user-attachments/assets/6b8497a3-1098-47f7-87c4-e92cd5263057)


