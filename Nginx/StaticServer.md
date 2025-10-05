## Nginx Веб-сервер (static server)

Ми не можемо просто роздавати файли(*.html) з диска, треба налаштувати веб-сервер який і буде роздавати їх, наприклад: Nginx, Apache, Caddy, Lighttpd тощо.

### 1 Налаштування Nginx як статичного сервера:
створимо простий сайт з одним файлом `index.html` і налаштуємо Nginx щоб він роздавав цей файл.


### Приклад схеми роботи
``` mermaid
flowchart TD
    A["Браузер користувача"] -->|HTTP GET /index.html| B[Nginx]
    B -->|"Читає файл з диска"| C["/var/www/my-site/index.html"]
    C -->|"Відповідь HTML"| A
```

1. Користувач відкриває сайт — запит іде до Nginx.
2. Nginx шукає файл на диску (наприклад, `/var/www/my-site/index.html`).
3. Якщо файл є, Nginx віддає його користувачу.
4. Якщо файл відсутній, Nginx повертає помилку 404 Not Found.

### Налаштування Nginx як статичного сервера
1. Встанови Nginx (див. [Installation.md](./Installation.md)).
2. Створи каталог для сайту в `/var/www/`:
   ```sh
    mkdir -p /var/www/my-static-site
   ```

3. створимо простий HTML файл `index.html`:
    <br>перейдемо в каталог:
   ```sh
   cd /var/www/my-static-site
   ```
   і створи файл `index.html` з таким вмістом:
    ```sh
    nano index.html
    ```

   ```html
   <html>
   <head>
       <title>Yak Shaving Ops</title>
   </head>
   <body>
       <h1>Welcome to Yak Shaving Ops</h1>
       <p>This is a simple static site served by Nginx.</p>
   </body>
   </html>
   ```

4. Налаштуй конфігурацію Nginx:
   не будемо змінювати основний конфігураційний файл `nginx.conf`, а створимо новий сайт в каталозі `/etc/nginx/sites-available`
   <br>Шлях: /etc/nginx/sites-available/my-static-site.conf
   <br>Створи файл `/etc/nginx/sites-available/my-static-site.conf

    ```
    sudo nano /etc/nginx/sites-available/my-static-site.conf
    ```
    з таким вмістом:
   ```nginx
   server {
        listen 8080;  
        server_name localhost;  # твій домен або IP

        root /var/www/my-static-site; # повний шлях до каталогу з index.html
        index index.html;
        
        location / {
            try_files $uri $uri/ =404;
        }
   }
   ```

    Пояснення:
    - listen 8080; — приймати запити на порт 8080 (HTTP). Наша сайт буде доступний за адресою `http://localhost:8080` або `http://your_domain.com:8080`.
    - server_name — домен (можна змінити на localhost або IP).
    - root — папка, де лежать файли сайту.
    - index — файл, який відкривається за замовчуванням.
    - try_files — перевіряє, чи існує файл; якщо ні — віддає 404.

5. Активуй сайт 

    створивши символічне посилання в `/etc/nginx/sites-enabled/`:
   ```sh
    sudo ln -s /etc/nginx/sites-available/my-static-site.conf /etc/nginx/sites-enabled/
   ```

    Перевір конфігурацію і перезавантаж Nginx:
   ```sh
   sudo nginx -t
   sudo systemctl restart nginx
   ```
6. Відкрий у браузері `http://localhost:8080` і побачиш свій сайт!
    або перевірити через curl:
    ```sh
        curl http://localhost:8080
    ```

### Корисні команди
- Перевірка конфігурації:
    ```sh
    sudo nginx -t
    ``` 
- Перезавантаження Nginx після змін:
    ```sh
    sudo systemctl restart nginx
    ```
- Перезапуск Nginx:
    ```sh
    sudo systemctl reload nginx
    ``` 

- Перевірка статусу Nginx:
    ```sh
    sudo systemctl status nginx
    ```
- Перегляд логів:
    ```sh
    sudo tail -f /var/log/nginx/access.log
    sudo tail -f /var/log/nginx/error.log
    ```



### Додаткові можливості
- Налаштування кешування статичних файлів.
- Використання gzip стиснення для швидшої передачі.
- Налаштування HTTPS з SSL сертифікатами.
- Використання virtual hosts для кількох сайтів на одному сервері.
- Обмеження доступу за IP або іншими параметрами.
- Налаштування логування для аналізу трафіку.
- Використання CDN для роздачі статичних файлів.
- Інтеграція з системами моніторингу (Prometheus, Grafana).
