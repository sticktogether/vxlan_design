# Построение Underlay сети(IS-IS)

### Задачи:
1. Настроить IS-IS для Underlay сети.

### CLOS топология сети

![image](https://github.com/user-attachments/assets/527fdec8-41f8-44a2-b93a-4a6c6481522d)


### Настройка IS-IS

1. Включаем фичу IS-IS в NX-OS feature isis.
2. Удаляем процесс OSPF и его конфигурацию с портов.
3. Создаем IS-IS процесс router isis 100.
4. Прописываем passive-interface default для того, чтобы не рассылать апедйты по дефолту.
5. Задаем RID для каждого роутера (например, net 49.0001.0010.0100.1020.00).
6. Отключаем passive-interface на необходимых интерфейсах no isis passive-interface.
7. Задаем тип интерфейса isis network point-to-point (для физических интерфейсов).
8. Включаем IS-IS процесс на интерфейсе ip router isis 100.
9. Добавляем Lo1, Lo2 в IS-IS ip router isis 100.

#### Состояние интерфейсов на SPINE2

![image](https://github.com/user-attachments/assets/8deebc66-bb6f-4119-b61c-2bae4eb46556)


#### Таблица маршрутизации на SPINE1

![image](https://github.com/user-attachments/assets/1b369dfa-9c9f-4702-8eba-1ab58236bcb5)


#### Таблица соседства на LEAF2

![image](https://github.com/user-attachments/assets/ffbfabca-9411-4ffd-b768-e07cd51c2909)


#### Пинг Lo1 LEAF4 -> Lo2 LEAF1

![image](https://github.com/user-attachments/assets/1270c03f-c3f3-4e63-8d56-c96c0d5364dc)

Все пинги проходят успешно. Полная связность между всеми Loopback интерфейсами. Настройка супер быстрая, но не такая очевидная, как в OSPF (в части задания net в router isis) :D
