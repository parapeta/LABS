## Отчет по лабораторной работе №1 

### 1) Подготовка среды 
Запускаю виртуальную машину на VM Virtual Box c установленной на нее ОС Linux Debian,
выполняю вход в root и с помощью команды: sudo apt-get update проверяю доступные пакеты,
с помощью команды: sudo apt-get upgrade обновляю эти пакеты

<img width="890" alt="Снимок экрана 2025-04-10 в 02 31 01" src="https://github.com/user-attachments/assets/bf2ec7e5-378d-4812-9cb1-a8489cbe4fb4" />


### 2) Установка PostgreSQL
С помощью команды: sudo apt-get install -y postgresql postgresql-contrib устананвливаю postgresql и его основные зависимости


### 3) Создание служебной учётной записи
С помощью команды: sudo -i -u postgres;
psql;
\du;
проверяю наличие учетной записи postgres в системе

пользоваель postgres – в системе linux обычный системный пользователь
Права:
1.	Может запускать и управлять процессами PostgreSQL. 
2.	Может читать и записывать файлы в /var/lib/postgresql/ (где хранятся базы данных).
3.	Может выполнять команды PostgreSQL (psql, pg_ctl, pg_dump и т. д.). Не имеет root-доступа и не может выполнять команды sudo.
4.	
<img width="756" alt="Снимок экрана 2025-04-10 в 03 00 20" src="https://github.com/user-attachments/assets/ab7bbbfb-50c6-495e-a124-a4a3a7e1e46d" />

### 4) Первичная настройка конфигурационных файлов 
sudo nano /etc/postgresql/15/main/postgresql.conf \
С помощью команды: sudo nano /etc/postgresql/15/main/postgresql.conf захожу в файл конфигурации postgresql.conf 

изменяю port = 5432 на 5433\

<img width="854" alt="Снимок экрана 2025-04-10 в 02 44 31" src="https://github.com/user-attachments/assets/d9d02748-a34d-48e6-a252-b8219acae806" />


 
### 5) Управление сервисом 
Проверяю статус сервера postgresql командой: sudo systemctl status postgresql 

Перезагружаю сервис командой sudo systemctl restart postgresql 
И добавляю автозапуск сервиса командой: sudo systemctl enable postgresql 


### 6) Создание тестовой базы данных 
Захожу в psql от имени postgres и создаю пользователя командой create user Bleg_Olegovich \
Также создаю базу данных: create db dbbleg


### 7) Знакомство со схемами 
Создаю схему test_schema: CREATE SCHEMA bleg_schema; \
Настраиваю полный доступ к схеме у пользователя dbbleg: GRANT ALL ON SCHEMA test_schema TO Bleg_Olegovich.

<img width="890" alt="Снимок экрана 2025-04-10 в 03 11 04" src="https://github.com/user-attachments/assets/78a35df9-116a-4718-85be-5fa678f55666" />

### 8) Использование утилиты psql для базовых операций 
CREATE TABLE public.mytable (ID SERIAL PRIMARY KEY, Name VARCHAR (100)); - создание таблицы с двумя полями: id и name \
INSERT INTO public.metable VALUES (id = 1,‘ODIN’), (id = 2, DVA); - добавление новых полей в таблицу \
SELECT * FROM public.mytable; - вывести все поля из таблицы mytable в схеме public \
DELETE ALL FROM public.mytable WHERE id = 2 OR name = ‘DVA’; – удалить строки из таблицы mytable  в схеме public, где id = 2 или name = ‘DVA’ 


### 9) Настройка локальных сетевых подключений
pg_hba.conf:/
  Общий формат:/
    connection-type database user address auth-method [auth-options]/
      1. connection-type (тип подключения):/
        - local: Подключения через сокет домена Unix/
        - host: Подключения по TCP/IP без шифрования\
        - hostssl: Подключения по TCP/IP с SSL-шифрованием\
        - hostnossl: Подключения по TCP/IP без SSL-шифрования\
        - hostgssenc: Подключения по TCP/IP с шифрованием GSSAPI\
        - hostnogssenc: Подключения по TCP/IP без шифрования GSSAPI\
      2. database (имя базы данных или специальные значения):\
        - all: любая база данных\
        - replication: специальная база данных для репликации\
      3. user: (Имя пользователя PostgreSQL или специальные значения):\
        - all: Любой пользователь.\
      4. address:\
        - для локальных подключений не используется\
        - для удаленных подключений указывает IP-адрес или имя хоста клиента\
        - может быть заменено полями IP-address и IP-mask для указания диапазона IP-адресов\
      5. auth-method (метод аутентификации)\
        - trust: Доверие, без пароля\
        - reject: Отказ в доступе\
        - md5: MD5-хешированный пароль\
        - password: Простой пароль\
        - scram-sha-256: SCRAM-SHA-256\
        - gss: GSSAPI\
        - sspi: SSPI (Windows)\
      6. [auth-options]:\
        - Дополнительные опции для метода аутентификации.


### 10)  Журналирование (logging)
log_destination = 'stderr' - позволяет выводить логи\
logging_collector = on – отвечает за сбор логов в файлы, делает логирование удобнее и кастомизируемым, например можно менять\
формат логов со стандартного stderr на jsonlog или csvlog\
log_directory = 'log' – директория логов\
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log' - определяет формат именования лог-файлов в PostgreSQL, завичит от состояния "logging_collector"\
log_statement = 'all' - логировать все SQL-запросы\
log_min_error_statement = error\
log_min_messages = warning - минимальный уровень сообщений\
log_statement – параметр, определяющий какие команды будут записываться в журнал логов\
sudo /etc/init.d/postgresql restart - перезапуск PostgreSQL

cat /var/lib/postgresql/15/main/log/postgresql-2025-04-10_091916.log - вывод логов\

<img width="867" alt="Снимок экрана 2025-04-10 в 11 37 15" src="https://github.com/user-attachments/assets/0afddf2b-43cd-4ace-84a2-a716ca12ce2e" />




### 11)  Назначение ролей и прав
Назначение прав для limited_user1(от лица postgres): Grant select on public.mytable to limited_user2;\
Вход в датабазу postgres: psql –h localhost –p 5433 –U limited_user1 –d postgres;\
Создание роли и назначение ей прав: CREATE ROLE read_write_role;\
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLE public.mytable TO read_write_role;\
Наследование прав от олной роли к другой: GRANT read_write_role TO limited_user;\
Set role limited user;

<img width="622" alt="Снимок экрана 2025-04-10 в 12 28 49" src="https://github.com/user-attachments/assets/67968617-b01e-44d7-9750-5889530b8f02" />




