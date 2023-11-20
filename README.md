# Docker + Yii2 Basic + MySql + phpMyAdmin

Для начала необходимо клонировать репозиторий [https://github.com/yiisoft/yii2-app-basic](https://github.com/yiisoft/yii2-app-basic)

Для добавления mysql и phpMyAdmin добавить следующий код в `docker-compose.yml`

```yaml
db:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_DATABASE=demo_project
      - MYSQL_USER=demo_user
      - MYSQL_PASSWORD=123456
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - '3306:3306'
    expose:
      - '3306'
    volumes:
      - "./docker/mysql:/var/lib/mysql"
    networks:
      - default
phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - '8888:80'
    environment:
      - PMA_ARBITRARY=1
      - PMA_HOST=db
    depends_on:
      - db
```
<details>
    <summary>Таким образом, полный файл `docker-compose.yml` будет выглядеть примерно так (спойлер):</summary>

```yaml
version: '2'
services:
  php:
    image: yiisoftware/yii2-php:8.2-apache
    volumes:
      - ~/.composer-docker/cache:/root/.composer/cache:delegated
      - ./:/app:delegated
    ports:
      - '8000:80'
  db:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_DATABASE=demo_project
      - MYSQL_USER=demo_user
      - MYSQL_PASSWORD=123456
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - '3306:3306'
    expose:
      - '3306'
    volumes:
      - "./docker/mysql:/var/lib/mysql"
    networks:
      - default
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - '8888:80'
    environment:
      - PMA_ARBITRARY=1
      - PMA_HOST=db
    depends_on:
      - db
```
</details>

Информацию о доступных образах Yii2 c различными веб-серверами и версиями PHP можно почерпнуть [тут](https://github.com/yiisoft/yii2-docker/#available-versions-for-yiisoftwareyii2-php)

Чтобы данная инсталляция смогла нормально работать под Linux, возможно, придется дополнительно установить разрешающие права на папки:

```bash
chmod -R 777 web/assets
chmod -R 777 runtime
```

В конфигурационном файле Yii2 прописываем соответствующие параметры подключения к базе данных из `docker-compose.yml`,
в качестве имени хоста сервера базы используя имя сервиса: "db".

Затем действуем по [инструкции](https://github.com/yiisoft/yii2-app-basic#install-with-docker):

В этих командах "php" используется, как имя сооветствующего сервиса из `docker-compose.yml`. Это не вызов интерпретатора, как может показаться.

Update your vendor packages

    docker-compose run --rm php composer update --prefer-dist

Run the installation triggers (creating cookie validation code)

    docker-compose run --rm php composer install    

Start the container

    docker-compose up -d


В результате сайт будет доступен по адресу [http://localhost:8000/](http://localhost:8000/)


А phpMyAdmin по адресу [http://localhost:8888/](http://localhost:8888/)
