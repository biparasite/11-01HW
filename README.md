# Домашнее задание к занятию "`Disaster recovery и Keepalived`" - `Сулименков Алексей`

---

### Задание 1

- Дана [схема](https://github.com/netology-code/sflt-homeworks/blob/main/1/hsrp_advanced.pkt) для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.

####. На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

Для того, что HRSP правильно работало, на Router1, в настройках интерфейса GI0/1, поднял HSRP Priority 50 до 95, в противном случае, при падении линка между Switch0 и Router2, <br>
 на Router1  интерфейс с IP 192.168.1.1 не переходил в Active, т.к. приоритет на Router2 был больше, а задать приоритет при падении линка на Packet Tracer нельзя.

1. Схема
[Схема в формате pkt](https://github.com/biparasite/11-01HW/blob/main/hsrp_advanced_HW.pkt)
2. Скриншот настроек Router 1
![Router1](https://github.com/biparasite/11-01HW/blob/main/Router1.png)
3. Скриншот настроек Router 2
![Router2](https://github.com/biparasite/11-01HW/blob/main/Router2.png)


---

