# Построение Underlay сети(eBGP)

### Задачи:
1. Настроить BGP для Underlay сети.

### CLOS топология сети

![image](https://github.com/user-attachments/assets/527fdec8-41f8-44a2-b93a-4a6c6481522d)


### Настройка BGP

1. Включаем фичу BGP в NX-OS feature bgp.
2. Удаляем процесс OSPF и его конфигурацию с портов.
3. Создаем IS-IS процесс router isis 100.
4. Прописываем passive-interface default для того, чтобы не рассылать апедйты по дефолту.
5. Задаем RID для каждого роутера (например, net 49.0001.0010.0100.1020.00).
6. Отключаем passive-interface на необходимых интерфейсах no isis passive-interface.
7. Задаем тип интерфейса isis network point-to-point (для физических интерфейсов).
8. Включаем IS-IS процесс на интерфейсе ip router isis 100.
9. Добавляем Lo1, Lo2 в IS-IS ip router isis 100.

#### Состояние интерфейсов на SPINE2



#### Таблица маршрутизации на SPINE1



#### Таблица соседства на LEAF2



#### Пинг Lo1 LEAF4 -> Lo2 LEAF1
