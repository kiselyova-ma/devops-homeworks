# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"

1. Fixed quotation marks next to ip
````json
{ "info" : "Sample JSON output from our service\t",
        "elements" :[ 
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
````
2.### Ваш скрипт:

```python
##!/usr/bin/env python3

import socket as s
import time as t
import datetime as dt
import json
import yaml

# set variables
i = 1
wait = 5
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init = 0
path = "C:\Users\marii\PycharmProjects\python"
log = "C:\Users\marii\PycharmProjects\python\log.log"

print('Checking IPs for the following servers:')
print(srv)

while True:
    for host in srv:
        mismatch = False
        ip = s.gethostbyname(host)
        if ip != srv[host]:
            if i == 1 and init != 1:
              mismatch = True
              with open(log, 'a') as fl: #log
                print(
                    str(dt.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) + ' [ERROR] ' + str(host) + ' IP mistmatch: ' +
                    srv[host] + ' ' + ip)
              with open(path + host + ".json", 'w') as jsf: #json
                json_data = json.dumps([{host: ip}])
                jsf.write(json_data)
                # yaml
              with open(path + host + ".yaml", 'w') as ymf: #yml
                yaml_data = yaml.dump({host: ip})
                ymf.write(yaml_data)
            srv[host] = ip
    t.sleep(wait)
```