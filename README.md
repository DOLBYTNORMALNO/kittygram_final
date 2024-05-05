Kittygram

Kittygram — это социальная сеть, которая предоставляет пользователям возможность находить друзей по интересам и делиться фотографиями своих питомцев.


Клонируйте репозиторий и перейдите в него:

    git clone git@github.com:DOLBYTNORMALNO/kittygram_final.git
    cd kittygram_final


Создайте файл .env с необходимыми переменными окружения, ниже пример:

    POSTGRES_DB=kittygram
    POSTGRES_USER=USER
    POSTGRES_PASSWORD=PASSWORD
    DB_HOST=db
    DB_PORT=5432
    SECRET_KEY=DJANGO_SECRET_KEY
    DEBUG=False
    ALLOWED_HOSTS=['ip', '127.0.0.1', 'localhost', 'DNS_name']
    DB_NAME=kittygram


Создайте Docker-образы в директориях frontend, backend, и nginx:

    cd frontend
    docker build -t username/kittygram_frontend .
    cd ../backend
    docker build -t username/kittygram_backend .
    cd ../nginx
    docker build -t username/kittygram_gateway .
Замените username на ваш логин на DockerHub.


Загрузите созданные образы на DockerHub:

    docker push username/kittygram_frontend
    docker push username/kittygram_backend
    docker push username/kittygram_gateway

Подключитесь к серверу и создайте директорию для проекта:

    ssh YOUR_USERNAME@SERVER_IP_ADDRESS
    mkdir kittygram

Скопируйте файл docker-compose.production.yml и .env в созданную директорию:

    scp -i PATH_TO_SSH_KEY/SSH_KEY_NAME docker-compose.production.yml YOUR_USERNAME@SERVER_IP_ADDRESS:/home/YOUR_USERNAME/kittygram/

Запустите Docker Compose:

    sudo docker compose -f docker-compose.production.yml up -d

Выполните миграции и соберите статические файлы:

    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /static/static/

Откройте и измените конфигурацию Nginx:

    sudo nano /etc/nginx/sites-enabled/default

Настройте проксирование:

    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }

Проверьте конфигурацию Nginx:

    sudo nginx -t

Если конфигурация корректна, перезагрузите Nginx:

    sudo service nginx reload
