# Домашнее задание к занятию "`Disaster recovery и Keepalived`" - `Сулименков Алексей`

---

### Задание 1

- Дана [схема](https://github.com/netology-code/sflt-homeworks/blob/main/1/hsrp_advanced.pkt) для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.

#### На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

Для того, что HRSP правильно работало, на Router1, в настройках интерфейса GI0/1, поднял HSRP Priority 50 до 95, в противном случае, при падении линка между Switch0 и Router2, <br>
 на Router1  интерфейс с IP 192.168.1.1 не переходил в Active, т.к. приоритет на Router2 был больше, а задать приоритет при падении линка на Packet Tracer нельзя.

1. [Схема в формате pkt](https://github.com/biparasite/11-01HW/blob/main/hsrp_advanced_HW.pkt)
2. Скриншот настроек Router 1
![Router1](https://github.com/biparasite/11-01HW/blob/main/Router1.png)
3. Скриншот настроек Router 2
![Router2](https://github.com/biparasite/11-01HW/blob/main/router2.png)


---

### Задание 2

- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
- Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script

#### На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

2.1 bash script
```
#!/usr/bin/bash

test -f /var/www/html/index.nginx-debian.html
DATA=$?

nc -z 10.211.55.11 80
PORT_CHECK=$?


if [[ $DATA -ne 0 || $PORT_CHECK -ne 0 ]]; then
  exit 1
fi
```

2.2 Скриншоты с переходом состояния MASTER->BACKUP->MASTER

VM1
![VM1](https://github.com/biparasite/11-01HW/blob/main/vrrp1.png)

VM2
![VM2](https://github.com/biparasite/11-01HW/blob/main/vrrp2.png)


2.3 Конфогурайионный файл VM1 master

```
global_defs {
    vrrp_version 3
}

# Script to check whether Nginx is running or not
vrrp_script check_nginx {
  script "/home/biparasite/nginx/nginx_chk.sh"
  interval 3
}

vrrp_instance VI_1 {
    state MASTER
    interface enp0s5
    priority 101
    virtual_router_id 99
    advert_int 1

    virtual_ipaddress {
        10.211.55.99/24
    }

    track_script {
        check_nginx
    }

}

``` 

Конфогурайионный файл VM2 backup

global_defs {
    vrrp_version 3
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp0s5
    priority 100
    virtual_router_id 99
    advert_int 1

    virtual_ipaddress {
        10.211.55.99/24
    }
}

