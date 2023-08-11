# Домашнее задание к занятию 12.8 "Резервное копирование баз данных" - `Горбачев Олег`

Любые вопросы по решению задач задавайте в чате учебной группы.

### Задание 1. Резервное копирование
Кейс

Финансовая компания решила увеличить надежность работы БД и их резервного копирования.
Необходимо описать, какие варианты резервного копирования подходят в случаях:

* 1.1 Необходимо восстанавливать данные в полном объеме за предыдущий день.

* 1.2 Необходимо восстанавливать данные за час до предполагаемой поломки.

* 1.3 Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую/починеную БД?

Приведите ответ в свободной форме.

---

### Ответ:

В процессе обычно используются утилиты для восстановления, поставляемые в комплекте с СУБД.

* 1.1 Необходимо восстанавливать данные в полном объеме за предыдущий день.
 Дифференциальная резервная копия подойдет.
Дифференциальная резервная копия позволяет быстрее восстанавливать данные по сравнению с инкрементным резервным копированием, поскольку для этого требуется всего две части резервной копии: полная резервная копия и последняя дифференциальная резервная копия.
Данное резервное копирование быстрее, чем полное, но медленнее, чем инкрементное .
 
* 1.2 Необходимо восстанавливать данные за час до предполагаемой поломки.
Инкрементное резервное копирование подойдет.
Инкрементное резервное копирование использует полную копию, как начальную точку. Затем выполняется резервное копирование только блоков данных, которые были изменены с момента последнего резервного задания, с заданным периодом выполнения задания. В зависимости от политики хранения резервных копии, через определенный период создается новая полная копия для повторения цикла.
Инкрементное резервное копирование можно выполнять так часто, как требуется, так как сохраняются только копии последних изменений. Нам это подходит.
 
* 1.3  Возможен ли кейс, когда при поломке базы происходило моментальное переключение на работающую/починеную БД?
 
Репликация master-slave 

 ---
 
### Задание 2. PostgreSQL
* 2.1 С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).
* 2.1* Возможно ли автоматизировать этот процесс? Если да, то как?
Приведите ответ в свободной форме.
___
Ответ:

**PostgreSQL**

* 2.1 С помощью официальной документации приведите пример команды резервирования данных и восстановления БД (pgdump/pgrestore).

Следующая команда делает дамп базы данных, используя специальный формат дампа:
```shell
pg_dump -Fc bd_name > dump.sql
```
Специальный формат дампа не является скриптом для psql и должен восстанавливаться с помощью команды 
pg_restore, например:
```shell
pg_restore –d bd_name dump.sql
```
* 2.1 Возможно ли автоматизировать этот процесс? Если да, то как?

Скрипт для автоматического резервного копирования
postgresql_dump.sh
```shell 
#!/bin/sh
PATH=/etc:/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin
PGPASSWORD=password

export PGPASSWORD

pathB=/backup

dbUser=dbuser
database=db

find $pathB \( -name "*-1[^5].*" -o -name "*-[023]?.*" \) -ctime +61 -delete

pg_dump -U $dbUser $database | gzip >  $pathB/pgsql_$(date "+%Y-%m-%d").sql.gz

unset PGPASSWORD

или для запуска резервного копирования по расписанию, сохраняем скрипт в файл, например, /scripts/postgresql_dump.sh и создаем задание в планировщике:

crontab -e

**6 0 * * * /scripts/postgresql_dump.sh**
```
Скрипт будет запускаться каждый день в 06:00.

---

### Задание 3. MySql

* 3.1 С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySql.


Приведите ответ в свободной форме.

### Ответ:

**MySql**

* 3.1 С помощью официальной документации приведите пример команды инкрементного резервного копирования базы данных MySql.
```shell
--incremental-base=history:last_full_backup
```
или

через XtraBackup как раз в простых, быстрых и удобных инкрементных бэкапах для mysql.
```shell
xtrabackup --backup --target-dir=/root/backupdb/inc1 --incremental-basedir=/root/backupdb/full
```


Задания,помеченные звездочкой * - дополнительные (не обязательные к выполнению) и никак не повлияют на получение вами зачета по этому домашнему заданию. Вы можете их выполнить, если хотите глубже и/или шире разобраться в материале.
