# Построение Underlay сети(eBGP)

### Задачи:
1. Настроить BGP для Underlay сети.

### CLOS топология сети

![image](https://github.com/user-attachments/assets/527fdec8-41f8-44a2-b93a-4a6c6481522d)


### Настройка BGP

1. Включаем фичу BGP в NX-OS feature bgp.
2. Удаляем процесс IS-IS и его конфигурацию с портов.
3. Создаем BGP процесс.
4. Конфигурируем LEAF коммутаторы.

#### LEAF1
feature bgp
feature bfd
route-map RM_REDIS_CON permit 10
  match ip address 10.0.0.1
route-map RM_REDIS_CON permit 20
  match ip address 10.1.0.1
route-map RM_REDIS_CON deny 30

router bgp 65001
  router-id 10.0.0.1
  reconnect-interval 12
  log-neighbor-changes
  address-family ipv4 unicast
    redistribute direct route-map RM_REDIS_CON
    maximum-paths 10
  template peer SPINES
    bfd
    remote-as 65010
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.1.0
    inherit peer SPINES
  neighbor 10.2.2.0
    inherit peer SPINES

5. Конфигурируем SPINE коммутаторы.

#### SPINE1
route-map RM_Leaves_BGP permit 10
  match as-number 65001, 65002, 65003, 65004
  
router bgp 65010
  router-id 10.0.1.0
  reconnect-interval 12
  log-neighbor-changes
  address-family ipv4 unicast
    maximum-paths 10
  neighbor 10.0.0.0/8 remote-as route-map RM_Leaves_BGP
    bfd
    address-family ipv4 unicast


#### Таблица соседства на SPINE2

![image](https://github.com/user-attachments/assets/0ee5036f-084d-47e8-a28d-f3a9f5de6023)


#### Таблица маршрутизации на LEAF1

![image](https://github.com/user-attachments/assets/7e72118e-4650-4556-ac5f-a9551ac2f6a3)


#### Пинг Lo1 LEAF2 -> Lo2 LEAF4

![image](https://github.com/user-attachments/assets/6e75f85b-faa6-4dc0-a5b8-57aca543c730)

