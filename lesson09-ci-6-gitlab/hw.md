# Домашнее задание к занятию "09.05 Gitlab"

## Подготовка к выполнению

1. Необходимо [зарегистрироваться](https://about.gitlab.com/free-trial/)
2. Создайте свой новый проект
3. Создайте новый репозиторий в gitlab, наполните его [файлами](./repository)
4. Проект должен быть публичным, остальные настройки по желанию

## Основная часть

```python
#check current message
vagrant@vagrant:~$ docker pull registry.gitlab.com/kiselyova-ma/new-project/python-api.py:latest
latest: Pulling from kiselyova-ma/new-project/python-api.py
Digest: sha256:2712c13eff0173f46e6760e148d145ef7caf860df0c2b7d2b147243630adb672
Status: Image is up to date for registry.gitlab.com/kiselyova-ma/new-project/python-api.py:latest
registry.gitlab.com/kiselyova-ma/new-project/python-api.py:latest
vagrant@vagrant:~$ docker run -p 5290:5290 -d registry.gitlab.com/kiselyova-ma/new-project/python-api.py
vagrant@vagrant:~$ curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Already started"}
vagrant@vagrant:~$ docker stop fd16c4b01863
fd16c4b01863
#new feature branch
vagrant@vagrant:~$ docker pull registry.gitlab.com/kiselyova-ma/new-project/python-api.py:feature
feature: Pulling from kiselyova-ma/new-project/python-api.py
Digest: sha256:e6760e148d1453b0206ca4e16cdcfd16c4b0186357e8d60bd39f527e8z48e863
Status: Image is up to date for registry.gitlab.com/kiselyova-ma/new-project/python-api.py:feature
registry.gitlab.com/kiselyova-ma/new-project/python-api.py:feature
vagrant@vagrant:~$ docker run -p 5290:5290 -d registry.gitlab.com/kiselyova-ma/new-project/python-api.py:feature
4b1c21c83acb93ab6b36f19c8260a6a4250fa5b2f027f80ce598ab0133c83ac4
vagrant@vagrant:~$ curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Running"}
vagrant@vagrant:~$ docker stop 4b1c21c83acb
4b1c21c83acb
#check new method response
vagrant@vagrant:~$ docker run -p 5290:5290 -d registry.gitlab.com/kiselyova-ma/new-project/python-api.py:latest
vagrant@vagrant:~$ curl localhost:5290/get_info
{"version": 3, "method": "GET", "message": "Running"}
```