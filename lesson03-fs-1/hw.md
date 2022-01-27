# Домашнее задание к занятию "3.5. Файловые системы"

1. Узнайте о [sparse](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных) файлах.
\+ 

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
Hardlink - то жёсткая ссылка на объект с тем же inode-номером, так что нет.
[оченьполезнаястатья](https://rtfm.co.ua/unix-chto-takoe-symlink-hardlink-i-inode/#Hard_link)
3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile.
![destroy](img/vdestroy.JPG)
![disk](img/diskinfo.JPG)

4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
![fdisk](img/fdisk.JPG)

5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.
![sfdisk](img/altersdb.JPG)

6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.
![raid1](img/raid1.JPG)

7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.
![raid0](img/raid0.JPG)

8. Создайте 2 независимых PV на получившихся md-устройствах.
![pvcreate](img/pvcreate.JPG)

9. Создайте общую volume-group на этих двух PV.
![vgcreate](img/vgcreate.JPG)

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
![lvcreate](img/lvcreate.JPG)

11. Создайте `mkfs.ext4` ФС на получившемся LV.
![mkfs](img/mkfs.JPG)

12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.
![mount](img/mount.JPG)

13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.
![file](img/cool.JPG)

14. Прикрепите вывод `lsblk`.
![lsblk](img/lsblk.JPG)

15. Протестируйте целостность файла:
![cool](img/gzip.JPG)

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
![pvmove](img/pvmove.JPG)

17. Сделайте `--fail` на устройство в вашем RAID1 md.
![failmd1](img/faultymd1.JPG)

18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.
![dmesg](img/dmesg.JPG)

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
![lastgzip](img/gzipafterfailure.JPG)

20. Погасите тестовый хост, `vagrant destroy`.
![lastvdestroy](img/lastvdestroy.JPG)