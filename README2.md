# docker  
1. Подключитесь к удаленному серверу

    ```bash
    ssh -i /home/ea703557/Загрузки/555/yc-ea703557 yc-user@158.160.28.33
key NRjeSf
 имя_пользователя@ip_адрес_сервера 
    ```

внешний файл nginx с приложением kittygram_final , который необходимо разместить на виртуальном сервере в каталоге:

```sudo nano /etc/nginx/sites-enabled/default```:

```
server {
    server_name 158.160.28.33 alextaski333.ddns.net;
    server_tokens off;

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:8000;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/alextaski333.ddns.net/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/alextaski333.ddns.net/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    server_name 158.160.28.33 alex86kittygram444.ddns.net;
    server_tokens off;

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/alex86kittygram444.ddns.net/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/alex86kittygram444.ddns.net/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = alex86kittygram444.ddns.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name 158.160.28.33 alex86kittygram444.ddns.net;
    return 404; # managed by Certbot
}

server {
    if ($host = alextaski333.ddns.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80;
    server_name 158.160.28.33 alextaski333.ddns.net;
    return 404; # managed by Certbot
}
```

На вашем удаленном сервере в корневом каталоге проекта вы также должны разместить docker-compose.production.yml:


```
version: '3'

volumes:
  pg_data_production:
  static_volume:

services:
  db:
    image: postgres:13.10
    env_file: .env
    volumes:
      - pg_data_production:/var/lib/postgresql/data
  backend:
    image: alex886/taski_backend # Docker Hub username / username on Docker image
    env_file: .env
    volumes:
      - static_volume:/backend_static
  frontend:
    image: alex886/taski_frontend  # Download from Docker Hub
    env_file: .env
    command: cp -r /app/build/. /frontend_static/
    volumes:
      - static_volume:/frontend_static
  gateway:
    image: alex886/taski_gateway  # Download from с Docker Hub
    env_file: .env
    volumes:
      - static_volume:/staticfiles/
    ports:
      - 8000:80
```

Полезные советы по Docker
Введение
Docker позволяет легко помещать приложения и службы в контейнеры, чтобы их можно было запускать где угодно. Однако при работе с Docker можно легко накопить чрезмерное количество неиспользуемых образов, контейнеров и томов данных, замедляющих работу и потребляющих место на диске.

Docker предоставляет все необходимые инструменты для очистки системы из командной строки. В этом руководстве с полезными советами кратко описываются полезные команды для освобождения места на диске и организации системы посредством удаления неиспользуемых образов, контейнеров и томов Docker.

Использование этого руководства:

Это руководство в формате полезных советов содержит автономные сниппеты для командной строки
Вы можете перейти к любому разделу, актуальному для задачи, которую вы пытаетесь выполнить.
Синтаксис замены команды command $(command), используемый в командах, доступен во многих популярных оболочках, включая bash, zsh и Windows Powershell.

Очистка всех неиспользуемых или не связанных с контейнерами образов, контейнеров, томов и сетей
В Docker имеется команда, очищающая все не связанные с контейнерами ресурсы, в том числе образы, контейнеры, тома и сети:

docker system prune
Чтобы удалить все остановленные контейнеры и неиспользуемые образы (а не только образы, не связанные с контейнерами), добавьте в эту команду флаг -a:

docker system prune -a
Удаление образов Docker
Удаление одного или нескольких конкретных образов
Используйте команду docker images с флагом -a, чтобы найти идентификатор удаляемых образов. Эта команда покажет вам все образы, включая промежуточные слои образов. Когда вы определитесь с составом удаляемых образов, вы можете передать их идентификаторы или теги в docker rmi:

Список:

docker images -a
Удаление:

docker rmi Image Image
Удаление образов, не привязанных к контейнеру
Образы Docker состоят из нескольких слоев. Несвязанные образы — это слои, не имеющие связей с каким-либо образами с тегами. У них нет никакого назначения, и они просто занимают место на диске. Их можно найти, добавив флаг фильтра -f со значением dangling=true в команду docker images. Если вы уверены, что хотите удалить их, вы можете использовать команду docker images purge:

Примечание. Если вы создали образ и не пометили его тегами, он будет отображаться в списке несвязанных образов, поскольку он не привязан к образу с тегом. Этого можно избежать, указав тег при сборке образа. Также вы можете помечать тегами уже существующие образы с помощью комнады docker tag.

Список:

docker images -f dangling=true
Удаление:

docker images purge