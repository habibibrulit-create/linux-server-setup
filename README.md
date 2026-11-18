# linux-server-setup

## Описание
Настройка Ubuntu-сервера для размещения простого веб-приложения: отдельный пользователь, SSH без root-входа, firewall, nginx, тестовая страница, health-check и проверка логов.

## Среда
- Где выполнялся проект: Ubuntu VM в VMware на Windows
- ОС: Ubuntu 26.04.1 LTS
- IP сервера: 192.168.254.132 (приватный, учебный адрес в локальной сети)

## Что настроено
- Пользователь `deploy` с домашней директорией и правами sudo
- SSH-доступ под `deploy`, root-login отключён (`PermitRootLogin no`)
- Firewall ufw: открыты только порты 22, 80, 443
- Nginx установлен, запущен и включён в автозагрузку (systemd)
- Тестовая страница в `/var/www/kai-app`
- Endpoint `/health` (возвращает `OK`, HTTP 200)
- Просмотр логов nginx (`access.log`, `error.log`)

## Как проверить
    ssh deploy@192.168.254.132
    sudo ufw status verbose
    sudo systemctl status nginx
    systemctl is-enabled nginx
    curl http://192.168.254.132
    curl http://192.168.254.132/health
    sudo tail -n 20 /var/log/nginx/access.log
    sudo tail -n 20 /var/log/nginx/error.log

Ожидаемый результат: вход под `deploy` успешен, вход под root запрещён, firewall активен, nginx `active (running)` и `enabled`, главная страница показывает `KAI DevOps App is running`, `/health` отвечает `OK`.

## Проблемы и решения
- Опечатка при добавлении в группу sudo: `groups deploy` не показал `sudo`; повторил команду `sudo usermod -aG sudo deploy`.
- В `sshd_config` убрал `#`, но оставил значение `prohibit-password`; исправил на `PermitRootLogin no` и проверил `sudo sshd -t`.
- Опечатка в имени пакета (`ngnix` вместо `nginx`); установил правильный пакет.
- В конфиге nginx вместо `index index.html;` была строка `index.html;`; исправил, `nginx -t` прошёл успешно.
- Стандартный сайт `default` перехватывал запросы; удалил ссылку из `sites-enabled`.
- Адрес выдан по DHCP, поэтому перед проверкой сверяю его через `ip a`.

## Скриншоты
Папка `screenshots/`:
- `ssh_deploy_login.png`: вход под deploy
- `ufw_status.png`: статус firewall
- `nginx_status.png`: статус nginx
- `website_running.png`: главная страница
- `health_check.png`: проверка /health
- `nginx_logs.png`: логи nginx