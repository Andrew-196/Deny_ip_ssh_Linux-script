<h3>Запрет IP подключенного по SSH на Linux</h3>

# Как использовать 
После запуска скрипта будет выведен IP host, где находится данный скрипт и username

![2025-05-30_13-59-07](https://github.com/user-attachments/assets/a258732f-2885-4751-8e8e-11e3bbb9df29)
---
Ниже будет описано про статус SSH и какие процессы запущены (pid)

![2025-05-30_14-02-01](https://github.com/user-attachments/assets/f6c9bd68-98d2-4aed-9efc-9bac32e97ae1)
---
Будет предложено какой процесс `pid` проверить через `netstat`. Например `5749`.

И выведет к какому IP подключен и с какого IP происходит подключение. И мы может либо запретить IP с которого подключаются `192.168.0.104` либо не запрещать `(y/n)`

![2025-05-30_14-05-21](https://github.com/user-attachments/assets/93a42201-a981-43c8-882b-119d211fb6d8)
---
При выборе `запретить`, нужно вручную ввести IP
После ввода появится сообщение что данный IP запрещен и добавлен в файл `hosts.deny`

![2025-05-30_14-09-43](https://github.com/user-attachments/assets/c4772d05-342d-4194-9133-e65a57bf4261)

# Полное выполнение

![Запрет IP подключение ssh (1)](https://github.com/user-attachments/assets/130753f8-f39e-4e41-9661-1692b3e3eee6)

# Полный код

```#!/bin/bash
echo "hello $USER"
username="$USER"
host_IP="$(hostname -I)"
echo "your host ip - $host_IP"
find_bash="/home/andrew/bash.sh"
echo "Ты тут - $(pwd) ---- bash.sh находится в $find_bash ----"

if [ "$username" == "andrew" ]; then
 echo "username - andrew"
else
 echo "you are not andrew"
fi

echo "----------"

sysctl="$(systemctl status ssh | grep -e Active -e Main.PID)"
process_ssh="$(ps -ef | grep ssh)"
echo "STATUS SSH"
echo "$sysctl"

echo "----------"

echo "SATUS PROCESS SSH"
echo "$process_ssh"

echo "----------"

read -p "Напишите PID process для проверки в netstat - " number

netstat="$(sudo netstat -tnp | grep $number)"
echo "$netstat"


read -p "Запретить данный  ip-address? (да/нет) - " deny
if [[ "$deny" == "да" || "$deny" == "ДА" || "$deny" == "yes" || "$deny" == "YES" ]]; then

 read -p "Введите ip-address - " ip
 echo "sshd:$ip" | sudo tee -a /etc/hosts.deny
 echo "$deny"
 echo "Запрещён IP и добавлен в hosts.deny - $ip"

else
 echo "Вы отменили запрет IP"
fi
#ДОБАВЛЕНИЕ БЕЗ ПРОВЕРКИ КОРРЕКТНОГО ВВОДА IP```

