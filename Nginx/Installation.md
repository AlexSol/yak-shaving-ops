## Installation and Setting Up Nginx
1. **Встановлення Nginx**:
   - На Ubuntu/Debian:
     ```sh
     sudo apt update
     sudo apt install nginx
     ```

2. Перевірка встановлення:
   - отримати версію Nginx в терміналі:
    ```sh
    nginx -v
    ```
    віддповідь має бути приблизно такою:
    ```sh
    nginx version: nginx/1.18.0 (Ubuntu)
   ```
    - або відкрити в браузері `http://localhost/` і побачити сторінку "Welcome to nginx!".
    - або відкрити в браузері `http://<your-server-ip>/` і побачити сторінку "Welcome to nginx!".

2. **Запуск і перевірка статусу**:
   ```sh
   sudo systemctl start nginx
   sudo systemctl status nginx
   ```
   відповідь має бути приблизно такою:
   ```
    ● nginx.service - A high performance web server and a reverse proxy server
        Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
        Active: active (running) since Sun 2025-10-05 19:01:34 EEST; 33min ago
        Docs: man:nginx(8)
    Main PID: 12357 (nginx)
        Tasks: 17 (limit: 9026)
        Memory: 15.0M
            CPU: 80ms
    CGroup: /system.slice/nginx.service
             ├─12357 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
   ```
