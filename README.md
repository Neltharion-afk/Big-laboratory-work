# Корпоративная сеть NetCorp

Проект отказоустойчивой корпоративной сети с удалённым филиалом.

---

## О проекте

**Цель:** Спроектировать и настроить отказоустойчивую корпоративную сеть с защищённым подключением филиала через GRE-туннель.

**Среда:** Cisco Packet Tracer 9.0


## Технологии

| Технология | Назначение |
|-----------|------------|
| VLAN | Сегментация отделов |
| STP (Rapid PVST+) | Петли + балансировка |
| EtherChannel | Агрегация линков |
| HSRP | Отказоустойчивый шлюз |
| OSPF (multi-area) | Динамическая маршрутизация |
| GRE | Туннель до филиала |
| DHCP | Автовыдача IP |
| NTP + Syslog | Время и логи |
| Port Security | Защита портов |
| DHCP Snooping + DAI | Защита L2 |
| ACL | Разграничение доступа |
| NAT (PAT + Static) | Интернет + веб-сервер |
| WLAN (WPA2-PSK) | Беспроводная сеть |
| QoS (LLQ) | Приоритет голоса |


## Схема сети

<img width="1569" height="696" alt="image" src="https://github.com/user-attachments/assets/6f308bd1-8f2d-480a-8904-5f1ecc70d38c" />

## IP-адресация и VLAN

См. [IP-PLAN](IP-PLAN.md) и [VLAN-TABLE](VLAN-TABLE.md)

## Матрица доступа (ACL)

См. [ACCESS-MATRIX](ACCESS-MATRIX.md)

## Скриншоты проверок

### Отказоустойчивость

#### HSRP

<img width="545" height="154" alt="image" src="https://github.com/user-attachments/assets/ca125699-f18d-4725-8afb-f7e426cd62db" /> <img width="544" height="156" alt="image" src="https://github.com/user-attachments/assets/1028cf5b-2db8-4b9c-96bc-e9d50d6fbfc1" />

Вывод ```show standby brief``` на ```CORE-1``` и ```CORE-2```. Видно распределение ```Active/Standby``` по ```VLAN```

#### STP

CORE-1

<img width="545" height="219" alt="image" src="https://github.com/user-attachments/assets/8002fee7-b1ee-4307-a05d-e65005f7c3b9" /> 

CORE-2

<img width="543" height="221" alt="image" src="https://github.com/user-attachments/assets/500d4f6b-e6ca-497d-8e74-0621449d59e2" />

Вывод ```show spanning-tree vlan 20``` и ```vlan 30```. ```CORE-1``` root для Sales, ```CORE-2``` root для IT

#### ETHERCHANNEL

<img width="417" height="235" alt="image" src="https://github.com/user-attachments/assets/51e1f764-b12e-4060-ba10-b76c3d844446" /> <img width="419" height="235" alt="image" src="https://github.com/user-attachments/assets/50b8c7d7-87c4-423b-bd95-360deb13b8ef" />

Вывод ```show etherchannel summary```. ```Po5(SU)```, оба порта в статусе ```P```

#### HSRP-FAILOVAER

<img width="915" height="345" alt="image" src="https://github.com/user-attachments/assets/9776fa48-3ded-4a9a-b0d5-8e91311f5293" />

<img width="895" height="403" alt="image" src="https://github.com/user-attachments/assets/4fb4a751-e77d-4db9-868d-378d48543996" />

Пинг 10.0.20.1 с ПК Sales при отключении CORE-1. Потеря 3 пакетов, затем восстановление, при включении так же потеря 2-ух пакетов, потом восстановление через CORE-1

### Маршрутизация и филиал

#### OSPF-NEIGHBORS

<img width="608" height="91" alt="image" src="https://github.com/user-attachments/assets/174e6281-2bbb-4695-b403-a998031752e9" />

Вывод ```show ip ospf``` neighbor на ```EDGE-HQ```. Соседи ```1.1.1.1```, ```2.2.2.2```, ```4.4.4.4``` в FULL

#### GRE-TUNNEL

EDGE-HQ

<img width="539" height="418" alt="image" src="https://github.com/user-attachments/assets/88ab0d47-0485-4713-a133-a8b9e31fb6d6" /> 

EDGE-BR

<img width="539" height="419" alt="image" src="https://github.com/user-attachments/assets/5472769c-56e0-4ad3-abf8-6891a89028df" />

Вывод ```show interface tunnel 0``` на ```EDGE-HQ``` и ```EDGE-BR```. Статус ```up/up```.

#### OSPF-ROUTES

<img width="1170" height="207" alt="image" src="https://github.com/user-attachments/assets/2ebe3910-b62b-4b74-a480-2a676aa5bed9" />

Вывод ```show ip route ospf``` на ```CORE-1``` и ```EDGE-BR```. Маршруты через ```GRE```

#### TRACERT-BRANCH

<img width="383" height="131" alt="image" src="https://github.com/user-attachments/assets/002b08aa-fd9d-43e3-b096-7bd945b3cf0f" />

Трассировка с ПК филиала до ```SRV-DHCP```. Путь через туннель за 4 хопа

### Безопасность

#### PORT-SECURITY

<img width="501" height="138" alt="image" src="https://github.com/user-attachments/assets/447c677b-d488-45bf-9916-c38181bd5f89" />

Вывод ```show port-security``` на ACC-1. 6 портов защищены, Sticky-адреса привязаны ко всем портам, ```violation``` ```shutdown```

#### DHCP-SNOOPING

<img width="387" height="205" alt="image" src="https://github.com/user-attachments/assets/277a50ab-3ed2-4aee-9d76-1f1e6bf0e82f" />

Вывод ```show ip dhcp snooping``` на ```ACC-1```. ```Enabled```, доверенные порты: ```Gi0/1```, ```Gi0/2``` (аплинки), ```Fa0/20``` (DHCP-сервер)

#### DAI

<img width="528" height="520" alt="image" src="https://github.com/user-attachments/assets/74bc9f9c-9286-432f-b074-feb40412de14" />

Вывод ```show ip arp inspection``` на ```ACC-1```. ```Active``` на ```VLAN 10,20,30,60```

#### ACL-COUNTERS

<img width="474" height="195" alt="image" src="https://github.com/user-attachments/assets/a9422983-a1a4-452d-848d-3cc74caf65cf" />

Вывод ```show access-list``` на ```CORE-1.```, к сожалению на CPT access-list работает не совсем корректно и не отображает ```match(es)``` корректно. А так же ```access-group``` на саб интерфейсе не отображается, но отрабатывает что будет видно дальше, но после ```reload``` CORE-1 или CORE-2 группы сбрасываются с саб интерфейсов.В моем случае было прописано:
```
configure terminal
interface vlan 20
 ip access-group ACL_SALES out
interface vlan 30
 ip access-group ACL_IT out
interface vlan 60
 ip access-group ACL_GUEST out
 ```

<img width="408" height="146" alt="image" src="https://github.com/user-attachments/assets/7c0330bc-8a47-4276-83cc-52df8160c6f0" />

Пинг с Sales на IT ```Destination host unreachable``` группа отрабатывает.

<img width="409" height="145" alt="image" src="https://github.com/user-attachments/assets/4f1c1b37-4df1-4191-afe1-e0b52ebf5aeb" />

Пинг с Guest на Sales ```Destination host unreachable``` группа отрабатывает.

### Сервисы

#### DHCP-POOLS

<img width="487" height="252" alt="image" src="https://github.com/user-attachments/assets/0674d188-e24c-415c-bba6-b9fad0039545" />

Окно ```Services DHCP``` на SRV-DHCP. 6 пулов для всех ```VLAN```.

#### NTP-STATUS

<img width="609" height="106" alt="image" src="https://github.com/user-attachments/assets/5ce47351-056c-405b-9d1f-22df4df7bf90" />

Вывод ```show ntp status``` на ACC-1. ```Synchronized, stratum 2```.

#### SYSLOG

<img width="1434" height="442" alt="image" src="https://github.com/user-attachments/assets/81a2c04e-ad7b-4e65-9197-a5fd8b0f9556" />

Окно ```Services SYSLOG``` на SRV-DHCP. Логи отключения и включения интерфейсов.

### WLAN и QoS

#### WLAN-CORP

<img width="432" height="273" alt="image" src="https://github.com/user-attachments/assets/f194b03f-07f1-4807-beb7-62f4c0eba326" />

```ipconfig``` на Laptop-CORP. ```IP``` из ```VLAN 20``` (10.0.20.x)

#### WLAN-GUEST

<img width="437" height="274" alt="image" src="https://github.com/user-attachments/assets/3e8bcdb3-d61d-4761-84fa-c3741d3259c9" />

```ipconfig``` на Laptop-Guest. ```IP``` из ```VLAN 60``` (192.168.100.x)

#### QOS-POLICY

<img width="361" height="130" alt="image" src="https://github.com/user-attachments/assets/3cfe4571-0317-4952-8061-678907acbf9d" />

```ipconfig``` на Laptop-Guest. ```IP``` из ```VLAN 60``` (192.168.100.x)

#### PHONES

<img width="500" height="158" alt="image" src="https://github.com/user-attachments/assets/1b5dd08d-e985-487c-bd22-0148cb05e0ff" />
<img width="501" height="159" alt="image" src="https://github.com/user-attachments/assets/a9aa8068-7c86-4443-adc6-897113702ad0" />

IP-телефоны. Адреса из ```VLAN 200``` (10.0.200.x)

### NAT и Интернет

#### NAT-TRANSLATIONS

<img width="558" height="60" alt="image" src="https://github.com/user-attachments/assets/161198e3-fe9d-4d60-92c2-9096610b6cb2" />

Вывод ```show ip nat translations``` на EDGE-HQ статический ```NAT``` для веб-сервера DMZ внутренний ```172.16.0.10``` -> внешний ```203.0.113.10``` порты 80 (HTTP) и 443 (HTTPS) опубликованы

#### PING-INTERNET

<img width="398" height="170" alt="image" src="https://github.com/user-attachments/assets/fcf99c4f-5c75-4168-a038-ec267a9c2054" />

Пинг с ПК Sales на ```203.0.113.2```. Успешный выход в Интернет

## Конфигурации устройств

[CORE-1](CORE-1)
[CORE-2](CORE-2)
[EDGE-HQ](EDGE-HQ)
[EDGE-BR](EDGE-BR)
[ACC-1](ACC-1)
[ACC-2](ACC-2)
[BR-WS](BR-SW)
[SP-1](SP-1)
[SP-2](SP-2)

## Тестирование

Все технологии протестированы:

- HSRP: отказ ядра — потеря ≤ 4 пакетов
- OSPF: соседи FULL, маршруты через GRE
- ACL: разграничение доступа между отделами
- DHCP: централизованная выдача адресов
- NTP: все устройства синхронизированы
- WLAN: 2 SSID, гостевая изоляция

## Ограничения CPT

- NAT hairpin (loopback) не работает
- QoS только IP Precedence (нет DSCP)
- ACL на SVI требуют точной настройки access-group без перезагрузки оборудования

## Автор

Любимов Николай












