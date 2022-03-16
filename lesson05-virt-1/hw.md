# Домашнее задание к занятию "5.1. Введение в виртуализацию. Типы и функции гипервизоров. Обзор рынка вендоров и областей применения."

## Задача 1

Опишите кратко, как вы поняли: в чем основное отличие полной (аппаратной) виртуализации, паравиртуализации и виртуализации на основе ОС.

Аппаратная - эмулируется аппаратное окружение, гипервизор сам по себе является ОС;  \
Паравиртуализация - изолированые машины, для разделение ресурсов гипервизору требуется ОС; \
Контейниризация - изолированые машины, контейнеры используют общее ядро, разделение ресурсов на уровне ОС.

upd:

В чём разница при работе с ядром гостевой ОС для полной и паравиртуализации?

при аппаратной виртуализации гипервизор выступает связью между аппаратом и гостевой ос, ос полностью изолирована.
при паравиртуализации гипервизор отвечает за разделение ресурсов между аппаратной частью и гостевой ос, тоесть гипервизор модифицирует ос, чтобы сделать её совместимой с ресурсами.

## Задача 2

Выберите один из вариантов использования организации физических серверов, в зависимости от условий использования.

Организация серверов:
- физические сервера,
- паравиртуализация,
- виртуализация уровня ОС.

Условия использования:
- Высоконагруженная база данных, чувствительная к отказу.
- Различные web-приложения.
- Windows системы для использования бухгалтерским отделом.
- Системы, выполняющие высокопроизводительные расчеты на GPU.

Опишите, почему вы выбрали к каждому целевому использованию такую организацию.

- Высоконагруженная база данных, чувствительная к отказу - физические сервера, аппаратная виртуализация, кластеры, репликация данных.
- Различные web-приложения - облако с контейнеризацией и контент деливери нетворком. скажем, амазон или азур, докер. масштабирование в зависимости от нагрузки.
- Windows системы для использования бухгалтерским отделом - родной Hyper-V с паравиртуализацией. Быстрое развёртывание, относительно лёгкая поддержка ввиду низкого порога входа в среду для начинающих специалистов.

- Системы, выполняющие высокопроизводительные расчеты на GPU - аппаратная виртуализация с кластерами. Физические сервера должны обеспечить лучшую производительность.


## Задача 3

Выберите подходящую систему управления виртуализацией для предложенного сценария. Детально опишите ваш выбор.

Сценарии:

1. 100 виртуальных машин на базе Linux и Windows, общие задачи, нет особых требований. Преимущественно Windows based инфраструктура, требуется реализация программных балансировщиков нагрузки, репликации данных и автоматизированного механизма создания резервных копий. 
2. Требуется наиболее производительное бесплатное open source решение для виртуализации небольшой (20-30 серверов) инфраструктуры на базе Linux и Windows виртуальных машин.
3. Необходимо бесплатное, максимально совместимое и производительное решение для виртуализации Windows инфраструктуры.
4. Необходимо рабочее окружение для тестирования программного продукта на нескольких дистрибутивах Linux. 

-
1. Hyper-v, так как преимущественно win структура без особых требований: лёгкий набор персонала под задачу, белансинг, репликация данных. если у компании есть средства, стоит рассмотреть vmware vsphere, как более продвинутый вариант с лучшим варианто маграции машин, менеджмента и разнообразия ОС.
2. KVM ProxMox - лучшая бесплатная производительность.
3. Hyper-v, лучшее решение по совмещению и производительности.
4. KVM, VirtualBox или облачный сервис (если нужно решение без больших затрат на развёртывание полной инфраструктуры). 

## Задача 4

Опишите возможные проблемы и недостатки гетерогенной среды виртуализации (использования нескольких систем управления виртуализацией одновременно) и что необходимо сделать для минимизации этих рисков и проблем. Если бы у вас был выбор, то создавали бы вы гетерогенную среду или нет? Мотивируйте ваш ответ примерами.

Гетерогенная среда виртуализации - частный случай виртуализации, необходимых для достижения определённых задач инфраструктуры.
В зависимости от проекта ответ может быть и да - такой сетап нужен, и нет (делай то, что говорят делать, а не делай то, чего не просит бизнес). 

Наиболее логичным выглядит использование Hyper-V совместно с VMWare.

Сложности:
- разные специалисты для менеджмента и поддержки систем, чем сложнее система, тем опытнее должен быть специалист;
- разный процесс мейнтененса систем (миграции, обновления);
- разделение виртуальных ресурсов под различные нужды разработки и тестирования;
- репликация и бекап двух систем;
- физически разные ресурсы;
- финансовые затраты;
- ...etc

Наиболее удобной и быстрой для реализации проектов считаю облака с возможностью контейнеризации:
- быстрое развёртывание большой структуры;
- масштабируемость;
- богатый выбор систем;
- не нужно думать о поддержке и защите системы, так как это задача вендора.