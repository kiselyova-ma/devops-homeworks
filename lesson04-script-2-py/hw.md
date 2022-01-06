# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  | Никакое, так как `a` и `b` разного типа `int()` и `str()` |
| Как получить для переменной `c` значение 12?  | `c = f'{a}'+b `|
| Как получить для переменной `c` значение 3?  | `c = a + int(b)` |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/PycharmProjects/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
# is_change = False - we don't need it
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
#        break - interrupts for
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\marii\Documents\Netology\devops-homeworks\Scripts\python.exe C:/Users/marii/PycharmProjects/devops-homeworks/lesson04-script-2-py/hw.py
01-intro-01/netology.jsonnet
01-intro-01/netology.md
01-intro-01/netology.sh
01-intro-01/netology.tf
01-intro-01/netology.yaml
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys

cwd = os.getcwd()

if len(sys.argv)>=2:
    cwd = sys.argv[1]
bash_command = ["cd "+cwd, "git status 2>&1"]

result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print(cwd+' is not a local repo')
    if result.find('modified') != -1:
        results = result.replace('\tmodified: ', '').replace(' ', '').replace(r'/', '\\')
        print(cwd+'\\'+results)
```

### Вывод скрипта при запуске при тестировании:
```
C:\Users\marii\PycharmProjects\sysadm-homeworks\01-intro-01\netology.jsonnet
C:\Users\marii\PycharmProjects\sysadm-homeworks\01-intro-01\netology.md
C:\Users\marii\PycharmProjects\sysadm-homeworks\01-intro-01\netology.sh
C:\Users\marii\PycharmProjects\sysadm-homeworks\01-intro-01\netology.tf
C:\Users\marii\PycharmProjects\sysadm-homeworks\01-intro-01\netology.yaml
```

## Обязательная задача 4
1. Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
##!/usr/bin/env python3

import socket as s
import time as t
import datetime as dt

# set variables
i = 1
wait = 5
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init = 0

print('Checking IPs for the following servers:')
print(srv)

while 1==1 :
  for host in srv:
    ip = s.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        print(str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [ERROR] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip)
      srv[host]=ip
  t.sleep(wait)
```

### Вывод скрипта при запуске при тестировании:
```
Checking IPs for the following servers:
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
2022-01-06 23:41:00 [ERROR] drive.google.com IP mistmatch: 0.0.0.0 74.125.205.194
2022-01-06 23:41:00 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 64.233.161.19
2022-01-06 23:41:00 [ERROR] google.com IP mistmatch: 0.0.0.0 74.125.131.101
```