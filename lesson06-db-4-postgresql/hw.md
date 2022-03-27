# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
```bash
vagrant@server1:~$ docker pull postgres:13
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/images/create?fromImage=postgres&tag=13": dial unix /var/run/docker.sock: connect: permission denied
vagrant@server1:~$ sudo docker pull postgres:13
13: Pulling from library/postgres
ae13dd578326: Already exists
723e40c35aaf: Already exists
bf97ae6a09b4: Already exists
2c965b3c8cbd: Already exists
c3cefa46a015: Already exists
64a7315fc25c: Already exists
b9846b279f7d: Already exists
ed988fb8e7d9: Already exists
9717ee6c56d3: Pull complete
f3e6602d7986: Pull complete
5e7f2e9f5694: Pull complete
647e19f05c1a: Pull complete
8c44671ffda3: Pull complete
Digest: sha256:4b531ee951d9e30b5d4e62d0ba87a1f020a2343ed6d28add4e02dc48ddcd746a
Status: Downloaded newer image for postgres:13
docker.io/library/postgres:13
vagrant@server1:~$ sudo docker volume create psql
psql
vagrant@server1:~$ sudo docker run --rm --name p-docker -e POSTGRES_PASSWORD=password -e POSTGRES_USER=puser -d -p 8081:8081 -v psql:/var/ postgres:13
a831286743ac434ee9f360a013d610368037f5ffab280620fe6fdcdd65f903e5
```

Подключитесь к БД PostgreSQL используя `psql`.
```bash
vagrant@server1:~$ sudo docker exec -it p-docker psql -U puser
psql (13.6 (Debian 13.6-1.pgdg110+1))
Type "help" for help.
```
Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:
- вывода списка БД
```bash
puser=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 postgres  | puser | UTF8     | en_US.utf8 | en_US.utf8 |
 puser     | puser | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | puser | UTF8     | en_US.utf8 | en_US.utf8 | =c/puser         +
           |       |          |            |            | puser=CTc/puser
 template1 | puser | UTF8     | en_US.utf8 | en_US.utf8 | =c/puser         +
           |       |          |            |            | puser=CTc/puser
(4 rows)
```
- подключения к БД
```bash
puser=# \c postgres
You are now connected to database "postgres" as user "puser".
```
- вывода списка таблиц
`postgres-# \d[S+] NAME`
- вывода описания содержимого таблиц
`postgres=# \dS+ pg_type`
- выхода из psql
`\q`

## Задача 2

Используя `psql` создайте БД `test_database`.
```bash
puser=# CREATE DATABASE test_database;
CREATE DATABASE
```

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).
```bash
puser=# \q
vagrant@server1:~$ sudo curl https://raw.githubusercontent.com/netology-code/virt-homeworks/master/06-db-04-postgresql/test_data/test_dump.sql > test_dump_psql.sql
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2082  100  2082    0     0   5719      0 --:--:-- --:--:-- --:--:--  5735
vagrant@server1:~$ sudo docker cp test_dump_psql.sql p-docker:/var/test_dump_psql.sql
```
Восстановите бэкап БД в `test_database`.
```bash
vagrant@server1:~$ sudo docker exec -it p-docker psql -U puser test_database -f /var/test_dump_psql.sql
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
psql:/var/test_dump_psql.sql:34: ERROR:  role "postgres" does not exist
CREATE SEQUENCE
psql:/var/test_dump_psql.sql:49: ERROR:  role "postgres" does not exist
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE
```

Перейдите в управляющую консоль `psql` внутри контейнера.
```bash
vagrant@server1:~$ sudo docker exec -it p-docker psql -U puser test_database
psql (13.6 (Debian 13.6-1.pgdg110+1))
Type "help" for help.

test_database=#
```

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
```bash
test_database=# \dt
        List of relations
 Schema |  Name  | Type  | Owner
--------+--------+-------+-------
 public | orders | table | puser
(1 row)
test_database=# ANALYZE VERBOSE;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
INFO:  analyzing "pg_catalog.pg_type"
INFO:  "pg_type": scanned 10 of 10 pages, containing 414 live rows and 0 dead rows; 414 rows in sample, 414 estimated total rows
INFO:  analyzing "pg_catalog.pg_foreign_table"
INFO:  "pg_foreign_table":.................
```

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.

```bash
test_database=# SELECT MAX (avg_width) FROM pg_stats WHERE tablename = 'orders';
 max
-----
  16
(1 row)
```

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Предложите SQL-транзакцию для проведения данной операции.
```bash
test_database=# BEGIN;
BEGIN
test_database=*# CREATE TABLE orders_more (LIKE orders);
ders_less (LIKE orders);
INSERT INTO orders_less SELECT * FROM orders WHERE price <=499;
DELETE FROM orders WHERE price <=499;
COMCREATE TABLE
test_database=*# INSERT INTO orders_more SELECT * FROM orders WHERE price >499;
INSERT 0 3
test_database=*# DELETE FROM orders WHERE price >499;
DELETE 3
test_database=*# CREATE TABLE orders_less (LIKE orders);
CREATE TABLE
test_database=*# INSERT INTO orders_less SELECT * FROM orders WHERE price <=499;
INSERT 0 5
test_database=*# DELETE FROM orders WHERE price <=499;
DELETE 5
test_database=*# COMMIT;
COMMIT
```
Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders? \
Нашла  [опцию с партишенами](https://www.postgresql.org/docs/10/ddl-partitioning.html). Так же ожно [создать вью из селекта](https://www.postgresql.org/docs/9.2/sql-createview.html). 

## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```bash
vagrant@server1:~$ sudo docker exec -it p-docker pg_dump -f test_database > test_dump_psql_27_03.sql
vagrant@server1:~$ ls
bye.txt  data  get-docker.sh  hello.txt  test_dump_psql_27_03.sql  test_dump_psql.sql  test_dump.sql
```

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`? \
Для уникальности столбца `title` необходимо было [добавить его как unique](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-UNIQUE-CONSTRAINTS).
