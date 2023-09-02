### Описание проекта 
Kittygram - сервис для любителей котиков.

Что умеет проект:

- Добавлять, просматривать, редактировать и удалять котиков.
- Добавлять новые и присваивать уже существующие достижения. 
- Просматривать чужих котов и их достижения.

## Установка 

1. Клонируйте репозиторий на свой компьютер:

    ```bash
    git clone https://github.com/AlexAvdeev1986/kittygram_final.git
    ```
    ```bash
    cd kittygram_final
    ```
2. Создайте файл .env и заполните его своими данными. Перечень данных указан в корневой директории проекта в файле .env.example.
Fill it with your data

# Database Secrets
POSTGRES_USER=[database_username]
POSTGRES_PASSWORD=[database_password]
POSTGRES_DB=[database_name]
DB_PORT=[database_connection_port]
DB_HOST=[db]

# Django Secrets
SECRET_KEY='SECRET_KEY'
DEBUG=False
ALLOWED_HOSTS='your_domain'

### Создание Docker-образов

1.  Замените username на ваш логин на DockerHub:

    ```bash
    cd backend
    docker build -t alex886/kittygram_backend .
    cd frontend
    docker build -t alex886/kittygram_frontend .
    cd ../nginx
    docker build -t alex886/kittygram_gateway .
    ```

2. Загрузите образы на DockerHub:

    ```bash
    docker push alex886/kittygram_backend
    docker push alex886/kittygram_frontend
    docker push alex886/kittygram_gateway
    ```

### Деплой на сервере

1. Подключитесь к удаленному серверу

    ```bash
    ssh -i /home/ea703557/Загрузки/555/yc-ea703557 yc-user@158.160.28.33
key NRjeSf
 имя_пользователя@ip_адрес_сервера 
    ```

2. Создайте на сервере директорию kittygram через терминал

    ```bash
    mkdir kittygram
    ```

3. Установка docker compose на сервер:

    ```bash
    sudo apt update
    sudo apt --fix-broken install
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

4. В директорию kittygram/ скопируйте файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    * ath_to_SSH — путь к файлу с SSH-ключом;
    * SSH_name — имя файла с SSH-ключом (без расширения);
    * username — ваше имя пользователя на сервере;
    * server_ip — IP вашего сервера.g
    ```bash
    ssh -i /home/ea703557/Загрузки/555/yc-ea703557 yc-user@158.160.28.33
key NRjeSf
 имя_пользователя@ip_адрес_сервера 
    ```

 scp -i /home/ea703557/Загрузки/555/yc-ea703557 docker-compose.production.yml  yc-user@158.160.28.33:/home/yc-user/kittygram/docker-compose.production.yml

 scp -i /home/ea703557/Загрузки/555/yc-ea703557 .env  yc-user@158.160.28.33:/home/yc-user/kittygram/.env
    ```

Далее выполняем последовательно
sudo docker compose -f docker-compose.production.yml pull
sudo docker compose -f docker-compose.production.yml down
sudo docker compose -f docker-compose.production.yml up -d
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collect_static/. /static_backend/static/

Создаем суперпользователся. Следуем инструкциям при выполнении.

sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser

    ```

7. На сервере в редакторе nano откройте конфиг Nginx:
```bash
    ssh -i /home/ea703557/Загрузки/555/yc-ea703557 yc-user@158.160.28.33
key NRjeSf
 имя_пользователя@ip_адрес_сервера 
    ```
    ```bash
    sudo nano /etc/nginx/sites-enabled/default
    ```
...8Устанавливаем и настраиваем NGINX
Устанавливаем NGINX....

sudo apt install nginx -y
Запускаем
sudo systemctl start nginx
Настраиваем firewall
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
Включаем firewall
sudo ufw enable
Открываем конфигурационный файл NGINX
sudo nano /etc/nginx/sites-enabled/default
Полностью удаляем из него все и пишем новые настройки
server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_set_header HOST $host;
        proxy_pass http://127.0.0.1:9000;

    }
}
Сохраняем изменения и выходим из редактора
Проверяем корректность настроек
sudo nginx -t
Запускаем NGINX
sudo systemctl start nginx
Настраиваем HTTPS
Установка пакетного менеджера snap.
У этого пакетного менеджера есть нужный вам пакет — certbot.
sudo apt install snapd
Установка и обновление зависимостей для пакетного менеджера snap.
sudo snap install core; 

sudo snap refresh core

sudo snap refresh

При успешной установке зависимостей в терминале выведется:
core 16-2.58.2 from Canonical✓ installed 
Установка пакета certbot.

sudo snap install --classic certbot
При успешной установке пакета в терминале выведется:
certbot 2.3.0 from Certbot Project (certbot-eff✓) installed
Создание ссылки на certbot в системной директории,
Чтобы у пользователя с правами администратора был доступ к этому пакету.
sudo ln -s /snap/bin/certbot /usr/bin/certbot
Получаем сертификат и настраиваем NGINX следуя инструкциям
sudo certbot --nginx
Перезапускаем NGINX
sudo systemctl reload nginx


Запустите SSH-агент: Убедитесь, что SSH-агент запущен на вашем локальном компьютере и в него добавлен ваш приватный ключ с помощью ssh-add ...

chmod 600 yc-ea703557
ssh-add yc-ea703557

chmod 600 authorized_keys

eval "$(ssh-agent -s)"

ssh-add 

ssh-add ~/.ssh/authorized_keys 