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
1. Встанови Nginx (див. [Installation](./Installation.md)).
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
   <br>Шлях: `/etc/nginx/sites-available/my-static-site.conf`
   <br>Створи файл `/etc/nginx/sites-available/my-static-site.conf`

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
    - `listen 8080;` — приймати запити на порт 8080 (HTTP). Наша сайт буде доступний за адресою `http://localhost:8080` або `http://your_domain.com:8080`.
    - `server_name` — домен (можна змінити на localhost або IP).
    - `root` — папка, де лежать файли сайту.
    - `index` — файл, який відкривається за замовчуванням.
    - `try_files` — перевіряє, чи існує файл; якщо ні — віддає 404.

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

---

Зараз сайт доступний на `http://localhost:8080`
Але ми хочемо щоб сайт був доступний за адресою `http://localhost/my-site` і при цьому без порту (тобто на стандартному 80).

потрібно змінити конфігурацію `/etc/nginx/sites-available/my-static-site.conf`
```nginx
   server {
        listen 80;
        server_name localhost;  # твій домен або IP

        location = /my-site {
            return 302 /my-site/;
        }

        location /my-site/ {
            alias  /var/www/my-static-site/;
            index index.html;
            try_files $uri $uri/ =404;
        }
   }
```

Інша версія з rewrite:

```nginx
   server {
        listen 80;
        server_name localhost;  # твій домен або IP

        location = /my-site {
            rewrite ^ /my-site/ last;   # внутрішній rewrite → клієнт отримає 200
        }

        location /my-site/ {
            alias  /var/www/my-static-site/;
            index index.html;
            try_files $uri $uri/ =404;
        }
   }
```

Пояснення:
- `listen 80;` — сайт доступний за http://localhost/
- `location /my-site` — відповідає за URL, який починається з /my-site
- `alias` — каже Nginx: цей URL насправді відповідає цій папці
(відрізняється від root, бо alias не додає /my-site до шляху)
- `try_files` — перевіряє, чи існує файл або директорія
- `index index.html;` — що показувати, якщо запитано директорію

Перевір конфігурацію і перезавантаж Nginx:
```sh
sudo nginx -t && sudo systemctl restart nginx
```

Тепер сайт буде доступний за адресою `http://localhost/my-site` (без порту) 
<br>і якщо ми відкриємо `http://localhost/my-site/`(СЛЕШ в кінці) то він коректно працювати у браузері.

Перевірити можна через curl:
```sh
curl -L http://localhost/my-site
curl -L http://localhost/my-site/
```

### Примітки:
- Використовуйте `curl -L` для перевірки редиректів.

Коли користувач (або браузер) звертається за:
```
http://localhost/my-site
```

а в конфігу вказано:
```nginx
location /my-site/ {
    alias /var/www/my-static-site/;
}
```

Nginx не вважає /my-site і /my-site/ одним і тим самим шляхом.
<br>Тобто:
- `/my-site` → НЕ підпадає під location /my-site/
- `/my-site/` → підпадає, і тоді все працює.

Тому без спеціального блоку /my-site — користувач отримає 404 (саме це ти бачив у логах раніше).

Рішення №1 — зовнішній редірект (з return 302)
```nginx
location = /my-site {
    return 302 /my-site/;
}
```
Як працює:
- Коли запитують `/my-site`, сервер каже:
- -> "йди на /my-site/"
(повертає HTTP-код `302 Found` або `301 Moved Permanently`)
Браузер бачить це і робить новий запит на `/my-site/`.


Рішення №2 — внутрішній редірект (з rewrite)
```nginx
location = /my-site {
    rewrite ^ /my-site/ last;
}
```

Як працює:
- Коли запитують `/my-site`, Nginx всередині себе переписує шлях на `/my-site/`.
- Клієнт (браузер, curl, API) не бачить редіректу — отримує одразу 200 OK.

---
- Якщо це *публічний сайт / документація / SPA* — краще `return 301` або `302`, бо браузери і пошуковики так очікують.
- Якщо це *внутрішній веб-сервіс* або *технічний роут*, і ти хочеш мінімум "шуму" — використовуй `rewrite ... last`.


---

### Додаткові можливості
- Налаштування кешування статичних файлів.
- Використання gzip стиснення для швидшої передачі.
- Налаштування HTTPS з SSL сертифікатами.
- Використання virtual hosts для кількох сайтів на одному сервері.
- Обмеження доступу за IP або іншими параметрами.
- Налаштування логування для аналізу трафіку.
- Використання CDN для роздачі статичних файлів.
- Інтеграція з системами моніторингу (Prometheus, Grafana).
