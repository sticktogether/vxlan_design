# Построение Underlay сети(OSPF)

### Задачи:
1. Настроить OSPF для Underlay сети.

### CLOS топология сети

![image](https://github.com/user-attachments/assets/527fdec8-41f8-44a2-b93a-4a6c6481522d)


### Настройка OSPF

Конфигурация данного протокола достаточно проста:
1. Включаем фичу с OSPF в NX-OS.
2. Создаем OSPF процесс.
3. Прописываем passive-interface default для того, чтобы не рассылать апедйты по дефолту.
4. * Задаем RID (не конфигурировал, меня устраивает автоматический выбор на основе уже настроенных Lo интерфейсов).
5. * Включаем аутентификацию (используем MD5 шифрование).
6. Отключаем passive-interface на необходимых интерфейсах no ip ospf passive-interface.
7. Задаем тип интерфейса ip ospf network point-to-point (для физических интерфейсов).
8. Включаем OSPF процесс ip router ospf 10000 area 0.
9. Добавляем Lo1, Lo2 в OSPF прооцесс.

#### Соседства на SPINE1

![image](https://github.com/user-attachments/assets/2ad21419-f4ea-4df5-b15f-311b5489ed71)

#### Таблица маршрутизации на SPINE1

![image](https://github.com/user-attachments/assets/f4e08675-5210-4ff4-b7ed-44f657ea0300)


#### Пинг Lo1 LEAF1 -> Lo2 LEAF4

![image](https://github.com/user-attachments/assets/687e1198-e94c-4bca-97f2-1d4183100ac3)
