# openvpn-install-script-debian-ubuntu
OpenVPN install script Debian and Ubuntu. Настройка сервера OpenVPN в Debian и Ubuntu рабочий скипт.

Debian / Ubuntu

Debian >= 10

Ubuntu >= 16.04

**Тестировано на Debian 10**

**Конфиг совместим с более ранними версиями OpenVPN до версии 2.4**

Покупаешь сервак VPS

На сервак закидываешь **vpn.sh**

Далее на файл ставишь права 777

```bash
chmod 777 vpn.sh
```

Запускаешь установку так

```bash
./vpn.sh
```

Генерация ключей занимает 15 минут.

После выполнения скрипта, на сервере будет сгененрирован клиентский файл

> /root/client000.ovpn

Скопировать на комп через ssh можно так (111.111.111.111 замени на адрес сервера)

```bash
scp root@111.111.111.111:/root/client000.ovpn vpn.ovpn
```

Запуск на компе

```bash
openvpn --config vpn.ovpn
```

Логирование отключено на серваке.


## Настройка файрвола

Чтобы трафик не шел мимо VPN

Замени интерфейс eth0 на свой, узнать имя так

```bash
ifconfig
```
Замени 111.111.111.111 на IP сервака

```bash
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -s 111.111.111.111 -p tcp -m tcp --sport 443 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -i tun0 -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -j REJECT --reject-with icmp-host-prohibited
iptables -A FORWARD -j REJECT --reject-with icmp-host-prohibited
iptables -A OUTPUT -o lo -j ACCEPT
iptables -A OUTPUT -o tun0 -j ACCEPT
iptables -A OUTPUT -d 111.111.111.111 -o eth0 -p tcp -m tcp --dport 443 -j ACCEPT
iptables -A OUTPUT -j REJECT --reject-with icmp-host-prohibited

ip6tables -A INPUT -j DROP
ip6tables -A FORWARD -j DROP
ip6tables -A OUTPUT -j DROP
```

После ввода команд, можешь проверить что все ввел верно

```bash
iptables-save
ip6tables-save
```

Теперь сохрани, чтобы правила не стерлись после перезагрузки компа (yes,yes при вопросе)

```bash
apt-get install iptables-persistent
```

## Доп инфо

У меня ты так же найдешь скрипт как настроить двойного впн

Комп - vpn -vpn - Интернет

Одинарный ВПН не подходит для анонимности, только для обхода блокировок.

Если ты используешь поверх него или перед ним Тор - это уже вполне рабочее решение для анонимнных трипов.

