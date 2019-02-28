# Zabbix в примерах

## Zabbix. Получение и первичная настройка.

Zabbix можно получить по ссылке:
http://www.zabbix.com/download.php
Системные требования: *2 ядра CPU / 2ГБ*
Далее предполагается, что вы скачали с официального сайта образ виртуальной машины, либо ISO-файл и установили его в Ubuntu Server 16.04 LTS.
По умолчанию доступ к веб-интерфейсу разрешен отовсюду.
Инсталляция готового решения Zabbix имеет следующие пароли:
Доступ к веб-интерфейсу может быть получен с http://<хост>/zabbix
Логин.пароль:
Admin:zabbix
SSH доступ логин.пароль:
appliance:zabbix
Используйте “sudo su” команду вместе с паролем от “appliance” пользователя для получения привилегированных root прав.
База данных:	

root:<случайный>
zabbix:<случайный>

Создание своего пользователя.
`adduser имя_пользователя`


После того, как пользователь создан, добавляем его в группу sudo.

`usermod -a -G sudo имя_пользователя`
Не забудьте отключить пользователя по умолчанию

`passwd appliance –l`


На все вопросы по работе сервера и клиента вам с удовольствием ответят логи.
Логи сервера лежат по пути:

`/var/log/zabbix/zabbix_server.log`

Агента:

`/var/log/zabbix/zabbix_agentd.log`

Команда:

`tail -f /путь/к/файлу` 

поможет в прямом эфире отслеживать, что же происходит.

На картинке пример просмотра логов агента.


## Zabbix добавление узла сети вручную


Для того, чтобы добавить узел сети, необходимо перейти в меню настройка, подменю узлы сети и выбрить создать узел сети.


Присваиваем узлу сети имя, вводим его видимое имя, добавляем в существующую или создаем новую группу, задаем интерфейс, посредством которого будет осуществляться мониторинг.



Для получения информации по узлу сети необходимо добавить к нему шаблон(ы).
Делается это так: выбираем узел сети, переходим на вкладку шаблоны, нажимаем кнопку выбрать, в открывшемся окне выбираем необходимый шаблон или шаблоны и нажимаем выбрать. Когда шаблоны выбраны, нажимаем на главной странице добавить и обновить.
В результате получаемые с узла данные будут доступны в меню мониторинг, подменю последние данные.


## Обнаружение узлов в сети.


Для того, чтобы не создавать все узлы сети вручную, полезно использовать функцию обнаружения. Она позволяет находить узлы и производить с ними различные действия:
добавлять в определенные группы узлов
привязывать шаблоны
прочее.
Чтобы создать правило обнаружения необходимо перейти в раздел обнаружение и выбрать пункт создать правило обнаружения.
Дальнейшие действия будем производить на примере принтеров. Предполагается, что нам известен диапазон их адресов.
 


## Установка Zabbix agent в CentOS 7


`sudo bash`
`rpm -ivh http://repo.zabbix.com/zabbix/3.4/rhel/7/x86_64/zabbix-release-3.2-1.el7.noarch.rpm`

`yum install zabbix-agent` 

По умолчанию в CentOS сервис не запускается автоматически при перезагрузке!
Убеждаемся в этом:
`systemctl status zabbix-agent`

И добавляем его в автозагрузку:
`systemctl enable zabbix-agent`

Запускаем сервис:
`systemctl start zabbix-agent`
Проверяем, все ли в порядк:
`systemctl status zabbix-agent`


## Установка Zabbix agent в Ubuntu 16.04 LTS


`sudo bash`

`wget http://repo.zabbix.com/zabbix/3.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.4-1+trusty_all.deb`

`dpkg -i zabbix-release_3.4-1+trusty_all.deb`

`apt-get update`

`apt-get install zabbix-agent`


## Установка Zabbix agent в Windows 7/8/2012 32-x/64-x
Для операционных систем семейства Windows на сайте представлены бинарники агента для 32-х и 64-х разрядных архитектур.
http://www.zabbix.com/downloads/3.4.0/zabbix_agents_3.4.0.win.zip
Установщик отсутствует. Для установки необходимо проделать несколько шагов:

1. Открыть в файерволе TCP порт 10050, по которому агент общается с сервером

`netsh advfirewall firewall add rule name="Zabbix Agent" dir=out protocol=tcp localport=10050 action=allow`

`netsh advfirewall firewall add rule name="Zabbix Agent" dir=in protocol=tcp localport=10050 action=allow`

2. Распаковать архив

3. Переименовать папку в zabbix_agent

4. `c:\zabbix_agent\bin\win64\zabbix_agentd.exe --config c:\zabbix_agent \conf\zabbix_agentd.conf --install`

Где: 
c:\zabbix_agent\bin\win64\zabbix_agentd.exe – путь к исполняемому файлу тебуемой разрядности;

--config c:\zabbix_agent\conf\zabbix_agentd.conf – путь к конфигурационному файлу;

--install – команда для установки сервиса с указанными выше параметрами.

На 64-битных системах требуется 64-битная версия Zabbix агента, чтобы все проверки связанные с запущенными 64-битными процессами корректно работали.

Или возьмите готовое у меня:

https://github.com/alv1r/zabbix_agent



Подробнее про настройки агента, специфические для Windows можно посмотреть тут:
https://www.zabbix.com/documentation/3.4/ru/manual/appendix/install/windows_agent

Для облегчения задачи я написал скрипт, который автоматически создает конфигурационный файл, открывает порты и устанавливает сервис требуемой разрядности.
Полный комплект (zabbix_agent) лежит на яндекс диске в папке soft
Скрипт требует настройки
SET String=%computername%.%userdomain%.local – заменяем на постфикс вашего домена (ru/loc/net) 
SET Zabbix=192.168.10.31 – заменяем на адрес вашего сервера Zabbix.


Настройка Zabbix agent (универсальная)


Zabbix agent хранит свои настройки в файле zabbix_agentd.conf .
В ОС UNIX он хранится по пути /etc/zabbix/zabbix_agentd.conf
В ОС Windows он хранится по пути zabbix_agent\conf\zabbix_agentd.conf
Ниже приведена минимальная необходимая для работы конфигурация. 

Server=192.168.10.31 – адрес сервера Zabbix

ServerActive=192.168.10.31 – адрес сервера Zabbix

Hostname=001-0036.et.local – полное имя хоста, в таком виде, каким его видит сервер.

ListenPort=10050  - порт, на котором будет работать агент.

LogFile=c:\zabbix_agent\zabbix_agentd.log  - расположение файла журнала в Windows

LogFile=/var/log/zabbix/zabbix_agentd.log - расположение файла журнала в UNIX

LogFileSize=10 – размер файла журнала в мегабайтах.

Подробнее про настройку можно почитать тут:
для Windows

https://www.zabbix.com/documentation/3.4/ru/manual/appendix/config/zabbix_agentd_win
UNIX

https://www.zabbix.com/documentation/3.4/ru/manual/appendix/config/zabbix_agentd


## Получение данных от агента.

В целях отладки очень удобно получать данные от агента в консоли. 
За это отвечает утилита zabbix_get. Синтаксис у нее такой:
zabbix_get -s <host> -p <port> -k ключ 
Где:
-s <host>  - адрес узла
-p <port> - порт, на котором слушает агент
-k ключ – ключ, значение которого необходимо получить

В примере выше мы запросили у узла 192.168.10.30, агент у которого работает на порту 10050 значение ключа sip.status.
.

## Мониторинг журналов событий Windows



Для того, чтобы работать с журналом событий, достаточно создать элемент данных следующего вида:
Тип: Zabbix агент (активный)
Ключ: eventlog[System,,"Error|Information","^Zabbix test event$"]
Тип информации: Журнал (лог)
Затем создаем триггер с текстом
{имя.хоста:eventlog[Application,,"Warning|Error|Failure",,,,].logseverity()}>1 and { имя.хоста:eventlog[Application,,"Warning|Error|Failure",,,,].nodata(60)}<>1

Чтобы упростить себе мониторинг журналов Система и Приложения достаточно импортировать на сервер шаблон YandexDisk\soft\zabbix_templates\zbx_eventlog_template.xml
Настройки-Шаблоны-Импорт

Затем выбираем файл и нажимаем импорт

После того, как шаблон добавлен, необходимо прикрепить его к нужному узлу сети. Проверить работу можно, зайдя в Мониторинг-Последние данные

Выбираем узел сети, или группу узлов и видим, какие ошибки и предупреждения поступали.

Проверить работу можно, выполнив на узле, в запущенной от имени администратора консоли PowerShell:
eventcreate /t error /id 999 /l system /so "Zabbix test event" /d "Test ERROR event log by Zabbix"
Которая создаст в журнале система тестовую ошибку.
Сбор информации из системных журналов является активной проверкой, а потому в настройках агента должно быть указано его полное имя и сервер для активных проверок.
Подробнее описано в разделе «Настройки Zabbix agent».
Почитать подробнее о настройке логов можно тут:
https://www.zabbix.com/documentation/3.0/ru/manual/config/items/itemtypes/zabbix_agent/win_keys


## Мониторинг нестандартных журналов событий Windows


Тип - активный, 
Ключ eventlog[Microsoft-Windows-Hyper-V-VMMS-Admin,,"Error|Critical",,,100,skip] 
тип информации: лог

Ищем ошибки, относящиеся к работе Windows Scheduler:

eventlog[Microsoft-Windows-TaskScheduler/Operational,,"Error|Critical",,,100,skip]
Основная проблема - что указать в качестве первого параметра ключа eventlog. Делаем так:
    открываем родной виндовый Event Viewer (например, right-click на MyComputer -> Manage, затем открываем System Tools -> Event Viewer);
    в Event Viewer-е находим нужный лог (например, в данном случае: Application and Services Logs -> Microsoft -> Windows -> TaskScheduler -> Operational), на нём RightClick -> Properties;
    в свойствах находим поле "Full Name" (самое первое), его содержимое и копипастим в качестве первого параметра ключа.

https://www.zabbix.com/forum/showthread.php?t=50565


## PostgreSQL


https://github.com/postgrespro/mamonsu
https://oc.postgrespro.ru/index.php/s/qu7YsFvOE55LdXo
https://raw.githubusercontent.com/postgrespro/mamonsu/master/packaging/conf/template_win32.xml


## Мониторинг необходимости перезагрузки Windows.



Бывает полезно знать, каким серверам требуется перезагрузка, а когда их много, то удобно об этом узнавать, не заходя на сервер.
Для мониторинга нам понадобится клиент, настроенный для активных проверок и соответствующий скрипт.
Скрипт берем в галерее технета по ссылке ниже.
https://gallery.technet.microsoft.com/scriptcenter/Get-PendingReboot-Query-bdb79542/view/Discussions
set-executionpolicy remotesigned
Полученный скрипт определяем в папку \zabbix_agent\scripts на клиенте, в конфигурации пишем:
UserParameter=Reboot.IsNedeed,powershell -NoProfile -ExecutionPolicy Bypass -command "$ErrorActionPreference = 'silentlycontinue';  $eval = get-pendingreboot; if ($eval.RebootPending) { Write-Host '1'; } else { Write-Host '0' };
На стороне Zabbix создаем элемент данных: 
Zabbix (активный)
ключ:
Reboot.IsNedeed


## Мониторинг Asterisk на примере Elastix (CentOS 7 x64)



Подразумевается, что агент у вас уже установлен. Если это не так – смотрите соответствующий раздел руководства.
Для того, чтобы настроить мониторинг нам нужно сделать несколько вещей:
0 добавить пользователю zabbix от чьего имени работает zabbix agent право запускать программы баз ввода пароля,
1 добавить в конфигурационный файл агента нужные параметры
3 скачать, импортировать и применить к нужному узлу шаблон.
Приступим. Открываем файл 
sudo nano /etc/sudoers
И в самый конец допишем:
zabbix ALL=(ALL) NOPASSWD:ALL
Теперь переходим к файлу конфигурации агента и внесем в него нужные изменения:
nano /etc/zabbix/conf/zabbix_agentd.conf
И добавляем в конце:
UserParameter=ast.pid,sudo -u zabbix sudo cat /var/run/asterisk/asterisk.pid
UserParameter=ast.uptime,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'core show uptime' | grep uptime | cut -f2 -d: | sed 's/ //g'
UserParameter=ast.reloadtime,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'core show uptime' | grep reload | cut -f2 -d: | sed 's/ //g'
UserParameter=ast.version,sudo -u zabbix sudo /usr/sbin/asterisk -V | cut -f2 -d' '

## Core Stats
## INFO: Active Calls is Buggy yet.
UserParameter=ast.callsdone,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'core show calls'| grep -i 'processed' | awk '{print $1}'

## IAX2 Stats

UserParameter=iax.status,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'iax2 show registry'|grep Registered |wc -l
UserParameter=iax.channels,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'iax2 show channels'|grep --text -i 'active IAX channel'|awk '{print $1}'

## SIP Stats

UserParameter=sip.status,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'sip show registry'|grep Registered |wc -l
UserParameter=sip.peersonline,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'sip show peers'|grep --text -i 'sip peers'|awk '{print $5}'
UserParameter=sip.peersoffline,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'sip show peers'|grep --text -i 'sip peers'|awk '{print $7}'
UserParameter=sip.peers,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'sip show peers'|grep --text -i 'sip peers'|awk '{print $1}'


## DNS Manager

UserParameter=dns.status,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'dnsmgr status' | grep 'DNS Manager' | awk '{print $NF}'
UserParameter=dns.entries,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'dnsmgr status' | grep 'Number of entries' | awk '{print $NF}'


## FAX Stats

UserParameter=fax.sessions,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'fax show stats' | grep 'Current Sessions' | awk '{print $NF}'
UserParameter=fax.transmits,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'fax show stats' | grep 'Transmit Attempts' | awk '{print $NF}'
UserParameter=fax.receive,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'fax show stats' | grep 'Receive Attempts' | awk '{print $NF}'
UserParameter=fax.done,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'fax show stats' | grep 'Completed' | awk '{print $NF}'
UserParameter=fax.fail,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'fax show stats' | grep 'Failed' | awk '{print $NF}'

### Parked Calls

UserParameter=ast.parkedcalls,sudo -u zabbix sudo /usr/sbin/asterisk -rvvvvvx 'parkedcalls show' | grep 'parked calls in total' | awk '{print $1}'

### Version information
### Version Info -- Edit this part for your own loss

UserParameter=ast.tribily.ver,sudo -u zabbix sudo echo ${VERSION}
Перезапускаем агент.
systemctl restart zabbix-agent
Скачиваем шаблон отсюда:
https://github.com/olindata/tribily-zabbix-templates/blob/master/App_Asterisk/Tpl_Tribily_App_Asterisk_Extended_v1.0.xml
Правой клавшей мыши по кнопке RAW, Сохранить объект как, далее Настройка-Шаблоны-Импорт шаблона, добавляем шаблон и применяем его к нужному узлу.
В последних данных увидим примерно следующее:


## Мониторинг доступности телефонов на примере аппаратов Yealink.

Поскольку SNMP доступен не для всех телефонов, а получать с них информацию необходимость есть, мы воспользуемся функцией Zabbix, которая называется Веб мониторинг.
Создаем узел сети с произвольным именем, без указания адреса, он нам нужен только для привязки к нему сценария.
После создания кликаем по пункту веб 
Далее выбираем создать веб сценарий

Даём ему имя, например, «Доступность», потом переходим на вкладку шаги, и нажимаем на кнопку добавить.
Вводим название шага, url, по которому необходимо проследовать, строку, которая там должна быть и код ответа. Нажимаем добавить, и еще раз добавить на основной страничке. 

А теперь по порядку. В поле url мы указываем адрес, по которому будет осуществлен переход во время проверки, требуемая строка это строка, которая должна быть на странице, а 200, это код ответа, если она присуствует.
Ссылка позволяет заходить на телефон не авторизируясь.
http://192.168.10.214/servlet?p=login&q=login&username=admin&pwd=admin
Далее нам необходимо создать триггер, который будет срабатывать, когда пропадет регистрация на телефоне.




Называем триггер, далее нажимаем добавить, и в появившемся окошке из элементов данных выбираем ошибку проверки, а в значении функции Последнее значение NOT N, и нажимаем вставить.
Выбираем высокую важность и нажимаем добавить.

Триггер означает, что, если проверка не удалась, и вернула любой код ответа, отличный от N (200, как мы его задали), это будет означать событие высокой важности с немедленным уведомлением заинтересованных лиц.

## Настройка оповещений по Email на примере  почты от yandex.



Переходим в раздел Администрирование(1)->Способы оповещений(2)->Email(3)

Настраиваем следующим образом:
Email
SMTP сервер smtp.yandex.ru
Порт SMTP сервера 465
SMTP helo yandex.ru
SMTP email адрес_для_отправки@домен.ру
Безопасность подключения SSL/TLS
Проверка SSL узла нет
Проверка SSL хоста нет
Аутентификация НетОбычный пароль
Имя пользователя monitor@ener-t.ru
Пароль ************
Активировано да
Ниже пример рабочих настроек.


## Мониторинг хоста Hyper-V

https://share.zabbix.com/virtualization/hyper-v/hyper-v-template-with-replication-state


https://github.com/ameiji/Zabbix-HyperV-Templates
Сначала импортируем шаблон Template_Windows_HyperV_VM_Guest.xml, потом Template_Windows_HyperV_Host.xml, поскольку они зависят друг от друга.
UserParameter=hyperv.discovery,powershell.exe -file "c:\zabbix_agent\scripts\zabbix-vm-perf.ps1"
UserParameter=hyperv.discoveryitem[*],powershell.exe -file "c:\zabbix_agent\scripts\zabbix-vm-perf.ps1" "$1" "$2"
UserParameter=hyperv.check[*],powershell.exe -file "c:\zabbix_agent\scripts\zabbix-vm-perf.ps1" "$1" "$2" "$3"
Set-ExecutionPolicy -ExecutionPolicy Bypass 
Мониторинг WSUS
https://github.com/zbx-sadman/wsus


## Мониторинг iLO


Для работы нам потребуется пакет freeipmi

'sudo apt-get install freeipmi'

/usr/sbin/ipmi-sensors -D LAN2_0 -h 192.168.10.36 -u пользователь_в_iLO -p пароль -l USER -W discretereading --no-header-output --quiet-cache --sdr-cache-recreate --comma-separated-output --entity-sensor-names

1 Скачиваем шаблон и скрипты отсюда: https://www.zabbix.com/forum/attachment.php?attachmentid=6874&d=1397131920
2 редактируем скрипты, вписываем туда наш логин и пароль от iLO

3 копируем скрипты в папку External scripts
cp ilo_discovery.pl ipmi_proliant.pl /usr/lib/zabbix/externalscripts/
4 Делаем их исполняемыми 
chmod +x /usr/lib/zabbix/externalscripts/ilo_discovery.pl
chmod +x /usr/lib/zabbix/externalscripts/ipmi_proliant.pl
и даем пользователю заббикс все права на них
chown zabbix:zabbix /usr/lib/zabbix/externalscripts/ilo_discovery.pl
chown zabbix:zabbix /usr/lib/zabbix/externalscripts/ipmi_proliant.pl
5 Создаем узел сети, в настройках интерфейса ничего не пишем, к нему применяем шаблон из архива, и во вкладке макросы ставим макрос {$ILO} равны IP интерфейса iLO


## Мониторинг S.M.A.R.T. в Windows x64

Для мониторинга используется набор утилит smarttools.
Скачиваем отсюда
https://sourceforge.net/projects/smartmontools/files/smartmontools/6.4/
Распаковываем в папку zabbix_agent\scripts\smarttools
Добавляем в конфигурационный файл строки:
#############SMARTMON
UserParameter=uHDD[*], for /F "tokens=10" %a in ('c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe -A $1 ^| find "$2"') do @echo %a
UserParameter=uHDD.health.[*], for /F "tokens=6" %a in ('c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe -H $1 ^| find "test"') do @echo %a
UserParameter=uHDD.model.[*],for /F "tokens=3*" %a in ('c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe -i $1 ^| find "Device Model"') do @echo %a %b
UserParameter=uHDD.sn.[*],for /F "tokens=3" %a in ('c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe -i $1 ^| find "Serial Number"') do @echo %a
UserParameter=uHDD.errorlog.[*], for /F "tokens=4" %a in ('c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe -l error $1 ^| find "ATA Error Count"') do @echo %a
UserParameter=uHDD.discovery,powershell -NoProfile -ExecutionPolicy Bypass -File "c:\zabbix_agent\scripts\smartctl-disks-discovery.ps1"
Обязательно перезапускаем службу после внесения изменений в конфигурационный файл. Так же должен быть разрешен запуск скриптов PowerShell. Делается это в консоли PowerShell запущенной от администратора командой:
Set-ExecutionPolicy -ExecutionPolicy Bypass

Отсюда берем шаблон и скрипт
https://github.com/v-zhuravlev/zbx-smartctl
Скрипт помещаем в папку c:\zabbix_agent\scripts\, а шаблон импортируем и применяем к нужным хостам. Вот и всё.
Для обеспечения работы в Windows x32 необходимо заменить в конфигурационном файле все вхождения bin64 на bin32. Как в строке ниже
c:\zabbix_agent\scripts\smarttools\bin64\smartctl.exe 

## Мониторим время окончания оплаты домена с помощью Zabbix

Для мониторинга нам понадобится скрипт и шаблон.
<details>
  <summary>checkdomainexpire.sh</summary>
 
```console
#!/bin/sh

# получаем имя домена
DOMAIN=$1

# получаем имя зоны
ZONE=`echo $DOMAIN | sed 's/\./ /' | awk '{ print $2 }'`

# получаем дату протухания домена
# Должна вернуться в формате ГГГГ-ММ-ДД (год-месяц-день)
case "$ZONE" in
ru|net.ru|org.ru|pp.ru)
DATE=`whois $DOMAIN | grep paid-till | awk '{ print $2 }' | sed 's/\./-/g'`
;;
com|net)
DATE=`whois $DOMAIN | grep "Registration Expiration Date:" | sed 's/Registrar Registration Expiration Date: //g;s/T/ /g' | awk '{ print $1 }'`
;;
org)
DATE=`whois $DOMAIN | grep "Registry Expiry Date:" | sed 's/Registry Expiry Date: //g;s/T/ /g' | awk '{ print $1 }'`
;;
*)
DATE="$(whois $DOMAIN | awk '/[Ee]xpir.*[Dd]ate:/ || /[Tt]ill:/ || /expire/ {print $NF; exit;}')"
if test -z "$DATE"; then
#Отсутствует информация в Whois для домена
echo "-1"
continue
fi
esac

# считаем дни и выводим
expr \( `date --date="$DATE" +%s` - `date +%s` \) / 60 / 60 / 24
```
</details>

Скрипт кладем в каталог по умолчанию для внешних скриптов, который указан в конфигурации сервера zabbix, вот строчка по умолчанию в zabbix-server.conf
```console
ExternalScripts=/usr/lib/zabbix/externalscripts/checkdomainexpire.sh
```
Теперь выдаем права и делаем его исполняемым:
```console
chown zabbix:zabbix /usr/lib/zabbix/externalscripts/checkdomainexpire.sh
chmod +x /usr/lib/zabbix/externalscripts/checkdomainexpire.sh
```
Теперь осталось в агента добавить свой параметр:
```console
nano /etc/zabbix/zabbix_agentd.conf
UserParameter=domainexpire[*],/usr/lib/zabbix/externalscripts/checkdomainexpire.sh $1
service zabbix-agent reload
```
Шаблон берем тут

https://sysadmin-note.ru/monitorim-vremya-okonchaniya-oplaty-domena-s-pomoshhyu-zabbix/
Привязываем шаблон к узлу сети и создаем макрос
{$DOMAINNAME} со значением имя.сайта

## Работа с SNMP. Общие сведения.



Значения OID удобно получаться с помощью специальной утилиты snmpget, нижу идут примеры использования:
snmpget -v2c -cpublic snmp_agent_Ip_address OID
где:
-v2c – версия протокола SNMP;
-cpublic – community имя. В нашем случае public;
snmp_agent_Ip_address – адрес узла сети;
OID – ключ, значение которого необходимо получить. 
Например, запрос вида: 
snmpget -v2c -cpublic prn-udo.et.local .1.3.6.1.2.1.1.5.0
Вернет нам модель принтера:



## Работа с Mikrotik.



Через winbox переходим "IP"-"SNMP"

Здесь устанавливаем флаг "Enabled", тем самым включая SNMP на маршрутизаторе. Далее нажимаем кнопку "Сommunities" и выбираем уже созданный по умолчанию community "public", и исправляем его на свой. Так же для безопасности можно указать IP адреса с которых можно подключаться по SNMP.
 
Скачиваем отсюда шаблон
https://github.com/andyzasl/zabbix-templates/tree/master/mikrotik
Импортируем и применяем к узлу сети, который мы создали для нашего Mikrotik.
Работа с SNMP. Принтеры.

Kyocera
Так напишу для истории, кому пригодится.
1.3.6.1.4.1.1347.43.10.1.1.12.1.1 - кол-во копий по счетчику принтера
1.3.6.1.2.1.43.11.1.1.8.1.1 - уровень тонера (Х)
1.3.6.1.2.1.43.11.1.1.9.1.1 - процент использования в тонере (Y)
Соответственно удобоваримый вид сколько осталось тонера Z= Y*100/X
Ну и еще немного для комплекта
1.3.6.1.2.1.25.3.2.1.3.1 - модель принтера
1.3.6.1.4.1.1347.40.10.1.1.5.1 - сетевое имя
1.3.6.1.2.1.25.3.5.1.2.1 - состояние принтера (нормальное %00, загрузить кассету B ...)
1.3.6.1.4.1.1347.43.5.4.1.5.1.1 - версия прошивки
1.3.6.1.4.1.1347.43.5.1.1.28.1 - серийный номер

Ricoh
Page Count - .1.3.6.1.2.1.43.10.2.1.4.1.1
Printer Description - .1.3.6.1.2.1.25.3.2.1.3.1
Printer Status - .1.3.6.1.2.1.25.3.5.1.1.1
Toner Estimated Capacity % - .1.3.6.1.4.1.367.3.2.1.2.24.1.1.5.1

По тонеру - показывает значение
-3 при полном картридже
-100 при низком уровне тонера
0 при необходимости замены тонера

Устранение ошибок при сборке Zabbix

При сборке Zabbix из исходных кодов, возникают ошибки с зависимостями.
Решение для устранения зависимостей под Debian:

Flag: --with-mysql
Error: configure: error: MySQL library not found
Fix: sudo apt-get install libmysqlclient-dev

Flag: --with-jabber
Error: checking for IKSEMEL... configure: error: Jabber library not found
Fix: sudo apt-get install libiksemel-dev

Flag: --with-libcurl
Error: configure: error: Curl library not found
Fix: sudo apt-get install libcurl4-openssl-dev

Flag: --with-net-snmp
Error: configure: error: Not found NET-SNMP library
Fix: sudo apt-get install libsnmp-dev
Flag: --with-openipmi
Error: configure: error: Invalid OPENIPMI directory - unable to find ipmiif.h
Fix: sudo apt-get install libopenipmi-dev

Zabbix 2.2.0

Flag: --with-libxml2
Error: configure: error: LIBXML2 library not found
Fix: sudo apt-get install libxml2-dev

Flag: --with-ssh2
Error: configure: error: SSH2 library not found
Fix: sudo apt-get install libssh2-1-dev
Типичные ошибки и их решение.

При установке из пакетов, для создания структуры БД, выполните следующие команды:
cd /usr/share/doc/zabbix-server-mysql
zcat create.sql.gz | mysql -uzabbix -p zabbix

При получении ошибок вида:
Cannot adopt OID in UCD-SNMP-MIB: laIndex ::= { laEntry 1 }
……………………………………………………………………………………………….
Cannot adopt OID in UCD-SNMP-MIB: laErrorFlag ::= { laEntry 100 }

Устанавливаем snmp-mibs-downloader
apt-get install snmp-mibs-downloader

Zabbix icmp pinger processes more than 75% busy - говорит нам о том, что процесс pinger перегружен. И если продолжить добавлять узлы на мониторинг, то вскоре данные просто перестанут успевать обрабатываться.
Исправить ситуацию можно увеличив кол-во pinger процессов. Для этого необходимо поправить конфигурацию zabbix сервера:

nano /etc/zabbix/zabbix_server.conf
находим строчку:
# StartPingers=1
Установим значение StartPingers в 5.
StartPingers=5
Сохраняем конфигурацию и перезагружаем zabbix сервер
service zabbix-server restart

sudo: no tty present and no askpass program specified
Суть проблемы: у пользователя zabbix, из-под которого в системе выполняется этот скрипт, не хватает прав на запуск через sudo без пароля команды nmap.
Решение: Правим файл /etc/sudoers, дописывая нужные разрешения:
nano /etc/sudoers
zabbix ALL=(ALL) NOPASSWD: /usr/sbin/smartctl

Если машина долгое время была выключена и показывает неправильное время, нужно проверить и при необходимости установить правильный часовой пояс.
dpkg-reconfigure tzdata

## Используемые материалы:


Zabbix Wiki, основной источник информации

https://www.zabbix.com/documentation/3.4/ru/start

Родной форум на русском

https://www.zabbix.com/forum/forumdisplay.php?f=21

Неофициальный, но очень полезный сайт:

https://www.zabbix.org/wiki/Main_Page

Поиск шаблонов:

https://share.zabbix.com/
http://monitoringartist.github.io/zabbix-searcher/
https://github.com/thecamels/zabbix

Шаблон для WSUS

https://github.com/zbx-sadman/wsus

Инсталляторы агентов для Windows 32/64

http://www.suiviperf.com/zabbix/index.php

Cisco template generator

http://ross.vc/cisco_tpl/

Мониторинг S.M.A.R.T.

https://github.com/v-zhuravlev/zbx-smartctl

Мониторим всё: расширение агентов Windows и Linux при помощи скриптов

https://habrahabr.ru/company/zabbix/blog/196218/
http://www.bog.pp.ru/work/zabbix.html

Настройка Asterisk:

https://www.zabbix.com/forum/showthread.php?t=41191
https://github.com/olindata/tribily-zabbix-templates/blob/master/App_Asterisk/Tpl_Tribily_App_Asterisk_Extended_v1.0.xml

SNMP

http://www.k-max.name/linux/snmp-protocol/
https://github.com/macrokernel/zabbix-snmpbuilder/tree/2.4.7
https://www.zabbix.org/wiki/Snmp_builder
https://habrahabr.ru/post/85156/

Мониторинг журналов безопасности:

https://habrahabr.ru/post/215509/

Мониторинг хоста Hyper-V
https://share.zabbix.com/virtualization/hyper-v/hyper-v-template-with-replication-state
https://github.com/ameiji/Zabbix-HyperV-Templates

Рекомендации по «производству» шаблонов

https://www.zabbix.org/wiki/Docs/template_guidelines
http://neskripit.ru/blogs/blogs-all/viewpost/724
http://saradmin.ru/?p=1542

Мониторинг свичей D-Link

https://share.zabbix.com/official-templates/template-modules-pack
https://share.zabbix.com/official-templates/network-devices/template-net-d-link-des-dgs-switch-snmpv2


Работа с сервисами в CentOS

http://www.putorius.net/2014/07/start-services-on-boot-in-red-hat-7-or.html

Мониторинг веб сайта

http://backnet.ru/2012/01/28/zabbix-kak-nastroit-monitoring-web-sayta/

Мониторинг состояния Windows Server (необходимость перезагрузки)

https://social.technet.microsoft.com/Forums/ru-RU/a72ebb02-c034-4bdc-8a43-72d2ab9bcb05/-?forum=WS8ru
https://gallery.technet.microsoft.com/scriptcenter/Get-PendingReboot-Query-bdb79542

PostgreSQL

https://github.com/lesovsky/zabbix-extensions/tree/master/files/postgresql

Мониторинг окончания домена

https://sysadmin-note.ru/monitorim-vremya-okonchaniya-oplaty-domena-s-pomoshhyu-zabbix/

Before I was trying to use Microsoft-Windows-Hyper-V-VMMS/Admin which is the name on Event Viewer, but on C:\windows\system32\WinEvt\Logs the log name is Microsoft-Windows-Hyper-V-VMMS-Admin which works !!!
всем привет! Ребят, подскажите как можно затягивать информацию из текстового файла в zabbix? Сам файл создается скриптом PS(туда сваливаются логи некоторых журналов). Пробовал вот таким ключом на zabbix-сервере: Logrt["D:\zabbix\test.txt"], но в логе агента виндового пишет: active check "Logrt["D:\zabbix\test.txt"]" is not supported
решил проблему с логом, если кому интересно, то делается это так: logrt[путь к логу,,ANSI], правда, забыл написать о решении=)
