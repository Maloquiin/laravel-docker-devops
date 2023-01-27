# Тестовое задание Junior DevOps

### Вводные данные
*	Дан репозиторий с PHP приложением Laravel;
*	Приложением подключается к двум БД MySql - из одной читает, в другую пишет;
*   Для запуска приложения требуется реализовать репликацию базы данных и запуск нескольких команд для миграции базы данных и установки требуемых библиотек.


### Задача
* Требуется запустить Laravel (PHP) приложение. Требуемое ПО для запуска:
    * Веб-сервер (Apache/Nginx, предпочтительнее Nginx);
    * PHP 8+;
    * База данных (MySql/MariaDB, предпочтительнее MariaDB 10+);
    * Composer 2+ - пакетный менеджер для подключения библиотек PHP;
    * Npm 8.19+ (node.js) - пакетный менеджер для подключения библиотек JS;
    * Artisan (from Laravel 9+);
    
### Инструкция по запуску сервисов
* Отредактировать docker-compose.yml и указать все environment в сервисах master и slave
* создать файл ".env" в корне приложения (скопировать в него содержимое из .env.example;
    * В созданном ".env" указать переменные подключения к базам данных:
        * WRITER_DB_HOST=ip адрес сервера БД для записи;
        * WRITER_DB_PORT=порт подключения к БД;
        * WRITER_DB_DATABASE=имя базы;
        * WRITER_DB_USERNAME=имя пользователя;
        * WRITER_DB_PASSWORD=пароль;

        Аналогично указать переменные для подключения к БД чтения:
        * READER_DB_HOST=
        * READER_DB_PORT=
        * READER_DB_DATABASE=
        * READER_DB_USERNAME=
        * READER_DB_PASSWORD=
   
* Cмонтируйте образ composer из Docker в каталоги, которые нужны для вашего проекта Laravel, чтобы избежать издержек глобальной установки Composer:
    ```Bash 
    docker run --rm -v $(pwd):/app composer install --ignore-platform-reqs
    ```
* Выполните команду для запуска контейнера:
    ```Bash
    docker-compose up -d
    ```

* Следующая команда будет генерировать ключ и скопирует его в файл .env, гарантируя безопасность сеансов пользователя и шифрованных данных:
    ```Bash
    docker-compose exec app php artisan key:generate
    ```
       
* Выполните команду для миграции базы данных:
    ```Bash
        docker-compose exec app php artisan migrate:fresh --seed;
    ```

* Чтобы кэшировать настройки в файле, ускоряющем загрузку приложения, запустите команду:
    ```Bash
    docker-compose exec app php artisan config:cache
    ```
* Теперь нужно настроить репликацию баз данных
    * Подключитесь к бд Master и сделайте дамп, следом выгрузите дамп на бд Slave
    
    * Подключитесь к бд Master и создайте пользователя для репликации командой:
    ```SQL
    create user 'replicant'@'%' identified by 'replicant';
    ```
    * Назначаем привилегии:
    ```SQL
    grant replication slave on *.* to replicant;
    ```
