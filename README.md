
# [КВАС](https://forum.keenetic.com/topic/14415-пробуем-квас-shadowsocks-и-другие-vpn-клиенты/?do=findComment&comment=152234) - выборочный обход блокировок
### VPN и SHADOWSOCKS клиент для [роутеров Keenetic](https://keenetic.ru/ru/)

--- 

Автор: **[Zeleza](https://forum.keenetic.com/profile/20603-zeleza/)**  
Разработка проекта ведется на IDE от компании [JetBrains](https://www.jetbrains.com/ru-ru/)
Для проведения тестов, в проекте используется пакет [BATS](https://github.com/bats-core/bats-core/blob/master/LICENSE.md) от нескольких [АВТОРОВ](https://github.com/bats-core/bats-core/blob/master/AUTHORS). 

---

#### Пакет представляет собой обвязку или интерфейс командной строки для работы с белым списком.

Данный пакет позволяет осуществлять
контроль и поддерживать в актуальном состоянии 
список разблокировки хостов или "Белый список". 
При обращении к любому хосту из этого списка, 
весь трафик будет идти через фактические любое 
VPN соединение, заранее настроенное на роутере, 
или через Shadowsocks соединение. 

---

В пакете реализуется связка: **ipset** + **vpn** | **shadowsocks** + [ **dnsmasq (wildcard)** + **dnscrypt-proxy2** ] | **AdGuardHome**.

---

В связи с использованием в пакете утилиты dnsmasq с **wildcard**, можно работать с любыми доменными именами третьего и выше уровней. 
Т.е. в белый список достаточно добавить ***domen.com** и маршрутизация трафика 
будет идти как к **sub1.domen.com**, так и к любому другому поддоменному имени типа **subN.domen.com**.

Последние новости о пакете, комментарии и пожелания можно узнать и обсудить на форуме компании Keenetic - [forum.keenetic.com](https://forum.keenetic.com/topic/14415-%D0%BF%D1%80%D0%BE%D0%B1%D1%83%D0%B5%D0%BC-%D0%BA%D0%B2%D0%B0%D1%81-shadowsocks-%D0%B8-%D0%B4%D1%80%D1%83%D0%B3%D0%B8%D0%B5-vpn-%D0%BA%D0%BB%D0%B8%D0%B5%D0%BD%D1%82%D1%8B)

## Список изменений 
- Крайняя версия Кваса  [1.0 beta 21](https://github.com/qzeleza/kvas/blob/main/ipk/)
- Список последних изменений можно [посмотреть здесь](HISTORY.md)

## ⚠️ Текущие ограничения 
1. Данный пакет использует возможности **[Entware/opkg](https://help.keenetic.com/hc/ru/articles/360000948719-OPKG)**. Без установленного **Entware** пакет **Квас** работать не будет.
2. В пакете используются некоторые функции API от Keenetic, потому **на других роутерах пакет не функционирует**.
3. Пакет работает пока только на IPv4.

## Возможности
1. Квас работает на всех **роутерах Keenetic**, в виду легковесности задействованных пакетов (начиная с версии **0.9 beta 9** работает на всех платформах: **mips, mipsel, aarch64**)
2. Квас использует **dnsmasq**, ***с поддержкой регулярных выражений***, а это в свою очередь дает одно, но большое преимущество: можно работать с соцсетями и прочими высоко-нагруженными сайтами, добавив лишь корневые домены по этим сайтам.
3. Квас позволяет **просматривать/добавлять/удалять/очищать/обновлять/импортировать и экспортировать** доменные имена списка разблокировки или белого списка.
4. Квас позволяет **отображать статус/отключать/включать** блокировку рекламы
5. Квас позволяет **отображать статус/отключать/включать** шифрование DNS
6. Квас позволяет тестировать и выводить отладочную информацию по всем элементам связки **ipset + vpn | shadowsocks + [ dnsmasq + dnscrypt-proxy2 ] | AdGuardHome**

#### Начиная с версии 1.0 beta 8 добавлена возможность подключения AdGuardHome в качестве DNS сервера вместо связки [ dnsmasq + dnscrypt-proxy2 ].

# Навигация
1. [Установка](#установка)
    1. [Подготовительные действия](#подготовительные-действия)
    2. [Установка пакета](#установка-пакета)
2. [Диагностика в случае проблем](#диагностика-в-случае-проблем)
3. [Пример использования](#примеры-использования)
4. [Подключение AdGuard Home](#использование-adguard-home-совместно-с-пакетом-квас)
5. [Подробный разбор команд](#подробный-разбор-команд)
    1. [Управление списками](#управление-списками)
    2. [Работа с файловым списком](#работа-с-файловым-списком)
    3. [Управление обновлениями маршрутов](#управление-обновлениями-маршрутов)
    4. [Управление подключением AdGuard Home](#adguard-home)
    5. [Управление интерфейсами (bridge)](#интерфейсы)
    6. [Управление VPN](#управление-vpn)
    7. [Управление DNS](#управление-dns)
    8. [Управление ssr](#управление-ssr)
    9. [Управление dnsmasq](#управление-dnsmasq)
    10. [Отладка ошибок](#отладка)
   11. [Фиксация ошибок](https://github.com/qzeleza/kvas/blob/main/issues/README.md)
   12. [Помощь](#помощь)
6. [Помощь проекту](#помощь-проекту)

---

# Установка
## Подготовительные действия

--- 
**Подготовьте к работе свою учетную запись VPN** 


1. **Для Shadowsocks**
      1. Приобретите на стороне учетную запись **Shadowsocks** клиента, либо установите **[свой сервер на docker](https://github.com/shadowsocks/shadowsocks-libev/blob/master/docker/alpine/README.md)** (дело 5-ти минут).
         
         **Внимание!** Практика показывает, что аренду сервера необходимо осуществлять у зарубежного провайдера! Это поможет решить проблемы с частичным открытием доменов из "белого списка".
   
   3. Получите и приготовьте четыре параметра:
         - **IP адрес** или доменное имя **Shadowsocks** сервера
         - **Номер** удаленного **порта Shadowsocks** сервера
         - **Пароль** для доступа на **Shadowsocks** сервер
         - **Метод шифрования** на стороне **Shadowsocks** сервера

---
2. Для других **VPN** соединений: **OpenVPN**, **Wireguard**, **IKE**, **PPPOE**, **L2TP**, **SSTP** - **[настройте подключение на своем роутере](https://help.keenetic.com/hc/ru/search?utf8=✓&query=Подключение+по+протоколу&commit=Поиск)** и проверьте его работоспособность.

   1. Установите **[Entware](https://help.keenetic.com/hc/ru/articles/360000948719-OPKG)**
   2. Установите необходимые компоненты в **WUI** роутера: **Общие настройки -> [Изменить набор компонентов](https://help.keenetic.com/hc/ru/articles/360009294539-Описание-компонентов-KeeneticOS):**
       - **Модули ядра подсистемы Netfilter**
       - **Пакет расширения Xtables-addons для Netfilter**
   3. Зайдите в админ панель роутера по адресу: **https://<IP роутера>/a** (будьте бдительны - символ **‘а’** в конце адреса не описка) и введите в поле **Command** следующую команду ```opkg dns-override```, затем нажмите кнопку **Send request**. 
   4. Далее, необходимо сохранить настройки, в том же поле введите команду ```system configuration save```, затем нажмите кнопку **Send request**. 
   5. И затем, необходимо перегрузить роутер, для этого, в том же поле введите ```system reboot```, после чего, нажмите **Send request**.

---

## Установка пакета
После перезагрузки устройства:
1. Cкачайте требуемую версию пакета на роутер, например в папку **/opt/packages/**. Крайнюю версию пакета всегда можно найти по [этой ссылке](https://github.com/qzeleza/kvas/tree/main/ipk).
2. Удалите предыдущую версию пакета (если она была установлена ранее)
3. Установите новую версию.
4. В процессе установки введите, полученные Вами данные на [подготовительном шаге](#подготовительные-действия) (учетные данные **shadowsocks**).
5. Далее, отвечайте на вопросы по ходу установки.

```
 ВАЖНО!
 
 ПЕРЕД ПОДКЛЮЧЕНИИ КЛИЕНТОВ К РОУТЕРУ ОБЯЗАТЕЛЬНО ПРОВЕРЬТЕ:
 
 1. ЧТО В НАСТРОЙКАХ ВАШИХ КЛИЕНТОВ, КОТОРЫЕ ПОДКЛЮЧАЮТСЯ К НЕМУ ПО WIFI ИЛИ ПО VPN, 
    В СТРОКЕ АДРЕСА ВАШЕГО DNS, УКАЗАН АДРЕС ВАШЕГО РОУТЕРА, А НЕ DNS ВАШЕГО ПРОВАЙДЕРА 
    ИЛИ ИЗ СПИСКА СТАНДАРТНЫХ АДРЕСОВ ТИПА 8.8.8.8, 1.1.1.1, 9.9.9.9 ИЛИ ИМ ПОДОБНЫЕ. 
 
 2. ЧТО ВАШ БРАУЗЕР НЕ ИСПОЛЬЗУЕТ СОБСТВЕННЫЕ DNS, ТАК КАК ОНИ ИМЕЮТ БОЛЬШИЙ ПРИОРИТЕТ, 
    ЧЕМ НАСТРОЙКИ, УКАЗАННЫЕ В ПУНКТЕ ВЫШЕ.
   
 3. ПРОВЕРЬТЕ В ЛИЧНОМ КАБИНЕТЕ ПРОВАЙДЕРА, ЧТО ОТКЛЮЧЕНА ПОДДЕРЖКА IPV6, ЕСЛИ ТАКОВОЙ ПУНКТ ИМЕЕТСЯ. 
 
```

***Пример кода установки:*** 
```
    mkdir -p /opt/packages # только в случае отсутствия папки
    cd /opt/packages
    opkg remove kvas
    wget https://github.com/qzeleza/kvas/tree/main/ipk/kvas_Х.Х-ХХ_all.ipk
    opkg install /opt/packages/kvas_Х.Х-ХХ_all.ipk
    # Вместо X подставляете актуальную версию пакета
```

#### ВНИМАНИЕ!
Если ранее пакет был уже установлен, то при запросе об удалении файлов конфигурации можете их не удалять, тогда не придется повторно вводить данные установки соединения.


## Диагностика в случае проблем
Существует два уровня диагностики:

1. Для проверки работы пакета наберите ```kvas test```
2. В случае отсутствия результата, наберите ```kvas debug > ./kvas.debug``` и отправьте сообщение автору через [форум Keenetic](https://forum.keenetic.com/profile/20603-zeleza/) или через его почту `kvas собачка zeleza.ru` сформированный **файл**.

# Примеры использования
```
kvas add ya.ru     - добавляем ya.ru в список разблокировки без поддержки регулярных выражений.
kvas add *ya.ru    - добавляем ya.ru в список разблокировки c поддержкой регулярных выражений.
kvas import ./list - добавляем хосты из файла в списочный файл.
kvas rm google     - удаляем все хосты со словом google внутри, из списка разблокировки.
kvas show          - выводим все хосты из списка разблокировки.
kvas test          - тестируем работу всех служб пакета.
kvas debug > ./log - сохраняем журнал отладочной информации в файл.
kvas purge         - удаляем все доменные имена из списка разблокировки.
```

# Использование AdGuard Home с пакетом Квас

## Последовательность шагов:
1. Устанавливаем и запускаем **AdGuardHome**: 
   - Устанавливаем **AdGuardHome** на роутер: `opkg install adguardhome-go`
   - Производим первый запуск **AdGuardHome** командой `AdGuardHome` и следуем инструкциям на экране.
2. [Осуществляем первичную настройку AdGuard Home](https://adguard.com/ru/blog/adguard-home-on-public-server.html)
3. [Устанавливаем Квас](#установка-пакета)
4. Подключаем **AdGuardHome** к **Квасу** командой `kvas adguard on`
5. Обновляем версию **AdGuardHome** на крайнюю, командой `kvas adguard update`

```
ВАЖНО! 
Данная команда `kvas adguard on`, должна исполнятся уже после установки AdGuardHome и его первичной настройки, 
при этом сам пакет AdGuardHome должен быть установлен исключительно на роутер на котором установлен пакет Квас.

Работа пакета AdGuardHome в связке с пакетом Квас, когда оба пакета находятся на различных устройствах - ПОКА НЕ ПРЕДСТАВЛЯЕТСЯ ВОЗМОЖНОЙ!
```

# Подробный разбор команд
## Управление списками
| Команда | Аргументы |
|----------------|:----------------:|
| kvas add / kvas new | [*][хост] |
| kvas del / kvas rm  | [*][хост] |
| kvas purge / kvas clear  | - |

```
add[new]     - добавляем один хост в список разблокировки, аргументом
               является добавляемый хост.
               * - в начале домена включает режим wildcard (пример: *mydomain.com).
               без * - режим wildcard отключен.
               
               При добавлении любого из доменов в список разлокировки, 
               в случае подключенной опции блокирования рекламы 'kvas adblock on', 
               добавляемые доменные имена проходят проверку на наличие их в списке 
               блокировки рекламы и в случае их наличия они удаляются от туда. 
               Причем, если добавлен домен в виде *domain.com, то будут 
               удалены все доменные имена с основанием domain.com, выше 
               второго уровня, т.е. удалению подлежат, все имена от domain.com 
               до dm5.dm4.dm3.domain.com и выше. Но удалению не подлежат 
               такие имена, которые не заканчиваются на domain.com, 
               как например domain.com.ru.org.
               
    
del[rm]      - удаляем указанный хост из списка разблокировки.
purge[clear] - полностью очищаем список разблокировки.
```
### Работа с файловым списком
| Команда                      | Аргументы |
|------------------------------|:----------------:|
| kvas / kvas show / kvas list | - |
| kvas export                  | [файл] |
| kvas import                  | [файл] |

```
show[list] - выводим список всех хостов в списках разблокировки
             и блокировки. Список отображается, в том числе, при запуске
             команды kvas без аргументов.

export     - экспорт списочного файла в указанный файл.

import     - добавляем хосты из указанного файла в списочный файл
             допускается использование комментариев с символом '#'.
```
## Управление обновлениями маршрутов
| Команда               | Аргументы |
|-----------------------|:---------:|
| kvas update/reset     |     -     |
| kvas period           | [период]  |
| kvas period del/clear |     -     |

```
update           - обновляем/переустанавливаем все таблицы, списки ipset IP адресов.
reset            - данная процедура универсальна для любого из подключений.
      
period           - установка/просмотр периода обновления IP адресов
                   для списочного файла. Период должно указывать
                   в форматах: m,h,d,w,n, например:
      
                     2m - обновляем каждые две минуты
                     2h - обновляем каждые два часа
                     3d - обновляем каждые три дня
                     4w - обновляем каждые четыре недели
                     2M - обновляем каждых два месяца
      
                   Возможен только один из вариантов, например:
                   10m или 2h, по умолчанию -период обновления каждые
                   12 часов или 2 раза в сутки.
period del|clear - удаление обновления периода обновления IP адресов из cron
```

## AdGuard Home
| Команда             | Аргументы |
|---------------------|:----------------:|
| kvas adguard        | - |
| kvas adguard on     | - |
| kvas adguard off    | - |
| kvas adguard test   | - |
| kvas adguard update | - |

```
adguard        - выводит состояние режима AdGuard.
adguard test   - тестирует правила создания ipset для AdGuard Home
adguard on     - устанавливаем AdGuard Home в качестве DNS сервера, вместо
                 dnscrypt-proxy2 или вместо текущего DNS. AdGuard Home,
                 при этом, должен быть установлен и настроен на роутере или
                 на любом другом устройстве в локальной сети.
adguard off    - отключает использование AdGuard Home в КВАСе
adguard update - производит обновление на новую версию пакета, если она доступна
```

## Блокировка рекламы
| Команда           | Аргументы |
|-------------------|:---------:|
| kvas adblock      |     -     |
| kvas adblock on   |     -     |
| kvas adblock add  |   host    |
| kvas adblock del  |   host    |
| kvas adblock off  |     -     |
| kvas adblock edit |     -     | 

```
adblock           - выводит состояние режима блокировки рекламы.
adblock on        - включаем, блокировку рекламы.
adblock off       - отключаем, блокировку рекламы.
adblock add <d>   - добавляем домен в список исключений и удаляем его 
                    из списка блокировки рекламы.
adblock del <d>   - удаляем доменное имя из списка исключений.
adblock edit      - редактируем список источников блокировки рекламы.
                    Обработке подлежат лишь те, файлы, которые содержат
                    записи в формате 0.0.0.0 <домен> или 127.0.0 <домен>.
               
```


## Шифрование DNS запросов
| Команда | Аргументы |
|----------------|:----------------:|
| kvas crypt | - |
| kvas crypt on | - |
| kvas crypt off | - |

```
crypt     - выводим статус шифрования dns (включено/отключено).
crypt on  - включаем,  шифрование dns (пакет dnscrypt-proxy2).
crypt off - отключаем, шифрование dns.
```
## Интерфейсы
| Команда | Аргументы |
|----------------|:----------------:|
| kvas bridge | - |
| kvas bridge add | all |
| kvas bridge del | all |

```
bridge         - выводит список доступных гостевых интерфейсов
bridge add all - разрешаем доступ к VPN всем существующим гостевым сетям
bridge del all - запрещаем доступ к VPN для всех гостевых сетей.
```

## Управление DNS
| Команда | Аргументы |
|----------------|:----------------:|
| kvas dns | - |
| kvas dns <DNS> | [IPv4] |
| kvas dns test | - |

```
dns                   - отображаем текущий DNS (по умолчанию) для работы пакета.
dns <DNS>             - устанавливаем DNS (по умолчанию) для пакета в значение <DNS>.
dns test <domain.com> - тестируем работу DNS (по умолчанию) в пакете.
```

## Управление VPN соединениями
| Команда               | Аргументы |
|-----------------------|:---------:|
| kvas vpn              |     -     |
| kvas vpn set          |     -     |
| kvas vpn reset        |     -     |
| kvas vpn rescan[scan] | - |
| kvas vpn guest        |     -     |
| kvas vpn guest        |    brN    |
| kvas vpn guest del    |     -     |

```
vpn             - отображаем текущий тип VPN соединения и его состояние.
vpn set         - меняем текущий VPN интерфейс, путем выбора из списка доступных.
vpn reset       - обновляем таблицу правил iptables для vpn подключения: чистим ее и затем заполняем.
vpn rescan|scan - принудительное сканирование интерфейсов роутера, в случае, 
                  появления нового подключения, например wireguard или openvpn..
vpn guest <brN> - добавляем правила iptable для гостевой сети или VPN сети,
                  по которой подключаются клиенты к роуеру, чтобы клиенты могли
                  ходить на сайты находящиеся в списочном файле.
                  Здесь brN - это идентификатор гостевой сети или VPN подключения
                  в терминах entware, например br1 или open_vpn0 или nwg2.
                  Узнать название сети можно по команде 'ip a' на роутере.
vpn guest       - команда без аргументов выводит, в случае наличия,
                  текущую гостевую сеть.
vpn guest del   - команда без аргументов удаляет гостевую сеть в случае ее наличия.
```

## Управление shadowsocks соедиением
| Команда | Аргументы |
|----------------|:----------------:|
| kvas ssr | - |
| kvas ssr new | - |
| kvas ssr reset | - |
| kvas ssr port| [порт] / ничего |

```
ssr          - отображаем текущий тип VPN соединения и его состояние (тоже что и ключ 'vpn').

ssr new      - меняет настройки учетной записи shadowsocks сервера на другие настройки, 
               в случае смены сервера или иных данных учетной записи

ssr port     - отображаем текущий локальный порт Shadowsocks соединения.
ssr port <N> - меняем текущий локальный порт Shadowsocks соединения на <N>.
ssr reset    - обновляем таблицу правил iptables для Shadowsocks соединения: чистим ее и затем заполняем.
```

## Управление dnsmasq
| Команда | Аргументы |
|----------------|:----------------:|
| kvas dnsmasq | - |
| kvas dnsmasq port | [порт] / ничего |
| kvas dnsmasq ip| [ip] / ничего |

```
dnsmasq          - отображаем текущие порт и IP, которые прослушивает служба dnsmasq.
dnsmasq port     - отображаем текущий порт, который прослушивает служба dnsmasq.
dnsmasq port <N> - меняем текущий порт, который прослушивает служба dnsmasq на <N>.
dnsmasq ip       - отображаем текущий IP, который прослушивает служба dnsmasq.
dnsmasq ip <N>   - меняем текущий IP, который прослушивает служба dnsmasq на <N>.
```

## Отладка
| Команда | Аргументы |
|----------------|:----------------:|
| kvas test  | - |
| kvas debug | - |

```
test  - проверка работы всех служб, задействованных в работе КВАСА.
debug - вывод отладочной информации в случае сбоев в работе КВАСА.
```

## Помощь

| Команда                        | Аргументы |
|--------------------------------|:----------------:|
| kvas version / kvas ver        | - |
| kvas help / kvas -h / kvas --h | - |

```
version | ver     - отображаем версию утилиты.
help | -h | --h   - отображается справка.  
 

```

## Помощь проекту

Поддержать проект можете, путем своего участия в проекте (пишите [в "личку"](https://forum.keenetic.com/profile/20603-zeleza/)), либо путем [перевода любой суммы средств на этот кошелек ЮМани](https://yoomoney.ru/to/4100117756734493).