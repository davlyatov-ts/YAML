# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"<br>
________________
## Обязательная задача 1<br>
### Мы выгрузили JSON, который получили через API запрос к нашему сервису:<br>
```
   { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
### Нужно найти и исправить все ошибки, которые допускает наш сервис<br>
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            },
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
### Не хватало в строке с ip адресом ковычек.
___________________________
## Обязательная задача 2<br>

В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP.<br>
К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов,<br>
описывающих наши сервисы. Формат записи JSON по одному сервису: { "имя сервиса" : "его IP"}.<br>
Формат записи YAML по одному сервису: - имя сервиса: его IP. Если в момент исполнения скрипта меняется IP<br>
у сервиса - он должен так же поменяться в yml и json файле.<br>
```
#!/usr/bin/env python3

import socket
import os
import pickle
import json
import yaml

def check_dns(hosts):
    dictionary = {}
    for hostname in hosts:
        dictionary[hostname] = socket.gethostbyname(hostname)
    return(dictionary)

def write_to_pickle(data, filename):
    with open(filename,'wb') as f:
        pickle.dump(data, f)

def write_to_json(data, filename):
    with open(filename,'w') as js:
        js.write(json.dumps(data))

def write_to_yaml(data, filename):
    with open(filename,'w') as ym:
        ym.write(yaml.dump(data, default_flow_style=False))

def read_from_file():
    with open('data.pickle','rb') as f:
        data = pickle.load(f)
        return data

def delimeter():
    print("="*50)

SITES = ("drive.google.com", "mail.google.com", "google.com")
PICKLE_FILE = "data.pickle"
JSON_FILE = "data.json"
YAML_FILE = "data.yml"


delimeter()

if os.path.isfile(PICKLE_FILE):

    old_dict = read_from_file()
    new_dict = check_dns(SITES)

    for host in SITES:
        if old_dict[host] == new_dict[host]:
            print('{:>11} {:>0} - {:>10}'.format("",host, old_dict[host]))
        else:
            print('[ERROR] {:>20} IP mismatch [OLD]: {:>10} [NEW]: {:>10}'.format(host, old_dict[host], new_dict[host]))
            write_to_json(new_dict, 'data.json')
            write_to_yaml(new_dict, 'data.yaml')
    delimeter()
else:
    temp = check_dns(SITES)
    print(temp)
    write_to_pickle(temp, PICKLE_FILE)
    print("File 'data.pickle' is created.")
    print("This file is template fo tests")
```
JSON файл
```
➜  ~ cat data.json 
{"drive.google.com": "173.194.222.194", "mail.google.com": "173.194.73.18", "google.com": "74.125.205.139"}% 
```
YAML файл
```
➜  ~ cat data.yaml 
drive.google.com: 173.194.222.194
google.com: 74.125.205.139
mail.google.com: 173.194.73.18
```
______________________________

