# SSH

## Що таке SSH
SSH (Secure Shell) — це протокол мережевого рівня, який забезпечує безпечний спосіб доступу до віддаленого комп'ютера через незахищену мережу. Він використовує криптографію для захисту даних, що передаються між клієнтом і сервером, забезпечуючи конфіденційність та цілісність інформації.

## Як працює SSH
SSH працює за моделлю клієнт-сервер. Клієнт ініціює з'єднання з сервером, після чого відбувається аутентифікація користувача. Після успішної аутентифікації встановлюється зашифрований канал зв'язку, через який можна безпечно передавати команди та дані.

## Step by Step інструкція по налаштуванню SSH
1. Встановіть SSH-клієнт (якщо він ще не встановлений).
    - На Ubuntu/Debian:
    ```sh
        sudo apt-get install openssh-client
    ```
    - На CentOS/RHEL:
    ```sh
        sudo yum install openssh-clients
    ```
2. Встановіть SSH-сервер на віддаленій машині (якщо він ще не встановлений).
    - На Ubuntu/Debian: `sudo apt-get install openssh-server`
    - На CentOS/RHEL: `sudo yum install openssh-server`
3. Запустіть SSH-сервер (якщо він ще не запущений).
    - `sudo systemctl start ssh` (Ubuntu/Debian)
    - `sudo systemctl start sshd` (CentOS/RHEL)
4. Перевірте статус SSH-сервера.
    - `sudo systemctl status ssh` (Ubuntu/Debian)
    - `sudo systemctl status sshd` (CentOS/RHEL)
5. Згенеруйте SSH-ключі на клієнтській машині (якщо ще не згенеровані).
    - `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
6. Скопіюйте публічний ключ на віддалений сервер.
    - `ssh-copy-id user@remote_host`
7. Підключіться до віддаленого сервера.
    - `ssh user@remote_host`

## Практичне завдання

Завдання: Підключення до віддаленого сервера по SSH
Мета — перевірити доступ до сервера та впевнитися, що з’єднання працює коректно.

Вхідні дані:
Метод аутентифікації:
- Пароль
- SSH-ключ (наприклад, ~/.ssh/id_rsa)

Очікуваний результат:
- Успішне підключення до сервера.
- Можливість виконувати команди на віддаленій машині.


Links для вивчення:
- https://bytebytego.com/guides/how-does-ssh-work/
- https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server