# Домашнее задание к занятию "6.5. Elasticsearch"

## Задача 1

В этом задании вы потренируетесь в:
- установке elasticsearch
- первоначальном конфигурировании elastcisearch
- запуске elasticsearch в docker

Используя докер образ [centos:7](https://hub.docker.com/_/centos) как базовый и 
[документацию по установке и запуску Elastcisearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html):



- составьте Dockerfile-манифест для elasticsearch
```bash
RUN yum install java-11-openjdk -y
RUN yum install wget -y

RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.1.0-linux-x86_64.tar.gz \
    && wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.1.0-linux-x86_64.tar.gz.sha512
RUN yum install perl-Digest-SHA -y
RUN shasum -a 512 -c elasticsearch-8.1.0-linux-x86_64.tar.gz.sha512 \
    && tar -xzf elasticsearch-8.1.0-linux-x86_64.tar.gz \
    && yum upgrade -y

ADD elasticsearch.yml /elasticsearch-8.1.0/config/
ENV JAVA_HOME=/elasticsearch-8.1.0/jdk/
ENV ES_HOME=/elasticsearch-8.1.0
RUN groupadd elasticsearch \
    && useradd -g elasticsearch elasticsearch

RUN mkdir /var/lib/logs \
    && chown elasticsearch:elasticsearch /var/lib/logs \
    && mkdir /var/lib/data \
    && chown elasticsearch:elasticsearch /var/lib/data \
    && chown -R elasticsearch:elasticsearch /elasticsearch-8.1.0/
RUN mkdir /elasticsearch-8.1.0/snapshots &&\
    chown elasticsearch:elasticsearch /elasticsearch-8.1.0/snapshots

vagrant@vagrant:~$ curl --cacert http_ca.crt -u elastic https://localhost:9200
Enter host password for user 'elastic':
{
  "name" : "netology",
  "cluster_name" : "netology_test",
  "cluster_uuid" : "-BjNMrjXJVHCUwOJNdOnfg",
  "version" : {
    "number" : "8.1.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f515d06b0fa07bb18d8202035e739a494ce9bc53a",
    "build_date" : "2022-03-30T10:03:00.256425233Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}



```

- соберите docker-образ и сделайте `push` в ваш docker.io репозиторий
```bash
$  docker push kiselyova/elastic:0.1
```

[Ссылка на репозиторий](https://hub.docker.com/r/kiselyova/elastic)

## Задача 2

В этом задании вы научитесь:
- создавать и удалять индексы
- изучать состояние кластера
- обосновывать причину деградации доступности данных

Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `elasticsearch` 3 индекса, в соответствии со таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

Получите список индексов и их статусов, используя API и **приведите в ответе** на задание.
```bash
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cat/indices?v
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 rih709MfTQmv7nKSdmSiFd   1   0          0            0       225b           225b
yellow open   ind-3 s7U4SkepPQdGih709nsfrS   4   2          0            0       225b           225b
yellow open   ind-2 TQmv7s4KSdKfgnso34w94g   2   1          0            0       450b           450b
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cluster/health/ind-1?pretty
{
  "cluster_name" : "netology_test",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cluster/health/ind-2?pretty
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 2,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 47.368421052631575
}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cluster/health/ind-3?pretty
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 4,
  "active_shards" : 4,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 8,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 47.368421052631575
}  
```

Получите состояние кластера `elasticsearch`, используя API.
```bash
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cluster/health/?pretty=true
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 9,
  "active_shards" : 9,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 47.368421052631575
  
```

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow? \
Жёлтые индексы - реплика \
Жёлтый кластер - нет реплики.


Удалите все индексы.
```bash
  [elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X DELETE https://localhost:9200/ind-1
{  "acknowledged" : true}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X DELETE https://localhost:9200/ind-2
{  "acknowledged" : true}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X DELETE https://localhost:9200/ind-3
{  "acknowledged" : true}
```

## Задача 3

В данном задании вы научитесь:
- создавать бэкапы данных
- восстанавливать индексы из бэкапов

Создайте директорию `{путь до корневой директории с elasticsearch в образе}/snapshots`.

Используя API [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
данную директорию как `snapshot repository` c именем `netology_backup`.

**Приведите в ответе** запрос API и результат вызова API для создания репозитория.

Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.

[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `elasticsearch`.

**Приведите в ответе** список файлов в директории со `snapshot`ами.

Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.

[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `elasticsearch` из `snapshot`, созданного ранее. 

**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:
- возможно вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `elasticsearch`

```bash
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X POST https://localhost:9200/_snapshot/netology_backup -H 'Content-Type: application/json' -d'{"type": "fs", "settings": { "location":"elasticsearch-8.1.0/snapshots" }}'
{  "acknowledged" : true}
[elasticsearch@8bf54685106e /]$  curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_snapshot/netology_backup
{
  "netology_backup" : {
    "type" : "fs",
    "settings" : {
      "location" : "elasticsearch-8.1.0/snapshots"
    }
  }
}

[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X PUT https://localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_replicas": 0,  "number_of_shards": 1 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cat/indices?v
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test  Lerpg-rpYEegvi_eOGk2wl   1   0          0            0       225b           225b

[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X PUT https://localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true
{"snapshot":{"snapshot":"elasticsearch","uuid":"F9WqDX7p]g;aJw8wQQG","repository":"netology_backup","version_id":8010099,"version":"8.1.0","indices":[".geoip_databases",".security-7","test"],"data_streams":[],"include_global_state":true,"state":"SUCCESS","start_time":"2022-03-16T11:52:14.448Z","start_time_in_millis":1647438734448,"end_time":"2022-03-16T11:52:16.264Z","end_time_in_millis":1647438736264,"duration_in_millis":1816,"failures":[],"shards":{"total":3,"failed":0,"successful":3},"feature_states":[{"feature_name":"geoip","indices":[".geoip_databases"]},{"feature_name":"security","indices":[".security-7"]}]}}


F9WqDX7p]g;aJw8wQQG

[elasticsearch@8bf54685106e /]$ ls -la /elasticsearch-8.1.0/snapshots/elasticsearch-8.1.0/snapshots
total 44
drwxr-xr-x 3 elasticsearch elasticsearch  4096 Mar 30 11:12 .
drwxr-xr-x 3 elasticsearch elasticsearch  4096 Mar 30 11:06 ..
-rw-r--r-- 1 elasticsearch elasticsearch  1098 Mar 30 11:12 index-0
-rw-r--r-- 1 elasticsearch elasticsearch     8 Mar 30 11:12 index.latest
drwxr-xr-x 5 elasticsearch elasticsearch  4096 Mar 30 11:12 indices
-rw-r--r-- 1 elasticsearch elasticsearch 18406 Mar 30 11:12 meta-F9WqDX7p]g;aJw8wQQG.dat
-rw-r--r-- 1 elasticsearch elasticsearch   391 Mar 30 11:12 snap-F9WqDX7p]g;aJw8wQQG.dat

[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cat/indices?v
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X PUT https://localhost:9200/test-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_replicas": 0,  "number_of_shards": 1 }}'
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cat/indices?v
health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 e9Fk7MNEgUyDRaCVCS3L3N   1   0          0            0       225b           225b

[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X POST https://localhost:9200/_snapshot/netology_backup/elasticsearch/_restore -H 'Content-Type: application/json' -d'{"include_global_state":true}'
{
  "accepted" : true
}
[elasticsearch@8bf54685106e /]$ curl -k -u elastic:f60oq51IWBG2b40KvDSSw -X GET https://localhost:9200/_cat/indices?v
health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 e9Fk7MNEgUyDRaCVCS3L3N   1   0          0            0       225b           225b
green  open   test   whSX8vuEtG5upxMshTdDux   1   0          0            0       225b           225b
```