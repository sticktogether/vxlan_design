# VxLAN. VPC

### Задачи:
Настроить отказоустойчивое подключение клиентов с использованием EVPN Multihoming

### CLOS топология сети
![image](https://github.com/user-attachments/assets/16ec6b64-0cdb-4e6b-bd95-478bfa869416)

### Настройка vPC

1. Включаем фичи для поддержки vPC.

> feature interface-vlan  
feature vn-segment-vlan-based  





























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


