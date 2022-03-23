# Домашнее задание к занятию "6.2. SQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

```bash
vagrant@server1:~$ sudo docker pull postgres:12
12: Pulling from library/postgres
ae13dd578326: Pull complete
723e40c35aaf: Pull complete
bf97ae6a09b4: Pull complete
2c965b3c8cbd: Pull complete
c3cefa46a015: Pull complete
64a7315fc25c: Pull complete
b9846b279f7d: Pull complete
ed988fb8e7d9: Pull complete
34aba58a1537: Pull complete
6ab3e935454d: Pull complete
93488f5477a0: Pull complete
ccc7cf69d189: Pull complete
1d87bbf01f9a: Pull complete
Digest: sha256:68ea5e8fecb1b24f53af0d35e4a193ea14df71674ca9dd1971eb88b20351a894
Status: Downloaded newer image for postgres:12
docker.io/library/postgres:12
vagrant@server1:~$ sudo docker volume create db
db
vagrant@server1:~$ sudo docker volume create db-replica
db-replica
vagrant@server1:~$ sudo docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v db:/var/lib/postgresql/data -v db-replica:/var/lib/postgresql postgres:12
vagrant@server1:~$ sudo docker exec -it pg-docker psql -U postgres
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
```bash
postgres=# CREATE DATABASE test_db;
CREATE DATABASE
postgres=# CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
postgres=# \q
```
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
 ```bash
vagrant@server1:~$ sudo docker exec -it pg-docker psql -U postgres -d test_db
psql (12.10 (Debian 12.10-1.pgdg110+1))
Type "help" for help.

test_db=# CREATE TABLE orders
test_db-# (id integer PRIMARY KEY,
test_db(# name text,
test_db(# price integer);
CREATE TABLE
test_db=# CREATE TABLE clients
(id integer PRIMARY KEY,
lastname text,
country text,
reserve integer,
FOREIGN KEY (reserve) REFERENCES orders (Id));
CREATE TABLE
```
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
"test-admin-user" SUPERUSER уже суперюзер
- создайте пользователя test-simple-user  
```bash
test_db=# CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
```
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
```bash
test_db=# GRANT SELECT, INSERT, UPDATE, DELETE ON orders, clients TO "test-simple-user";
GRANT
```

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
- описание таблиц (describe)
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
- список пользователей с правами над таблицами test_db

```bash
test_db=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)

test_db=# \d+
                       List of relations
 Schema |  Name   | Type  |  Owner   |    Size    | Description
--------+---------+-------+----------+------------+-------------
 public | clients | table | postgres | 8192 bytes |
 public | orders  | table | postgres | 8192 bytes |
(2 rows)

test_db=# SELECT * FROM information_schema.role_table_grants WHERE grantee = 'test-simple-user';
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
(8 rows)

test_db=# \du
                                       List of roles
    Role name     |                         Attributes                         | Member of
------------------+------------------------------------------------------------+-----------
 postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test-admin-user  | Superuser, No inheritance                                  | {}
 test-simple-user | No inheritance                                             | {}
 ```

## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

```bash
test_db=# insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
```

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

```bash
test_db=# insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
```bash
test_db=# SELECT 'orders' AS table_name, COUNT(*) FROM orders
test_db-# UNION
test_db-# SELECT 'clients' AS table_name, COUNT(*) FROM clients;
 table_name | count
------------+-------
 clients    |     5
 orders     |     5
(2 rows)
test_db=# SELECT table_name, count(*) AS column_count
FROM information_schema."columns"
WHERE table_schema = 'public'
GROUP BY table_name ORDER BY column_count desc;
 table_name | column_count
------------+--------------
 clients    |            4
 orders     |            3
(2 rows)
```
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказка - используйте директиву `UPDATE`.

```bash
test_db=# UPDATE clients SET reserve = 3 WHERE id = 1;
UPDATE 1
test_db=# UPDATE clients SET reserve = 4 WHERE id = 2;
UPDATE 1
test_db=# UPDATE clients SET reserve = 5 WHERE id = 3;
UPDATE 1
test_db=# SELECT * FROM clients c WHERE exists (SELECT id FROM orders o WHERE o.id = c.reserve);
 id |       lastname       | country | reserve
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
(3 rows)
```

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

```bash
test_db=# EXPLAIN SELECT * FROM clients c WHERE exists (SELECT id FROM orders o WHERE o.id = c.reserve);
                               QUERY PLAN
------------------------------------------------------------------------
 Hash Join  (cost=37.00..57.24 rows=810 width=72)
   Hash Cond: (c.reserve = o.id)
   ->  Seq Scan on clients c  (cost=0.00..18.10 rows=810 width=72)
   ->  Hash  (cost=22.00..22.00 rows=1200 width=4)
         ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=4)
(5 rows)
```

Результат показывает кост ресурса запроса, выполнение ввсего запроса, самого джоина.

## Задача 6

В лекции не обсуждали бекапы, о них онформации не было пока