# Docker Registry 3

Приватный Docker Registry с авторизацией через `htpasswd`, запускаемый через Docker Compose.

## Структура

```text
.
├── auth/
│   └── htpasswd
├── data/
├── docker-compose.yml
└── README.md
```

* `auth/` — файл пользователей и паролей.
* `data/` — хранилище образов Registry.

## Запуск

Создать каталоги:

```bash
mkdir -p auth data
```

Запустить Registry:

```bash
docker compose up -d
```

## Добавление пользователей
Добавления пользователей для авторизации.
### Через пакет

```bash
apt update && apt install -y apache2-utils
htpasswd -Bbc ./auth/htpasswd admin
htpasswd -Bbn ./auth/htpasswd {login} {password}
```

### Через контейнер

```bash
docker run --rm \
  --entrypoint htpasswd \
  httpd:2 \
  -Bbn developer DevPassword123 \
  >> ./auth/htpasswd
```

## Ограничения

`htpasswd` предоставляет только базовую авторизацию:

* нет ролей и групп;
* нет ограничений по репозиториям;
* все пользователи имеют одинаковые права.

## Настройка HTTPS через Nginx

В репозитории присутствует файл `example.nginx.conf` с примером конфигурации Nginx для публикации Docker Registry через HTTPS.

Конфигурация выполняет следующие задачи:

* принимает HTTP-запросы на 80 порту;
* предоставляет доступ к каталогу проверки Certbot;
* автоматически перенаправляет весь HTTP-трафик на HTTPS;
* проксирует запросы к Docker Registry;
* использует сертификаты Let's Encrypt.

Перед использованием замените `{domain}` на ваш домен.

### Получение сертификата

Создайте каталог для проверки домена:

```bash
mkdir -p /var/www/certbot
```

Получите сертификат:

```bash
certbot certonly \
  --webroot \
  -w /var/www/certbot \
  -d registry.example.com
```
