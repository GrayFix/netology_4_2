# 4.2. Использование Python для решения типовых DevOps задач

1. Значение переменной с
  - если брать код из примера, у меня получилась ошибка "TypeError: unsupported operand type(s) for +: 'int' and 'str'"
  - Чтобы получить 12 как целое число, то c = int(str(a) + b)
  - Чтобы получить значение 3, c = a + int(b)

2. Доработанный скрипт. Убран break, путь к папке вынесен в переменную, выводится полный путь.
```python
#!/usr/bin/env python3

import os

git_path="~/netology/sysadm-homeworks"

bash_command = ["cd "+git_path, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '').replace('#','')
        print(os.path.join(git_path,prepare_result))
```

3. Доработанный вариант скрипта. По умолчанию работает с папкой из предыдущего примера. Но можно задать свою.
```python
#!/usr/bin/env python3

import os
import argparse

parser = argparse.ArgumentParser(description='Get Git modified files')
parser.add_argument(
    '-g',
    type=str,
    default='/home/vlad_adm@ugmk.com/1/2',
    help='provide Git base path (default: /home/vlad_adm@ugmk.com/1/2)'
    )
args = parser.parse_args()

bash_command = ["cd "+args.g, "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '').replace('#','')
        print(os.path.join(args.g,prepare_result))
```

4. Скрипт проверки сервиса. Для хранения самих сервисом и их ip адресов испольузется json файл 4_config.json. Доступность самих сайтов не проверяется, проверяется только IP адрес.
Пример json файла настройки:
```json
{
  "drive.google.com": "173.194.222.194",
  "mail.google.com": "173.194.222.18",
  "google.com": "74.125.131.113"
}
```

Скрипт:
```python
#!/usr/bin/env python3

import os
import socket
import json

# JSON файл для хранения хостов и их IP адресов, формат ключ/значение
conf_file="4_config.json"

with open(conf_file) as json_data_file:
    conf = json.load(json_data_file)

# Перебор всех хостов из файла конфигурации и опредление их IP адресов
for host, ip in conf.items():
    new_ip=socket.gethostbyname(host)

    # Если IP адреса отличаются, выводится предупреждение и сохранение нового IP адреса в файле конфигурации
    if (ip != new_ip):
        print ('[ERROR] {} IP mismatch: {} {}'.format(host,ip,new_ip))
        conf[host]=new_ip

# Печать всех сопоставлений хоста и IP адреса
# Сделано в отдельном цикле чтобы отделить ошибки от полезной информации
for host, ip in conf.items():
    print('{} - {}'.format(host,ip))

# Пишем в файл, параметр ident дает нам человекочитаемый формат
with open(conf_file, "w") as json_data_file:
    json.dump(conf, json_data_file, indent=2)
```

5. Пример скрипта для дополнительного задания. [Ссылка](https://github.com/GrayFix/netology_4_2/blob/main/5.py)
